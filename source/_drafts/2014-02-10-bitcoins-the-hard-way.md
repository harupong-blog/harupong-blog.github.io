 <span>Saturday, February 1, 2014</span>


# [Bitcoins the hard way: Using the raw Bitcoin protocol](http://www.righto.com/2014/02/bitcoins-hard-way-using-raw-bitcoin.html)

All the recent media attention on Bitcoin inspired me to learn how Bitcoin really works, right down to the bytes flowing through the network.
Normal people use software[[1]](#ref1) that hides what is really going on, but I wanted to get a hands-on understanding of the Bitcoin protocol.
My goal was to use the Bitcoin system directly: create a Bitcoin transaction manually, feed it into the system as hex data, and see how it gets processed.
This turned out to be considerably harder than I expected, but I learned a lot in the process and hopefully you will find it interesting.

This blog post starts with a quick overview of Bitcoin and then jumps into the low-level details: creating a Bitcoin address, making a transaction, signing the transaction, feeding the transaction into the peer-to-peer network, and observing the results.

## A quick overview of Bitcoin

I'll start with a quick overview of how Bitcoin works[[2]](#ref2),
before diving into the details.
Bitcoin is a relatively new digital currency[[3]](#ref3) that can be transmitted across the Internet. You can buy bitcoins[[4]](#ref4) with dollars or other traditional money from sites such as [Coinbase](http://coinbase.com) or [MtGox](http://mtgox.com)[[5]](#ref5), send bitcoins to other people, buy things with them at [some](http://www.shopify.com/blog/10480345-75-places-to-spend-your-bitcoins)&nbsp;[places](https://www.spendbitcoins.com/places/), and exchange bitcoins back into dollars.
<p>
To simplify slightly, bitcoins consist of entries in a distributed database that keeps track of the ownership of bitcoins.
Unlike a bank, bitcoins are not tied to users or accounts. Instead bitcoins are owned by a Bitcoin _address_, for example `1KKKK6N21XKo48zWKuQKXdvSsCf95ibHFa`.

### Bitcoin transactions

A _transaction_ is the mechanism for spending bitcoins. In a transaction, the owner of some bitcoins transfers ownership to a new address.
<p>
A key innovation of Bitcoin is how transactions are recorded in the distributed database through _mining_.
Transactions are grouped into blocks and about every 10 minutes a new block of transactions is sent out, becoming part of the transaction log known as the _blockchain_, which indicates the transaction has been made (more-or-less) official.[[6]](#ref6)
Bitcoin mining is the process that puts transactions into a block, to make sure everyone has a consistent view of the transaction log.
To mine a block, miners must find an extremely rare solution to an (otherwise-pointless) cryptographic problem. Finding this solution generates a mined block, which becomes part of the official block chain.
<p>
Mining is also the mechanism for new bitcoins to enter the system.
When a block is successfully mined, new bitcoins are generated in the block and paid to the miner. This mining bounty is large - currently 25 bitcoins per block (about $19,000). In addition, the miner gets any fees associated with the transactions in the block. Because of this, mining is very competitive with many people attempting to mine blocks.
The difficulty and competitiveness of mining is a key part of Bitcoin security, since it ensures that nobody can flood the system with bad blocks.

### The peer-to-peer network

There is no centralized Bitcoin server. Instead, Bitcoin runs on a peer-to-peer network. If you run a Bitcoin client, you become part of that network. The nodes on the network exchange transactions, blocks, and addresses of other peers with each other.
When you first connect to the network, your client downloads the blockchain from some random node or nodes. In turn, your client may provide data to other nodes. When you create a Bitcoin transaction, you send it to some peer, who sends it to other peers, and so on, until it reaches the entire network. Miners pick up your transaction, generate a mined block containing your transaction, and send this mined block to peers. Eventually your client will receive the block and your client shows that the transaction was processed.

### Cryptography

Bitcoin uses digital signatures to ensure that only the owner of bitcoins can spend them. The owner of a Bitcoin address has the private key associated with the address. To spend bitcoins, they sign the transaction with this private key, which proves they are the owner. (It's somewhat like signing a physical check to make it valid.) A public key is associated with each Bitcoin address, and anyone can use it to verify the digital signature.
<p>
Blocks and transactions are identified by a 256-bit cryptographic hash of their contents. This hash value is used in multiple places in the Bitcoin protocol. In addition, finding a special hash is the difficult task in mining a block.
<p>
[![Bitcoin statistic coin ANTANA](http://farm8.staticflickr.com/7429/10867417104_6595b54daa_n.jpg)](http://www.flickr.com/photos/105644709@N08/10867417104/ "Bitcoin statistic coin ANTANA by antanacoins, on Flickr")

<div class="cite">
Bitcoins do not really look like this.
Photo credit: [Antana](http://www.flickr.com/photos/105644709@N08/), [CC:by-sa](http://creativecommons.org/licenses/by-sa/2.0/deed.en)
</div>

## Diving into the raw Bitcoin protocol

The remainder of this article discusses, step by step, how I used the raw Bitcoin protocol.
First I generated a Bitcoin address and keys. Next I made a transaction to move a small amount of bitcoins to this address. Signing this transaction took me a lot of time and difficulty. Finally, I fed this transaction into the Bitcoin peer-to-peer network and waited for it to get mined.
The remainder of this article describes these steps in detail.

It turns out that actually using the Bitcoin protocol is harder than I expected. As you will see, the protocol is a bit of a jumble: it uses big-endian numbers, little-endian numbers, fixed-length numbers, variable-length numbers, custom encodings, [DER encoding](http://en.wikipedia.org/wiki/Distinguished_Encoding_Rules#DER_encoding), and a variety of cryptographic algorithms, seemingly arbitrarily. As a result, there's a lot of annoying manipulation to get data into the right format.[[7]](#ref7)
<p>
The second complication with using the protocol directly is that being cryptographic, it is very unforgiving. If you get one byte wrong, the transaction is rejected with no clue as to where the problem is.[[8]](#ref8)
<p>
The final difficulty I encountered is that the process of signing a transaction is much more difficult than necessary, with a lot of details that need to be correct. In particular, the version of a transaction that gets signed is very different from the version that actually gets used.

## Bitcoin addresses and keys

My first step was to create a Bitcoin address. Normally you use Bitcoin client software to create an address and the associated keys. However, I wrote some Python code to create the address, showing exactly what goes on behind the scenes.
<p>
Bitcoin uses a variety of keys and addresses, so the following diagram may help explain them. You start by creating a random 256-bit private key. The private key is needed to sign a transaction and thus transfer (spend) bitcoins. Thus, the private key must be kept secret or else your bitcoins can be stolen.
<p>
The Elliptic Curve DSA algorithm generates a 512-bit public key from the private key. (Elliptic curve cryptography will be discussed later.) This public key is used to verify the signature on a transaction. Inconveniently, the Bitcoin protocol adds a [prefix of 04](https://en.bitcoin.it/wiki/Protocol_specification#Signatures) to the public key. The public key is not revealed until a transaction is signed, unlike most systems where the public key is made public.
<p>
[![How bitcoin keys and addresses are related](https://lh4.googleusercontent.com/-p8yVJXqY7fg/UuLaPjMDtyI/AAAAAAAAWYQ/QoenRIBO1O4/s588/bitcoinkeys.png "How bitcoin keys and addresses are related")
](https://picasaweb.google.com/lh/photo/PWNDstAMH7mIdYfxE221HqNTQ8Yf_fOdBXli6Rl3Pxs)
<p>
<div class="cite">
How bitcoin keys and addresses are related
</div>
<p>
The next step is to generate the Bitcoin address that is shared with others.
Since the 512-bit public key is inconveniently large, it is hashed down to 160 bits using the SHA-256 and RIPEM hash algorithms.[[9]](#ref9) The key is then encoded in ASCII using Bitcoin's custom Base58Check encoding.[[10]](#ref10) The resulting address, such as `1KKKK6N21XKo48zWKuQKXdvSsCf95ibHFa`, is the address people publish in order to receive bitcoins. Note that you cannot determine the public key or the private key from the address. If you lose your private key (for instance by [throwing out your hard drive](http://www.usatoday.com/story/news/world/2013/11/28/newser-bitcoin-landfill/3775271/)), your bitcoins are lost forever.
<p>
Finally, the [Wallet Interchange Format](https://en.bitcoin.it/wiki/WIF) key (WIF) is used to add a private key to your client wallet software. This is simply a Base58Check encoding of the private key into ASCII, which is easily reversed to obtain the 256-bit private key. (I was curious if anyone would use the private key above to steal my 80 cents of bitcoins, and sure enough [someone did](http://blockexplorer.com/tx/c92ad3cb375aca80e8b2b740f24130a52d6fdfb24b3effa5b3f97abb99a84393#i72459399).)
<p>
To summarize, there are three types of keys: the private key, the public key, and the hash of the public key, and they are represented externally in ASCII using Base58Check encoding. The private key is the important key, since it is required to access the bitcoins and the other keys can be generated from it. The public key hash is the Bitcoin address you see published.
<p>
I used the following code snippet[[11]](#ref11) to generate a private key in WIF format and an address. The private key is simply a random 256-bit number. The ECDSA crypto library generates the public key from the private key.[[12]](#ref12) The Bitcoin address is generated by SHA-256 hashing, RIPEM-160 hashing, and then Base58 encoding with checksum. Finally, the private key is encoded in Base58Check to generate the WIF encoding used to enter a private key into Bitcoin client software.[[1]](#ref1) Note: this Python random function is not cryptographically strong; use a better function if you're doing this for real.
<p>
<script src="https://gist.github.com/shirriff/8e6b5650361bbe78a055.js"></script>

## Inside a transaction

A _transaction_ is the basic operation in the Bitcoin system.
You might expect that a transaction simply moves some bitcoins from one address to another address, but it's more complicated than that.
A Bitcoin transaction moves bitcoins between one or more _inputs_ and _outputs_. Each input is a transaction and address supplying bitcoins. Each output is an address receiving bitcoin, along with the amount of bitcoins going to that address.
<p>
[![A sample Bitcoin transaction. Transaction C spends .008 bitcoins from Transactions A and B.](https://lh4.googleusercontent.com/-FX_lwaangsI/UuNVjoFa4jI/AAAAAAAAWZU/NMeJZDHe6EA/s800/transaction-diagram.png "A sample Bitcoin transaction. Transaction C spends .008 bitcoins from Transactions A and B.")
](https://picasaweb.google.com/lh/photo/KzugE1Dj3S_YNt0hrmp8PKNTQ8Yf_fOdBXli6Rl3Pxs)
<p>
<div class="cite">
A sample Bitcoin transaction. Transaction C spends .008 bitcoins from Transactions A and B.
</div>
<p>
The diagram above shows a sample transaction "C". In this transaction, .005 BTC are taken from an address in Transaction A, and .003 BTC are taken from an address in Transaction B. (Note that arrows are references to the previous outputs, so are backwards to the flow of bitcoins.) For the outputs, .003 BTC are directed to the first address and .004 BTC are directed to the second address. The leftover .001 BTC goes to the miner of the block as a fee. Note that the .015 BTC in the other output of Transaction A is not spent in this transaction.
<p>
Each input used must be entirely spent in a transaction. If an address received 100 bitcoins in a transaction and you just want to spend 1 bitcoin, the transaction must spend all 100. The solution is to use a second output for _change_, which returns the 99 leftover bitcoins back to you.
<p>
Transactions can also include _fees_. If there are any bitcoins left over after adding up the inputs and subtracting the outputs, the remainder is a fee paid to the miner.
The fee isn't strictly required, but transactions without a fee will be a low priority for miners and may not be processed for days or may be discarded entirely.[[13]](#ref13)
A typical fee for a transaction is 0.0002 bitcoins (about 20 cents), so fees are low but not trivial.

## Manually creating a transaction

For my experiment I used a simple transaction with one input and one output, which is shown below.
I started by bying bitcoins from [Coinbase](http://coinbase.com) and putting 0.00101234 bitcoins into address `1MMMMSUb1piy2ufrSguNUdFmAcvqrQF8M5`, which was transaction [`81b4c832...`](https://blockchain.info/tx/81b4c832d70cb56ff957589752eb4125a4cab78a25a8fc52d6a09e5bd4404d48). My goal was to create a transaction to transfer these bitcoins to the address I created above,
`1KKKK6N21XKo48zWKuQKXdvSsCf95ibHFa`, subtracting a fee of 0.0001 bitcoins. Thus, the destination address will receive 0.00091234 bitcoins.
<p>
[![Structure of the example Bitcoin transaction.](https://lh5.googleusercontent.com/-DzIHjsd2qEM/UuMH-iJPYJI/AAAAAAAAWYk/YRLW-55mfpY/s400/transaction-diagram-new.png "Structure of the example Bitcoin transaction.")
](https://picasaweb.google.com/lh/photo/VQdIQNIEiihzeRjFO1s1KKNTQ8Yf_fOdBXli6Rl3Pxs)
<p>
<div class="cite">
Structure of the example Bitcoin transaction.
</div>
<p>
Following the [specification](https://en.bitcoin.it/wiki/Protocol_specification#tx), the unsigned transaction can be assembled fairly easily, as shown below. There is one input, which is using output 0 (the first output) from transaction `81b4c832...`. Note that this transaction hash is inconveniently reversed in the transaction. The output amount is 0.00091234 bitcoins (91234 is 0x016462 in hex), which is stored in the value field in little-endian form. The cryptographic parts - _scriptSig_ and _scriptPubKey_ - are more complex and will be discussed later.
<p>
<table class="tx">
<tr><td colspan=2>version</td><td>01 00 00 00</td></tr>
<tr><td colspan=2>input count</td><td>01</td></tr>
<tr><td rowspan=5>input</td><td class="txindent">previous output hash
(reversed)</td><td>48 4d 40 d4 5b 9e a0 d6 52 fc a8 25 8a b7 ca a4 25 41 eb 52 97 58 57 f9 6f b5 0c d7 32 c8 b4 81</td></tr>
<tr><td class="txindent">previous output index</td><td>00 00 00 00</td></tr>
<tr><td class="txindent">script length</td><td></td></tr>
<tr><td class="txindent">scriptSig</td><td>script containing signature</td></tr>
<tr><td class="txindent">sequence</td><td>ff ff ff ff</td></tr>
<tr><td colspan=2>output count</td><td>01</td></tr>
<tr><td rowspan=3>output</td><td class="txindent">value</td><td>62 64 01 00 00 00 00 00</td></tr>
<tr><td class="txindent">script length</td><td></td></tr>
<tr><td class="txindent">scriptPubKey</td><td>script containing destination address</td></tr>
<tr><td colspan=2>block lock time</td><td>00 00 00 00</td></tr>
</table>
<p>
Here's the code I used to generate this unsigned transaction. It's just a matter of [packing](http://docs.python.org/2/library/struct.html) the data into binary. Signing the transaction is the hard part, as you'll see next.
<script src="https://gist.github.com/shirriff/6f7ca9aadf0db855b383.js"></script>

## How Bitcoin transactions are signed

The following diagram gives a simplified view of how transactions are signed and linked together.[[14]](#ref14) Consider the middle transaction, transferring bitcoins from address B to address C. The contents of the transaction (including the hash of the previous transaction) are hashed and signed with B's private key. In addition, B's public key is included in the transaction.
<p>
By performing several steps, anyone can verify that the transaction is authorized by B. First, B's public key must correspond to B's address in the previous transaction, proving the public key is valid. (The address can easily be derived from the public key, as explained earlier.) Next, B's signature of the transaction can be verified using the B's public key in the transaction. These steps ensure that the transaction is valid and authorized by B. One unexpected part of Bitcoin is that B's public key isn't made public until it is used in a transaction.
<p>
With this system, bitcoins are passed from address to address through a chain of transactions. Each step in the chain can be verified to ensure that bitcoins are being spent validly. Note that transactions can have multiple inputs and outputs in general, so the chain branches out into a tree.
<p>
[![How Bitcoin transactions are chained together.](https://lh4.googleusercontent.com/-bweWuuZS2Ws/UuVm2RjRCyI/AAAAAAAAWZ4/IV2rPvunYLQ/s512/bitcoin-transaction-chain.png "How Bitcoin transactions are chained together.")
](https://picasaweb.google.com/lh/photo/1EyCN5GPklKAFHGZs8TT2aNTQ8Yf_fOdBXli6Rl3Pxs)
<p>
<div class="cite">
How Bitcoin transactions are chained together.[[14]](#ref14)
</div>

## The Bitcoin scripting language

You might expect that a Bitcoin transaction is signed simply by including the signature in the transaction, but the process is much more complicated.
In fact, there is a small program inside each transaction that gets executed to decide if a transaction is valid.
This program is written in _Script_, the [stack-based](http://en.wikipedia.org/wiki/Stack-based) Bitcoin scripting language. Complex redemption conditions can be expressed in this language. For instance, an escrow system can require two out of three specific users must sign the transaction to spend it. Or various types of [contracts](https://en.bitcoin.it/wiki/Contracts) can be set up.[[15]](#ref15)
<p>
The Script language is surprisingly complex, with about [80 different opcodes](https://en.bitcoin.it/wiki/Script). It includes arithmetic, bitwise operations, string operations, conditionals, and stack manipulation. The language also includes the necessary cryptographic operations (SHA-256, RIPEM, etc.) as primitives. In order to ensure that scripts terminate, the language does not contain any looping operations. (As a consequence, it is not Turing-complete.) In practice, however, only a few types of transactions are supported.[[16]](#ref16)
<p>
In order for a Bitcoin transaction to be valid, the two parts of the redemption script must run successfully.
The script in the old transaction is called _scriptPubKey_ and the script in the new transaction is called _scriptSig_.
To verify a transaction, the scriptSig executed followed by the scriptPubKey.
If the script completes successfully, the transaction is valid and the Bitcoin can be spent. Otherwise, the transaction is invalid. The point of this is that the scriptPubKey in the old transaction defines the conditions for spending the bitcoins. The scriptSig in the new transaction must provide the data to satisfy the conditions.
<p>
In a standard transaction, the scriptSig pushes the signature (generated from the private key) to the stack, followed by the public key. Next, the scriptPubKey (from the source transaction) is executed to verify the public key and then verify the signature.
<p>
As expressed in Script, the scriptSig is:
<pre>
PUSHDATA
signature data and SIGHASH_ALL
PUSHDATA
public key data
</pre>
The scriptPubKey is:
<pre>
OP_DUP
OP_HASH160
PUSHDATA
Bitcoin address (public key hash)
OP_EQUALVERIFY
OP_CHECKSIG
</pre>
<p>
When this code executes, PUSHDATA first pushes the signature to the stack. The next PUSHDATA pushes the public key to the stack. Next, OP_DUP duplicates the public key on the stack. OP_HASH160 computes the 160-bit hash of the public key. PUSHDATA pushes the required Bitcoin address. Then OP_EQUALVERIFY verifies the top two stack values are equal - that the public key hash from the new transaction matches the address in the old address. This proves that the public key is valid. Next, OP_CHECKSIG checks that the signature of the transaction matches the public key and signature on the stack. This proves that the signature is valid.

## Signing the transaction

I found signing the transaction to be the hardest part of using Bitcoin manually, with a process that is surprisingly difficult and error-prone.
The basic idea is to use the ECDSA elliptic curve algorithm and the private key to generate a digital signature of the transaction, but the details are tricky.
The signing process has been described through a
[19-step process](http://bitcoin.stackexchange.com/questions/3374/how-to-redeem-a-basic-tx) ([more info](https://en.bitcoin.it/wiki/OP_CHECKSIG)).
Click the [thumbnail below](https://en.bitcoin.it/w/images/en/7/70/Bitcoin_OpCheckSig_InDetail.png) for a detailed diagram of the process.
<p>
[
 ![](https://lh4.googleusercontent.com/-anHnnkgfQw8/Ut4QeV-Q9yI/AAAAAAAAWXA/RvR5tOvR4tk/s800/signing-thumb.png)](https://en.bitcoin.it/w/images/en/7/70/Bitcoin_OpCheckSig_InDetail.png)
<p>
The biggest complication is the signature appears in the middle of the transaction, which raises the question of how to sign the transaction before you have the signature.
To avoid this problem,
the _scriptPubKey_ script is copied from the source transaction into the spending transaction (i.e. the transaction that is being signed) before computing the signature. Then the signature is turned into code in the Script language, creating the _scriptSig_ script that is embedded in the transaction. It appears that using the previous transaction's _scriptPubKey_ during signing is for historical reasons rather than any logical reason.[[17]](#ref17)
For transactions with multiple inputs, signing is even more complicated since each input requires a separate signature, but I won't go into the details.
<p>
One step that tripped me up is the _hash type_.
Before signing, the transaction has a hash type constant temporarily appended. For a regular transaction, this is [SIGHASH_ALL](https://en.bitcoin.it/wiki/OP_CHECKSIG#How_it_works) (0x00000001). After signing, this hash type is removed from the end of the transaction and appended to the scriptSig.
<p>
Another annoying thing about the Bitcoin protocol is that the signature and public key are both 512-bit elliptic curve values, but they are represented in totally different ways: the signature is encoded with [DER](http://en.wikipedia.org/wiki/X.690#DER_encoding) encoding but the public key is represented as plain bytes. In addition, both values have an extra byte, but positioned inconsistently: SIGHASH_ALL is put after the signature, and type 04 is put before the public key.
<p>
Debugging the signature was made more difficult because the ECDSA algorithm uses a random number.[[18]](#ref18) Thus, the signature is different every time you compute it, so it can't be compared with a known-good signature.
<p>
With these complications it took me a long time to get the signature to work.
Eventually, though, I got all the bugs out of my signing code and succesfully signed a transaction. Here's the code snippet I used.
<p>
<script src="https://gist.github.com/shirriff/b00515b8583064487ff3.js"></script>
<p>
The final scriptSig contains the signature along with the public key for the source address (`1MMMMSUb1piy2ufrSguNUdFmAcvqrQF8M5`). This proves I am allowed to spend these bitcoins, making the transaction valid.
<p>
<table class="tx">
  <tr><td colspan=2>PUSHDATA 47</td><td>47</td></tr>
  <tr><td rowspan=8>signature
 (DER)</td>
  <td>sequence</td><td>30</td></tr>
  <tr><td class="txindent">length</td><td>44</td></tr>
  <tr><td class="txindent">integer</td><td>02</td></tr>
  <tr><td class="txindent2">length</td><td>20</td></tr>
  <tr><td class="txindent2">X</td><td>2c b2 65 bf 10 70 7b f4 93 46 c3 51 5d d3 d1 6f c4 54 61 8c 58 ec 0a 0f f4 48 a6 76 c5 4f f7 13</td></tr>
  <tr><td class="txindent">integer</td><td>02</td></tr>
<tr><td class="txindent2">length</td><td>20</td></tr>
<tr><td class="txindent2">Y</td><td>
    6c 66 24 d7 62 a1 fc ef 46 18 28 4e ad 8f 08 67 8a c0 5b 13 c8 42 35 f1 65 4e 6a d1 68 23 3e 82</td></tr>
<tr><td colspan=2 class="txindent">SIGHASH_ALL</td><td>01</td></tr>
  <tr><td colspan=2>PUSHDATA 41</td><td>41</td></tr>
  <tr><td rowspan=5>public key</td>
   <td>type</td><td>04</td></tr>
  <tr><td>X</td><td>14 e3 01 b2 32 8f 17 44 2c 0b 83 10 d7 87 bf 3d 8a 40 4c fb d0 70 4f 13 5b 6a d4 b2 d3 ee 75 13</td></tr>
  <tr><td>Y</td><td> 10 f9 81 92 6e 53 a6 e8 c3 9b d7 d3 fe fd 57 6c 54 3c ce 49 3c ba c0 63 88 f2 65 1d 1a ac bf cd</td></tr>
</table>
<p>
The final scriptPubKey contains the script that must succeed to spend the bitcoins. Note that this script is executed at some arbitrary time in the future when the bitcoins are spent.
It contains the destination address
(`1KKKK6N21XKo48zWKuQKXdvSsCf95ibHFa`) expressed in hex, not Base58Check. The effect is that only the owner of the private key for this address can spend the bitcoins, so that address is in effect the owner.
<table class="tx">
  <tr><td>OP_DUP</td><td>76</td></tr>
  <tr><td>OP_HASH160</td><td>a9</td></tr>
  <tr><td>PUSHDATA 14</td><td>14</td></tr>
  <tr><td class="txindent">public key hash</td><td>c8 e9 09 96 c7 c6 08 0e e0 62 84 60 0c 68 4e d9 04 d1 4c 5c</td>
    <tr><td>OP_EQUALVERIFY</td><td>88</tr>
    <tr><td>OP_CHECKSIG</td></td><td>ac</td></tr>
</table>

## The final transaction

Once all the necessary methods are in place, the final transaction can be assembled.
<script src="https://gist.github.com/shirriff/64330be02c0aadddf2c3.js"></script>
The final transaction is shown below. This combines the scriptSig and scriptPubKey above with the unsigned transaction described earlier.
<p>
<table class="tx">
<tr><td colspan=2>version</td><td>01 00 00 00</td></tr>
<tr><td colspan=2>input count</td><td>01</td></tr>
<tr><td rowspan=5>input</td><td class="txindent">previous output hash
(reversed)</td><td>48 4d 40 d4 5b 9e a0 d6 52 fc a8 25 8a b7 ca a4 25 41 eb 52 97 58 57 f9 6f b5 0c d7 32 c8 b4 81</td></tr>
<tr><td class="txindent">previous output index</td><td>00 00 00 00</td></tr>
<tr><td class="txindent">script length</td><td>8a</td></tr>
<tr><td class="txindent">scriptSig</td><td>47 30 44 02 20 2c b2 65 bf 10 70 7b f4 93 46 c3 51 5d d3 d1 6f c4 54 61 8c 58 ec 0a 0f f4 48 a6 76 c5 4f f7 13 02 20 6c 66 24 d7 62 a1 fc ef 46 18 28 4e ad 8f 08 67 8a c0 5b 13 c8 42 35 f1 65 4e 6a d1 68 23 3e 82 01 41 04 14 e3 01 b2 32 8f 17 44 2c 0b 83 10 d7 87 bf 3d 8a 40 4c fb d0 70 4f 13 5b 6a d4 b2 d3 ee 75 13 10 f9 81 92 6e 53 a6 e8 c3 9b d7 d3 fe fd 57 6c 54 3c ce 49 3c ba c0 63 88 f2 65 1d 1a ac bf cd</td></tr>
<tr><td class="txindent">sequence</td><td>ff ff ff ff</td></tr>
<tr><td colspan=2>output count</td><td>01</td></tr>
<tr><td rowspan=3>output</td><td class="txindent">value</td><td>62 64 01 00 00 00 00 00</td></tr>
<tr><td class="txindent">script length</td><td>19</td></tr>
<tr><td class="txindent">scriptPubKey</td><td>76 a9 14 c8 e9 09 96 c7 c6 08 0e e0 62 84 60 0c 68 4e d9 04 d1 4c 5c 88 ac</td></tr>
<tr><td colspan=2>block lock time</td><td>00 00 00 00</td></tr>
</table>
<p>

## A tangent: understanding elliptic curves

Bitcoin uses elliptic curves as part of the signing algorithm. I had heard about elliptic curves before in the context of solving Fermat's Last Theorem, so I was curious about what they are.
The mathematics of elliptic curves is interesting, so I'll take a detour and give a quick overview.
<p>
The name _elliptic curve_ is confusing: elliptic curves are not ellipses, do not look anything like ellipses, and they have very little to do with ellipses.
An elliptic curve is a curve satisfying the fairly simple equation _y^2 = x^3 + ax + b_.
Bitcoin uses a specific elliptic curve called [secp256k1](http://www.secg.org/collateral/sec2_final.pdf) with the simple equation _y^2=x^3+7_.
[[25]](#ref25)
<p>
[![Elliptic curve formula used by Bitcoin.](https://lh3.googleusercontent.com/-hYOTYOlOJ5U/Us-rlHxDwGI/AAAAAAAAVdU/Zfd6Fz74gKk/s300/elliptic-curve-small.png "Elliptic curve formula used by Bitcoin.")
](https://picasaweb.google.com/lh/photo/x3-0WmXfEV7hgp6AxLU5K6NTQ8Yf_fOdBXli6Rl3Pxs)
<p>
<div class="cite">
Elliptic curve formula used by Bitcoin.
</div>
<p>
An important property of elliptic curves is that you can define addition of points on the curve with a simple [rule](http://en.wikipedia.org/wiki/Elliptic_curve#The_group_law): if you draw a straight line through the curve and it hits three points A, B, and C, then addition is defined by A+B+C=0. Due to the special nature of elliptic curves, addition defined in this way works "normally" and forms a group. With addition defined, you can define integer multiplication: e.g. 4A = A+A+A+A.
<p>
What makes elliptic curves useful cryptographically is that it's fast to do integer multiplication, but division basically requires brute force. For example, you can compute a product such as 12345678*A = Q really quickly (by computing powers of 2), but if you only know A and Q solving n*A = Q is hard. In elliptic curve cryptography, the secret number 12345678 would be the private key and the point Q on the curve would be the public key.
<p>
In cryptography, instead of using real-valued points on the curve, the coordinates are integers modulo a prime.[[19]](#ref19) One of the surprising properties of elliptic curves is the math works pretty much the same whether you use real numbers or modulo arithmetic.
Because of this, Bitcoin's elliptic curve doesn't look like the picture above, but is a random-looking mess of 256-bit points (imagine a big gray square of points).
<p>
The Elliptic Curve Digital Signature Algorithm ([ECDSA](http://en.wikipedia.org/wiki/Elliptic_Curve_DSA)) takes a message hash, and then does some straightforward elliptic curve arithmetic using the message, the private key, and a random number[[18]](#ref18) to generate a new point on the curve that gives a signature. Anyone who has the public key, the message, and the signature can do some simple elliptic curve arithmetic to verify that the signature is valid. Thus, only the person with the private key can sign a message, but anyone with the public key can verify the message.
<p>
For more on elliptic curves, see the references[[20]](#ref20).

## Sending my transaction into the peer-to-peer network

Leaving elliptic curves behind, at this point I've created a transaction and signed it. The next step is to send it into the peer-to-peer network, where it will be picked up by miners and incorporated into a block.

### How to find peers

The first step in using the peer-to-peer network is finding a peer.
The list of peers changes every few seconds, whenever someone runs a client.
Once a node is connected to a peer node, they share new peers by exchanging _addr_ messages whenever a new peer is discovered. Thus, new peers rapidly spread through the system.
<p>
There's a chicken-and-egg problem, though, of how to find the first peer. Bitcoin clients solve this problem with several methods. Several reliable peers are registered in DNS under the name _bitseed.xf2.org_. By doing a nslookup, a client gets the IP addresses of these peers, and hopefully one of them will work.
If that doesn't work, a seed list of peers is hardcoded into the client.
[[26]](#ref26)
<p>
[![nslookup can be used to find Bitcoin peers.](https://lh3.googleusercontent.com/-S3C58sitiLQ/Us-rln_BqGI/AAAAAAAAVdc/Bsr2aVLYvbk/s400/nslookup.png "nslookup can be used to find Bitcoin peers.")
](https://picasaweb.google.com/lh/photo/Zw8y8RELLwKPd3f5nEk2P6NTQ8Yf_fOdBXli6Rl3Pxs)
<p>
<div class="cite">
nslookup can be used to find Bitcoin peers.
</div>
<p>
Peers enter and leave the network when ordinary users start and stop Bitcoin clients, so there is a lot of turnover in clients. The clients I use are unlikely to be operational right now, so you'll need to find new peers if you want to do experiments. You may need to try a bunch to find one that works.

### Talking to peers

Once I had the address of a working peer, the next step was to send my transaction into the peer-to-peer network.[[8]](#ref8)  Using the peer-to-peer protocol is pretty straightforward. I opened a TCP connection to an arbitrary peer on port 8333, started sending messages, and received messages in turn. The Bitcoin peer-to-peer protocol is pretty forgiving; peers would keep communicating even if I totally messed up requests.
<p>
Important note: as a few people pointed out, if you want to experiment you should use the Bitcoin [Testnet](https://en.bitcoin.it/wiki/Testnet), which lets you experiment with "fake" bitcoins, since it's easy to lose your valuable bitcoins if you mess up on the real network. (For example, if you forget the change address in a transaction, excess bitcoins will go to the miners as a fee.) But I figured I would use the real Bitcoin network and risk my $1.00 worth of bitcoins.
<p>
The protocol consists of about 24 different message types.
Each message is a fairly straightforward binary blob containing an ASCII command name and a binary payload appropriate to the command.
The protocol is well-documented on the [Bitcoin wiki](https://en.bitcoin.it/wiki/Protocol_specification).
<p>
The first step when connecting to a peer is to establish the connection by exchanging _version_ messages.
First I send a _[version](https://en.bitcoin.it/wiki/Protocol_specification#version)_ message with my protocol version number[[21]](#ref21), address, and a few other things. The peer sends its _version_ message back. After this, nodes are supposed to acknowledge the version message with a _[verack](https://en.bitcoin.it/wiki/Protocol_specification#verack)_ message. (As I mentioned, the protocol is forgiving - everything works fine even if I skip the verack.)
<p>
Generating the _version_ message isn't totally trivial since it has a bunch of fields, but it can be created with a few lines of Python. _makeMessage_ below builds an arbitrary peer-to-peer message from the magic number, command name, and payload. _getVersionMessage_ creates the payload for a _version_ message by packing together the various fields.
<script src="https://gist.github.com/shirriff/7e2b3cb0d7432175d719.js"></script>

### Sending a transaction: _tx_

I sent the transaction into the peer-to-peer network with the stripped-down Python script below. The script sends a _version_ message, receives (and ignores) the peer's _version_ and _verack_ messages, and then sends the transaction as a _tx_ message. The hex string is the transaction that I created earlier.
<script src="https://gist.github.com/shirriff/d0a553d74ccc4ff0a4c4.js"></script>
<p>
The following screenshot shows how sending my transaction appears in the Wireshark network analysis program[[22]](#ref22). I wrote Python scripts to process Bitcoin network traffic, but to keep things simple I'll just use Wireshark here. The "tx" message type is visible in the ASCII dump, followed on the next line by the start of my transaction (01 00 ...).
<p>
[![A transaction uploaded to Bitcoin, as seen in Wireshark.](https://lh4.googleusercontent.com/--LYqURgHjRQ/UuINg_1uEMI/AAAAAAAAWXs/UkWpMkKq45w/s700/bitcoin-wireshark-tx.png "A transaction uploaded to Bitcoin, as seen in Wireshark.")
](https://picasaweb.google.com/lh/photo/24C8JkBF2sCMBxeqZFYptaNTQ8Yf_fOdBXli6Rl3Pxs)
<p>
<div class="cite">
A transaction uploaded to Bitcoin, as seen in Wireshark.
</div>
<p>
To monitor the progress of my transaction, I had a socket opened to another random peer. Five seconds after sending my transaction, the other peer sent me a _tx_ message with the hash of the transaction I just sent. Thus, it took just a few seconds for my transaction to get passed around the peer-to-peer network, or at least part of it.

## Victory: my transaction is mined

After sending my transaction into the peer-to-peer network, I needed to wait for it to be mined before I could claim victory.
Ten minutes later my script received an _inv_ message with a new block (see Wireshark trace below).
[Checking this block](https://blockchain.info/block-index/456667) showed that it contained my transaction, proving my transaction worked.
I could also verify the success of this transaction by looking in my Bitcoin wallet and by checking online.
Thus, after a lot of effort, I had successfully created a transaction manually and had it accepted by the system. (Needless to say, my first few transaction attempts weren't successful - my faulty transactions vanished into the network, never to be seen again.[[8]](#ref8))
<p>
[![A new block in Bitcoin, as seen in Wireshark.](https://lh5.googleusercontent.com/-Ez7hyfuTNKY/UuINidlZelI/AAAAAAAAWX0/F1Y7WwMxhLA/s700/bitcoin-wireshark-inv.png "A new block in Bitcoin, as seen in Wireshark.")
](https://picasaweb.google.com/lh/photo/KLVWQ4CFFrqDb5yGRJ7nT6NTQ8Yf_fOdBXli6Rl3Pxs)
<p>
<div class="cite">
A new block in Bitcoin, as seen in Wireshark.
</div>
<p>
My transaction was mined by the large GHash.IO mining pool, into block
[#279068](https://blockchain.info/block-index/456667) with hash `[0000000000000001a27b1d6eb8c405410398ece796e742da3b3e35363c2219ee](https://coinbase.com/network/blocks/0000000000000001a27b1d6eb8c405410398ece796e742da3b3e35363c2219ee)`. (The hash is reversed in _inv_ message above: ee19...) Note that the hash starts with a large number of zeros - finding such a literally one in a quintillion value is what makes mining so difficult. This particular block contains 462 transactions, of which my transaction is just one.
<p>
For mining this block, the miners received the reward of 25 bitcoins, and total fees of 0.104 bitcoins, approximately $19,000 and $80 respectively. I paid a fee of 0.0001 bitcoins, approximately 8 cents or 10% of my transaction.
The mining process is very interesting, but I'll leave that for a future article.
<p>
[![Untitled](http://farm4.staticflickr.com/3780/9305154893_e1b7925728_n.jpg)](http://www.flickr.com/photos/gastev/9305154893/ "Untitled by Gastev, on Flickr")
<div class="cite">
Bitcoin mining normally uses special-purpose ASIC hardware, designed to compute hashes at high speed.
 Photo credit: [Gastev](http://www.flickr.com/photos/gastev/), [CC:by](http://creativecommons.org/licenses/by/2.0/deed.en)
</div>

## Conclusion

Using the raw Bitcoin protocol turned out to be harder than I expected, but I learned a lot about bitcoins along the way, and I hope you did too.
My full code is available on GitHub.[[23]](#ref23) My code is purely for demonstration -  if you actually want to use bitcoins through Python, use a real library[[24]](#ref24) rather than my code.

## Notes and references

<a name="ref1"></a>[1]
The original Bitcoin client is [Bitcoin-qt](http://bitcoin.org/en/download). In case you're wondering why _qt_, the client uses the common [Qt UI framework](http://en.wikipedia.org/wiki/Qt_(software)). Alternatively you can use wallet software that doesn't participate in the peer-to-peer network, such as [Electrum](https://electrum.org//) or [MultiBit](https://multibit.org/). Or you can use an online wallet such as [Blockchain.info](http://blockchain.info/wallet).
<p>
<a name="ref2"></a>[2]
A couple good articles on Bitcoin are [How it works](http://bitcoin.org/en/how-it-works) and the very thorough [How the Bitcoin protocol actually works](http://www.michaelnielsen.org/ddi/how-the-bitcoin-protocol-actually-works/).
<p>
<a name="ref3"></a>[3]
The original Bitcoin paper is [Bitcoin: A Peer-to-Peer Electronic Cash System](http://www.bitcoin.org/bitcoin.pdf) written by the pseudonymous Satoshi Nakamoto in 2008. The true identity of Satoshi Nakamoto is unknown, although there are many theories.
<p>
<a name="ref4"></a>[4]
You may have noticed that sometimes Bitcoin is capitalized and sometimes not. It's not a problem with my shift key - the ["official" style](https://en.bitcoin.it/wiki/Introduction#Capitalization_.2F_Nomenclature) is to capitalize _Bitcoin_ when referring to the system, and lower-case _bitcoins_ when referring to the currency units.
<p>
<a name="ref5"></a>[5]
In case you're wondering how the popular MtGox Bitcoin exchange got its name, it was originally a trading card exchange called "Magic: The Gathering Online Exchange" and later took the acronym as its name.
<p>
<a name="ref6"></a>[6]
For more information on what data is in the blockchain, see the very helpful article [Bitcoin, litecoin, dogecoin: How to explore the block chain](http://grantammons.me/bitcoin-litecoin-dogecoin-exploring-the-block-chain/?utm_source=hn&utm_medium=web&utm_campaign=hn).
<p>
<a name="ref7"></a>[7]
I'm not the only one who finds the Bitcoin transaction format inconvenient. For a rant on how messed up it is, see [Criticisms of Bitcoin's raw txn format](http://exiledbear.wordpress.com/2013/06/06/criticisms-of-bitcoins-raw-txn-format/).
<p>
<a name="ref8"></a>[8]
You can also generate transaction and send raw transactions into the Bitcoin network using the bitcoin-qt console. Type _sendrawtransaction a1b2c3d4..._. This has the advantage of providing information in the debug log if the transaction is rejected. If you just want to experiment with the Bitcoin network, this is much, much easier than my manual approach.
<p>
<a name="ref9"></a>[9]
Apparently there's no solid reason to use RIPEM-160 hashing to create the address and SHA-256 hashing elsewhere, beyond a vague sense that using a different hash algorithm helps security.
See [discussion](http://bitcoin.stackexchange.com/questions/9202/why-does-bitcoin-use-two-hash-functions-sha-256-and-ripemd-160-to-create-an-ad). Using one round of SHA-256 is subject to a [length extension attack](http://en.wikipedia.org/wiki/Length_extension_attack), which explains why double-hashing is used.
<p>
<a name="ref10"></a>[10]
The Base58Check algorithm is documented on the [Bitcoin wiki](https://en.bitcoin.it/wiki/Base58Check_encoding). It is similar to base 64 encoding, except it omits the O, 0, I, and l characters to avoid ambiguity in printed text. A 4-byte checksum guards against errors, since using an erroneous bitcoin address will cause the bitcoins to be lost forever.
<p>
<a name="ref11"></a>[11]
Some boilerplate has been removed from the code snippets. For the full Python code, see GitHub. You will also need the [ecdsa cryptography library](https://pypi.python.org/pypi/ecdsa/0.10).
<p>
<a name="ref12"></a>[12]
You may wonder how I ended up with addresses with nonrandom prefixes such as 1MMMM. The answer is brute force - I ran the address generation script overnight and collected some good addresses. (These addresses made it much easier to recognize my transactions in my testing.) There are [scripts](https://en.bitcoin.it/wiki/Vanitygen) and [websites](https://bitcoinvanity.appspot.com/) that will generate these "vanity" addresses for you.
<p>
<a name="ref13"></a>[13]
For a summary of Bitcoin fees, see [bitcoinfees.com](http://bitcoinfees.com/).
This recent [Reddit discussion of fees](http://www.reddit.com/r/Bitcoin/comments/1s4zdn/it_currently_costs_around_5_to_send_someone/?utm_medium=twitter&utm_source=Fancy+Show+Tech) is also interesting.
<p>
<a name="ref14"></a>[14]
The [original Bitcoin paper](http://www.bitcoin.org/bitcoin.pdf) has a similar figure showing how transactions are chained together. I find it very confusing though, since it doesn't distinguish between the address and the public key.
<p>
<a name="ref15"></a>[15]
For details on the different types of contracts that can be set up with Bitcoin, see [Contracts](https://en.bitcoin.it/wiki/Contracts). One interesting type is the [2-of-3](https://github.com/bitcoin/bips/blob/master/bip-0011.mediawiki) escrow transaction, where two out of three parties must sign the transaction to release the bitcoins. [Bitrated](https://www.bitrated.com/) is one site that provides these.
<p>
<a name="ref16"></a>[16]
Although Bitcoin's Script language is very flexible, the Bitcoin network only permits a few standard transaction types and [non-standard transactions](http://wiki.betcoin.tm/Nonstandard_block) are not propagated ([details](https://bitcointalk.org/index.php?topic=8924.0)). [Some miners](https://en.bitcoin.it/wiki/Free_transaction_relay_policy) will accept non-standard transactions directly, though.
<p>
<a name="ref17"></a>[17]
There isn't a security benefit from copying the scriptPubKey into the spending transaction before signing since the hash of the original transaction is included in the spending transaction. For discussion, see [Why TxPrev.PkScript is inserted into TxCopy during signature check?](https://bitcointalk.org/index.php?topic=102487.msg1123257#msg1123257)
<p>
<a name="ref18"></a>[18]
The random number used in the elliptic curve signature algorithm is critical to the security of signing. Sony used a constant instead of a random number in the PlayStation 3, allowing the private key to be [determined](http://www.edn.com/design/consumer/4368066/The-Sony-PlayStation-3-hack-deciphered-what-consumer-electronics-designers-can-learn-from-the-failure-to-protect-a-billion-dollar-product-ecosystem). In an incident related to Bitcoin, [a weakness in the random number generator](http://www.theregister.co.uk/2013/08/12/android_bug_batters_bitcoin_wallets/) allowed bitcoins to be stolen from Android clients.
<p>
<a name="ref19"></a>[19]
For Bitcoin, the coordinates on the elliptic curve are integers modulo the [prime](http://www.wolframalpha.com/input/?i=is+2%5E256+-+2%5E32+-+2%5E9+-2%5E8+-+2%5E7+-+2%5E6+-2%5E4+-1+prime)_2^256 - 2^32 - 2^9 -2^8 - 2^7 - 2^6 -2^4 -1_, which is very nearly 2^256. This is why the keys in Bitcoin are 256-bit keys.
<p>
<a name="ref20"></a>[20]
For information on the historical connection between elliptic curves and ellipses (the equation turns up when integrating to compute the arc length of an ellipse) see the interesting article [Why Ellipses Are Not Elliptic Curves](http://www.maa.org/sites/default/files/pdf/upload_library/2/Rice-2013.pdf), Adrian Rice and Ezra Brown, _Mathematics Magazine_, vol. 85, 2012, pp. 163-176.
For more introductory information on elliptic curve cryptography, see [ECC tutorial](http://www.certicom.com/index.php/ecc-tutorial) or [A (Relatively Easy To Understand) Primer on Elliptic Curve Cryptography](http://blog.cloudflare.com/a-relatively-easy-to-understand-primer-on-elliptic-curve-cryptography).
For more on the mathematics of elliptic curves, see
[An Introduction to the
Theory of Elliptic Curves](http://www.math.brown.edu/~jhs/Presentations/WyomingEllipticCurve.pdf) by Joseph H. Silverman.
[Three Fermat trails to elliptic curves](http://www.math.vt.edu/people/brown/doc/ellip.pdf) includes a discussion of how Fermat's Last Theorem was solved with elliptic curves.
<p>
<a name="ref21"></a>[21]
There doesn't seem to be [documentation](http://bitcoin.stackexchange.com/questions/13537/how-do-i-find-out-what-the-latest-protocol-version-is) on the different Bitcoin protocol versions other than the [code](https://github.com/bitcoin/bitcoin/blob/v0.8.5/src/version.h#L28). I'm using version 60002 somewhat arbitrarily.
<p>
<a name="ref22"></a>[22]
The Wireshark network analysis software can dump out most types of Bitcoin packets, but only if you download a recent ["beta release](http://samkear.com/networking/analyzing-bitcoin-network-traffic-wireshark) - I'm using version 1.11.2.
<p>
<a name="ref23"></a>[23]
The full code for my examples is [available on GitHub](https://github.com/shirriff/bitcoin-code).
<p>
<a name="ref24"></a>[24]
Several Bitcoin libraries in Python are
[bitcoin-python](https://en.bitcoin.it/wiki/Bitcoin-python), [pycoin](https://github.com/richardkiss/pycoin), and
[python-bitcoinlib](https://github.com/jgarzik/python-bitcoinlib).
<p>
<a name="ref25"></a>[25]
The elliptic curve plot was generated from the [Sage](http://www.sagemath.org/) mathematics package:
<pre>
var("x y")
implicit_plot(y^2-x^3-7, (x,-10, 10), (y,-10, 10), figsize=3, title="y^2=x^3+7")
</pre>
<p>
<a name="ref26"></a>[26]
The hardcoded peer list in the Bitcoin client is in [chainparams.cpp](https://github.com/bitcoin/bitcoin/blob/master/src/chainparams.cpp) in the array _pnseed_.
For more information on finding Bitcoin peers, see [How Bitcoin clients find each other](http://bitcoin.stackexchange.com/questions/3536/how-bitcoin-clients-find-each-other) or [Satoshi client node discovery](https://en.bitcoin.it/wiki/Satoshi_Client_Node_Discovery).
<p>
<div style='clear: both;'></div>
</div>
<div class='post-footer'>
<div class='post-footer-line post-footer-line-1'><span class='post-author vcard'>
[Tweet](https://twitter.com/share)
<script>!function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0],p=/^http:/.test(d.location)?'http':'https';if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src=p+'://platform.twitter.com/widgets.js';fjs.parentNode.insertBefore(js,fjs);}}(document, 'script', 'twitter-wjs');</script>
<div class='g-plusone' data-size='medium'></div>
<script type='text/javascript'>
  (function() {
    var po = document.createElement('script'); po.type = 'text/javascript'; po.async = true;
    po.src = 'https://apis.google.com/js/platform.js';
    var s = document.getElementsByTagName('script')[0]; s.parentNode.insertBefore(po, s);
  })();
</script>
Posted by
<span class='fn'>Ken Shirriff</span>
</span>
<span class='post-timestamp'>
at
[<abbr class='published' title='2014-02-01T08:47:00-08:00'>8:47 AM</abbr>](http://www.righto.com/2014/02/bitcoins-hard-way-using-raw-bitcoin.html "permanent link")
</span>
<span class='post-comment-link'>
</span>
<span class='post-icons'>
<span class='item-control blog-admin pid-1138732533'>
[
![](http://img2.blogblog.com/img/icon18_edit_allbkg.gif)
](http://www.blogger.com/post-edit.g?blogID=6264947694886887540&postID=2262890283799972493&from=pencil "Edit Post")
</span>
</span>
<span class='post-backlinks post-comment-link'>
</span>
</div>
<div class='post-footer-line post-footer-line-2'><span class='post-labels'>
Labels:
[bitcoin](http://www.righto.com/search/label/bitcoin)
</span>
</div>
<div style='font-size:14px;margin-top:8px;color: hsl(247, 100%, 32%)'>

        P.S. You can help my daughter raise money for wells in Africa by donating [here](http://righto.com/change) or 
[Donate Bitcoins](https://coinbase.com/checkouts/2bec0e930963643a8c1cf98c05edda12)<script src='https://coinbase.com/assets/button.js' type='text/javascript'></script>
</div>
<div class='post-footer-line post-footer-line-3'></div>
</div>
</div>
<div class='comments' id='comments'>
<a name='comments'></a>

#### 16 comments:

<div id='Blog1_comments-block-wrapper'>
<dl class='avatar-comment-indent' id='comments-block'>
<dt class='comment-author ' id='c2953710590232749535'>
<a name='c2953710590232749535'></a>
<div class="avatar-image-container avatar-stock"><span dir="ltr">[![](http://img2.blogblog.com/img/b16-rounded.gif "Andy Alness")

](http://www.blogger.com/profile/12585240605890974348)</span></div>
[Andy Alness](http://www.blogger.com/profile/12585240605890974348)
said...
</dt>
<dd class='comment-body' id='Blog1_cmt-2953710590232749535'>
<p>
Good article. I did this exercise myself for largely the same purpose. In addition, I also wanted to see how multisig transactions would work for an escrow service and at the time no wallets had implemented them. It took a long time and lots of debugging to make the rather simple transactions work :)

</dd>
<dd class='comment-footer'>
<span class='comment-timestamp'>
[
February 1, 2014 at 10:15 AM
](http://www.righto.com/2014/02/bitcoins-hard-way-using-raw-bitcoin.html?showComment=1391278511051#c2953710590232749535 "comment permalink")
<span class='item-control blog-admin pid-1900516566'>
[
![](//www.blogger.com/img/icon_delete13.gif)
](http://www.blogger.com/delete-comment.g?blogID=6264947694886887540&postID=2953710590232749535 "Delete Comment")
</span>
</span>
</dd>
<dt class='comment-author ' id='c6537224019462939942'>
<a name='c6537224019462939942'></a>
<div class="avatar-image-container avatar-stock"><span dir="ltr">[![](http://img2.blogblog.com/img/b16-rounded.gif "J Crew Customer")

](http://www.blogger.com/profile/04986547867608926967)</span></div>
[J Crew Customer](http://www.blogger.com/profile/04986547867608926967)
said...
</dt>
<dd class='comment-body' id='Blog1_cmt-6537224019462939942'>

Great stuff.  Excellent explanation of elliptic curves and their relevance to cryptography.

</dd>
<dd class='comment-footer'>
<span class='comment-timestamp'>
[
February 1, 2014 at 12:25 PM
](http://www.righto.com/2014/02/bitcoins-hard-way-using-raw-bitcoin.html?showComment=1391286355531#c6537224019462939942 "comment permalink")
<span class='item-control blog-admin pid-1101507519'>
[
![](//www.blogger.com/img/icon_delete13.gif)
](http://www.blogger.com/delete-comment.g?blogID=6264947694886887540&postID=6537224019462939942 "Delete Comment")
</span>
</span>
</dd>
<dt class='comment-author ' id='c5864006548388104818'>
<a name='c5864006548388104818'></a>
<div class="avatar-image-container avatar-stock"><span dir="ltr">[![](http://img1.blogblog.com/img/blank.gif "michagogo")

](https://github.com/michagogo)</span></div>
[michagogo](https://github.com/michagogo)
said...
</dt>
<dd class='comment-body' id='Blog1_cmt-5864006548388104818'>

In note 1, I&#39;d suggest you replace Armory with Electrum -- Armory actually does participate, as it runs an instance of bitcoind in the background.

</dd>
<dd class='comment-footer'>
<span class='comment-timestamp'>
[
February 1, 2014 at 12:43 PM
](http://www.righto.com/2014/02/bitcoins-hard-way-using-raw-bitcoin.html?showComment=1391287383866#c5864006548388104818 "comment permalink")
<span class='item-control blog-admin pid-1176852125'>
[
![](//www.blogger.com/img/icon_delete13.gif)
](http://www.blogger.com/delete-comment.g?blogID=6264947694886887540&postID=5864006548388104818 "Delete Comment")
</span>
</span>
</dd>
<dt class='comment-author ' id='c6606795861919630201'>
<a name='c6606795861919630201'></a>
<div class="avatar-image-container avatar-stock"><span dir="ltr">![](http://img1.blogblog.com/img/blank.gif "Anonymous")

</span></div>
Anonymous
said...
</dt>
<dd class='comment-body' id='Blog1_cmt-6606795861919630201'>

Please also publish your article to http://www.codeproject.com/

Thank you!
Regards,
TomazZ

</dd>
<dd class='comment-footer'>
<span class='comment-timestamp'>
[
February 1, 2014 at 1:04 PM
](http://www.righto.com/2014/02/bitcoins-hard-way-using-raw-bitcoin.html?showComment=1391288655171#c6606795861919630201 "comment permalink")
<span class='item-control blog-admin pid-231644436'>
[
![](//www.blogger.com/img/icon_delete13.gif)
](http://www.blogger.com/delete-comment.g?blogID=6264947694886887540&postID=6606795861919630201 "Delete Comment")
</span>
</span>
</dd>
<dt class='comment-author ' id='c3378806427552100689'>
<a name='c3378806427552100689'></a>
<div class="avatar-image-container avatar-stock"><span dir="ltr">![](http://img1.blogblog.com/img/blank.gif "Anonymous")

</span></div>
Anonymous
said...
</dt>
<dd class='comment-body' id='Blog1_cmt-3378806427552100689'>

Thank you for doing this!!!

Great job!

</dd>
<dd class='comment-footer'>
<span class='comment-timestamp'>
[
February 1, 2014 at 2:03 PM
](http://www.righto.com/2014/02/bitcoins-hard-way-using-raw-bitcoin.html?showComment=1391292223918#c3378806427552100689 "comment permalink")
<span class='item-control blog-admin pid-74049733'>
[
![](//www.blogger.com/img/icon_delete13.gif)
](http://www.blogger.com/delete-comment.g?blogID=6264947694886887540&postID=3378806427552100689 "Delete Comment")
</span>
</span>
</dd>
<dt class='comment-author ' id='c5983367371322728741'>
<a name='c5983367371322728741'></a>
<div class="avatar-image-container avatar-stock"><span dir="ltr">![](http://img1.blogblog.com/img/blank.gif "Anonymous")

</span></div>
Anonymous
said...
</dt>
<dd class='comment-body' id='Blog1_cmt-5983367371322728741'>

Fantastic article. I look forward to the future mining article.

</dd>
<dd class='comment-footer'>
<span class='comment-timestamp'>
[
February 1, 2014 at 3:18 PM
](http://www.righto.com/2014/02/bitcoins-hard-way-using-raw-bitcoin.html?showComment=1391296713682#c5983367371322728741 "comment permalink")
<span class='item-control blog-admin pid-666143721'>
[
![](//www.blogger.com/img/icon_delete13.gif)
](http://www.blogger.com/delete-comment.g?blogID=6264947694886887540&postID=5983367371322728741 "Delete Comment")
</span>
</span>
</dd>
<dt class='comment-author ' id='c9176915190195518269'>
<a name='c9176915190195518269'></a>
<div class="avatar-image-container avatar-stock"><span dir="ltr">![](http://img1.blogblog.com/img/blank.gif "Anonymous")

</span></div>
Anonymous
said...
</dt>
<dd class='comment-body' id='Blog1_cmt-9176915190195518269'>

RIPEMD-160 is used instead of SHA-256 for address hashing because it generates a shorter ascii address string (after base58 conversion)

</dd>
<dd class='comment-footer'>
<span class='comment-timestamp'>
[
February 1, 2014 at 4:27 PM
](http://www.righto.com/2014/02/bitcoins-hard-way-using-raw-bitcoin.html?showComment=1391300859398#c9176915190195518269 "comment permalink")
<span class='item-control blog-admin pid-1407507301'>
[
![](//www.blogger.com/img/icon_delete13.gif)
](http://www.blogger.com/delete-comment.g?blogID=6264947694886887540&postID=9176915190195518269 "Delete Comment")
</span>
</span>
</dd>
<dt class='comment-author ' id='c5313734924391763290'>
<a name='c5313734924391763290'></a>
<div class="avatar-image-container vcard"><span dir="ltr">[![](http://img1.blogblog.com/img/blank.gif "Jordan Baucke")

<noscript>![](//lh3.googleusercontent.com/-96kZzIqilkE/AAAAAAAAAAI/AAAAAAAAC18/UhYxFzIUKJM/s512-c/photo.jpg)</noscript>](http://www.blogger.com/profile/13110827147196202702)</span></div>
[Jordan Baucke](http://www.blogger.com/profile/13110827147196202702)
said...
</dt>
<dd class='comment-body' id='Blog1_cmt-5313734924391763290'>

Read your article with great enthusiasm. Excellent explanations of some of the very nuanced parts of the network that only the core developers seem to understand.

</dd>
<dd class='comment-footer'>
<span class='comment-timestamp'>
[
February 1, 2014 at 6:17 PM
](http://www.righto.com/2014/02/bitcoins-hard-way-using-raw-bitcoin.html?showComment=1391307449084#c5313734924391763290 "comment permalink")
<span class='item-control blog-admin pid-517189411'>
[
![](//www.blogger.com/img/icon_delete13.gif)
](http://www.blogger.com/delete-comment.g?blogID=6264947694886887540&postID=5313734924391763290 "Delete Comment")
</span>
</span>
</dd>
<dt class='comment-author ' id='c3900292953990868416'>
<a name='c3900292953990868416'></a>
<div class="avatar-image-container avatar-stock"><span dir="ltr">![](http://img1.blogblog.com/img/blank.gif "Anonymous")

</span></div>
Anonymous
said...
</dt>
<dd class='comment-body' id='Blog1_cmt-3900292953990868416'>

great article keep em coming!
btc gladly donated (c85e4153b2a8b254015d41c1f94cd6f7b3d31b3d5057b01ccfc995dadc789aaa-000)

</dd>
<dd class='comment-footer'>
<span class='comment-timestamp'>
[
February 1, 2014 at 6:25 PM
](http://www.righto.com/2014/02/bitcoins-hard-way-using-raw-bitcoin.html?showComment=1391307912703#c3900292953990868416 "comment permalink")
<span class='item-control blog-admin pid-339701982'>
[
![](//www.blogger.com/img/icon_delete13.gif)
](http://www.blogger.com/delete-comment.g?blogID=6264947694886887540&postID=3900292953990868416 "Delete Comment")
</span>
</span>
</dd>
<dt class='comment-author ' id='c117131956787192502'>
<a name='c117131956787192502'></a>
<div class="avatar-image-container avatar-stock"><span dir="ltr">![](http://img1.blogblog.com/img/blank.gif "sombody")

</span></div>
sombody
said...
</dt>
<dd class='comment-body' id='Blog1_cmt-117131956787192502'>

FYI that random number generator you are using for making the private keys in the very first gist is not secure enough for crypto. Electrum uses python ecdsa which uses os.urandom.

</dd>
<dd class='comment-footer'>
<span class='comment-timestamp'>
[
February 1, 2014 at 9:34 PM
](http://www.righto.com/2014/02/bitcoins-hard-way-using-raw-bitcoin.html?showComment=1391319276501#c117131956787192502 "comment permalink")
<span class='item-control blog-admin pid-777848674'>
[
![](//www.blogger.com/img/icon_delete13.gif)
](http://www.blogger.com/delete-comment.g?blogID=6264947694886887540&postID=117131956787192502 "Delete Comment")
</span>
</span>
</dd>
<dt class='comment-author ' id='c3441133127784461950'>
<a name='c3441133127784461950'></a>
<div class="avatar-image-container avatar-stock"><span dir="ltr">![](http://img1.blogblog.com/img/blank.gif "Julien")

</span></div>
Julien
said...
</dt>
<dd class='comment-body' id='Blog1_cmt-3441133127784461950'>

Great article. Do you also have a Dogecoin address? I&#39;d like to donate, but currently don&#39;t have an accessible Bitcoin wallet with enough balance.

</dd>
<dd class='comment-footer'>
<span class='comment-timestamp'>
[
February 2, 2014 at 5:42 AM
](http://www.righto.com/2014/02/bitcoins-hard-way-using-raw-bitcoin.html?showComment=1391348548948#c3441133127784461950 "comment permalink")
<span class='item-control blog-admin pid-1858259081'>
[
![](//www.blogger.com/img/icon_delete13.gif)
](http://www.blogger.com/delete-comment.g?blogID=6264947694886887540&postID=3441133127784461950 "Delete Comment")
</span>
</span>
</dd>
<dt class='comment-author ' id='c2276700412622271286'>
<a name='c2276700412622271286'></a>
<div class="avatar-image-container avatar-stock"><span dir="ltr">[![](http://img2.blogblog.com/img/b16-rounded.gif "Shi Ranger")

](http://www.blogger.com/profile/00107543283188379687)</span></div>
[Shi Ranger](http://www.blogger.com/profile/00107543283188379687)
said...
</dt>
<dd class='comment-body' id='Blog1_cmt-2276700412622271286'>

The mining process is very interesting, but I&#39;ll leave that for a future article

what time ? I waiting for this .

</dd>
<dd class='comment-footer'>
<span class='comment-timestamp'>
[
February 2, 2014 at 6:53 AM
](http://www.righto.com/2014/02/bitcoins-hard-way-using-raw-bitcoin.html?showComment=1391352833498#c2276700412622271286 "comment permalink")
<span class='item-control blog-admin pid-2103279851'>
[
![](//www.blogger.com/img/icon_delete13.gif)
](http://www.blogger.com/delete-comment.g?blogID=6264947694886887540&postID=2276700412622271286 "Delete Comment")
</span>
</span>
</dd>
<dt class='comment-author ' id='c2341108661614521080'>
<a name='c2341108661614521080'></a>
<div class="avatar-image-container avatar-stock"><span dir="ltr">![](http://img1.blogblog.com/img/blank.gif "Anonymous")

</span></div>
Anonymous
said...
</dt>
<dd class='comment-body' id='Blog1_cmt-2341108661614521080'>

Very nice.

Small comment: you only mention the old uncompressed format for public keys. There is a much shorter one, namely 0x02 or 0x03 followed by only the X coordinate, 0x03 in case of odd y and 0x02 in case of even. This encoding is preferred because it takes less space in the blockchain and network.

</dd>
<dd class='comment-footer'>
<span class='comment-timestamp'>
[
February 2, 2014 at 8:15 AM
](http://www.righto.com/2014/02/bitcoins-hard-way-using-raw-bitcoin.html?showComment=1391357722281#c2341108661614521080 "comment permalink")
<span class='item-control blog-admin pid-1038344699'>
[
![](//www.blogger.com/img/icon_delete13.gif)
](http://www.blogger.com/delete-comment.g?blogID=6264947694886887540&postID=2341108661614521080 "Delete Comment")
</span>
</span>
</dd>
<dt class='comment-author blog-author' id='c5350152117647161546'>
<a name='c5350152117647161546'></a>
<div class="avatar-image-container vcard"><span dir="ltr">[![](http://img1.blogblog.com/img/blank.gif "Ken Shirriff")

<noscript>![](//lh5.googleusercontent.com/-mhMQ1x5I-14/AAAAAAAAAAI/AAAAAAAAQAM/18Pth9GsJ-M/s512-c/photo.jpg)</noscript>](http://www.blogger.com/profile/08097301407311055124)</span></div>
[Ken Shirriff](http://www.blogger.com/profile/08097301407311055124)
said...
</dt>
<dd class='comment-body' id='Blog1_cmt-5350152117647161546'>

Thanks everyone for the comments. Julien: my Dogecoin address is DAJVsKTtM2QsstemCZVzn5oZAiSywDgDiS

</dd>
<dd class='comment-footer'>
<span class='comment-timestamp'>
[
February 2, 2014 at 10:02 PM
](http://www.righto.com/2014/02/bitcoins-hard-way-using-raw-bitcoin.html?showComment=1391407372563#c5350152117647161546 "comment permalink")
<span class='item-control blog-admin pid-1138732533'>
[
![](//www.blogger.com/img/icon_delete13.gif)
](http://www.blogger.com/delete-comment.g?blogID=6264947694886887540&postID=5350152117647161546 "Delete Comment")
</span>
</span>
</dd>
<dt class='comment-author ' id='c5920876022580319164'>
<a name='c5920876022580319164'></a>
<div class="avatar-image-container avatar-stock"><span dir="ltr">![](http://img1.blogblog.com/img/blank.gif "Anonymous")

</span></div>
Anonymous
said...
</dt>
<dd class='comment-body' id='Blog1_cmt-5920876022580319164'>

Sent!

</dd>
<dd class='comment-footer'>
<span class='comment-timestamp'>
[
February 3, 2014 at 10:19 AM
](http://www.righto.com/2014/02/bitcoins-hard-way-using-raw-bitcoin.html?showComment=1391451565226#c5920876022580319164 "comment permalink")
<span class='item-control blog-admin pid-650511844'>
[
![](//www.blogger.com/img/icon_delete13.gif)
](http://www.blogger.com/delete-comment.g?blogID=6264947694886887540&postID=5920876022580319164 "Delete Comment")
</span>
</span>
</dd>
<dt class='comment-author blog-author' id='c4205779930285601417'>
<a name='c4205779930285601417'></a>
<div class="avatar-image-container vcard"><span dir="ltr">[![](http://img1.blogblog.com/img/blank.gif "Ken Shirriff")

<noscript>![](//lh5.googleusercontent.com/-mhMQ1x5I-14/AAAAAAAAAAI/AAAAAAAAQAM/18Pth9GsJ-M/s512-c/photo.jpg)</noscript>](http://www.blogger.com/profile/08097301407311055124)</span></div>
[Ken Shirriff](http://www.blogger.com/profile/08097301407311055124)
said...
</dt>
<dd class='comment-body' id='Blog1_cmt-4205779930285601417'>

Wow. Much dogecoin donation. Very generous. So thanks.

And thank you everyone for the Bitcoin donations too. It&#39;s all going for [wells in Africa](https://thewaterproject.org/community/profile/north-star-academy-change-4-change).

</dd>
<dd class='comment-footer'>
<span class='comment-timestamp'>
[
February 3, 2014 at 8:09 PM
](http://www.righto.com/2014/02/bitcoins-hard-way-using-raw-bitcoin.html?showComment=1391486984501#c4205779930285601417 "comment permalink")
<span class='item-control blog-admin pid-1138732533'>
[
![](//www.blogger.com/img/icon_delete13.gif)
](http://www.blogger.com/delete-comment.g?blogID=6264947694886887540&postID=4205779930285601417 "Delete Comment")
</span>
</span>
</dd>
</dl>
</div>

[Post a Comment](http://www.blogger.com/comment.g?blogID=6264947694886887540&amp;postID=2262890283799972493)

<div id='backlinks-container'>
<div id='Blog1_backlinks-container'>
<a name='links'></a>

#### 

[
]()

</div>
</div>
</div>
</div>
<!-- google_ad_section_end(name=default) -->
<div class='inline-ad'>
</div>
<!-- google_ad_section_start -->

        </div></div>

<!-- google_ad_section_end -->
</div>
<div class='blog-pager' id='blog-pager'>
<span id='blog-pager-older-link'>
[Older Post](http://www.righto.com/2013/11/how-hacker-news-ranking-really-works.html "Older Post")
</span>
[Home](http://www.righto.com/)
</div>
<div class='clear'></div>
<div class='post-feeds'>
<div class='feed-links'>
Subscribe to:
[Post Comments (Atom)](http://www.righto.com/feeds/2262890283799972493/comments/default)
</div>
</div>
<script type="text/javascript">window.___gcfg = {'lang': 'en'};</script>
</div></div>
</div>
<div id='sidebar-wrapper'>
<div class='sidebar section' id='sidebar'><div class='widget LinkList' id='LinkList2'>

## Power supply posts

<div class='widget-content'>

*   [iPhone charger teardown](http://www.arcfn.com/2012/05/apple-iphone-charger-teardown-quality.html)
*   [A dozen USB chargers](http://www.arcfn.com/2012/10/a-dozen-usb-chargers-in-lab-apple-is.html)
*   [Magsafe hacking](http://www.righto.com/2013/06/teardown-and-exploration-of-magsafe.html)
*   [Inside a fake iPhone charger](http://www.arcfn.com/2012/03/inside-cheap-phone-charger-and-why-you.html)
*   [Power supply history](http://www.arcfn.com/2012/02/apple-didnt-revolutionize-power.html)
<div class='clear'></div>
<span class='widget-item-control'>
<span class='item-control blog-admin'>
[
![](http://img1.blogblog.com/img/icon18_wrench_allbkg.png)
](//www.blogger.com/rearrange?blogID=6264947694886887540&widgetType=LinkList&widgetId=LinkList2&action=editWidget&sectionId=sidebar "Edit")
</span>
</span>
<div class='clear'></div>
</div>
</div><div class='widget LinkList' id='LinkList1'>

## Quick links

<div class='widget-content'>

*   [Arduino IR library](http://www.arcfn.com/2009/08/multi-protocol-infrared-remote-library.html)
*   [6502 reverse-engineering](http://www.righto.com/2013/01/a-small-part-of-6502-chip-explained.html)
<div class='clear'></div>
<span class='widget-item-control'>
<span class='item-control blog-admin'>
[
![](http://img1.blogblog.com/img/icon18_wrench_allbkg.png)
](//www.blogger.com/rearrange?blogID=6264947694886887540&widgetType=LinkList&widgetId=LinkList1&action=editWidget&sectionId=sidebar "Edit")
</span>
</span>
<div class='clear'></div>
</div>
</div><div class='widget Label' id='Label1'>

## Labels

<div class='widget-content cloud-label-widget-content'>
<span class='label-size label-size-3'>
[6502](http://www.righto.com/search/label/6502)
</span>
<span class='label-size label-size-4'>
[8085](http://www.righto.com/search/label/8085)
</span>
<span class='label-size label-size-3'>
[apple](http://www.righto.com/search/label/apple)
</span>
<span class='label-size label-size-5'>
[arc](http://www.righto.com/search/label/arc)
</span>
<span class='label-size label-size-4'>
[arduino](http://www.righto.com/search/label/arduino)
</span>
<span class='label-size label-size-1'>
[bitcoin](http://www.righto.com/search/label/bitcoin)
</span>
<span class='label-size label-size-1'>
[c#](http://www.righto.com/search/label/c%23)
</span>
<span class='label-size label-size-2'>
[calculator](http://www.righto.com/search/label/calculator)
</span>
<span class='label-size label-size-2'>
[css](http://www.righto.com/search/label/css)
</span>
<span class='label-size label-size-4'>
[electronics](http://www.righto.com/search/label/electronics)
</span>
<span class='label-size label-size-1'>
[f#](http://www.righto.com/search/label/f%23)
</span>
<span class='label-size label-size-1'>
[fractals](http://www.righto.com/search/label/fractals)
</span>
<span class='label-size label-size-2'>
[genome](http://www.righto.com/search/label/genome)
</span>
<span class='label-size label-size-1'>
[haskell](http://www.righto.com/search/label/haskell)
</span>
<span class='label-size label-size-2'>
[html5](http://www.righto.com/search/label/html5)
</span>
<span class='label-size label-size-2'>
[ipv6](http://www.righto.com/search/label/ipv6)
</span>
<span class='label-size label-size-4'>
[ir](http://www.righto.com/search/label/ir)
</span>
<span class='label-size label-size-2'>
[java](http://www.righto.com/search/label/java)
</span>
<span class='label-size label-size-2'>
[javascript](http://www.righto.com/search/label/javascript)
</span>
<span class='label-size label-size-3'>
[math](http://www.righto.com/search/label/math)
</span>
<span class='label-size label-size-2'>
[oscilloscope](http://www.righto.com/search/label/oscilloscope)
</span>
<span class='label-size label-size-2'>
[photo](http://www.righto.com/search/label/photo)
</span>
<span class='label-size label-size-3'>
[power supply](http://www.righto.com/search/label/power%20supply)
</span>
<span class='label-size label-size-5'>
[random](http://www.righto.com/search/label/random)
</span>
<span class='label-size label-size-3'>
[sheevaplug](http://www.righto.com/search/label/sheevaplug)
</span>
<span class='label-size label-size-3'>
[snark](http://www.righto.com/search/label/snark)
</span>
<span class='label-size label-size-2'>
[spanish](http://www.righto.com/search/label/spanish)
</span>
<span class='label-size label-size-2'>
[theory](http://www.righto.com/search/label/theory)
</span>
<span class='label-size label-size-2'>
[Z-80](http://www.righto.com/search/label/Z-80)
</span>
<div class='clear'></div>
<span class='widget-item-control'>
<span class='item-control blog-admin'>
[
![](http://img1.blogblog.com/img/icon18_wrench_allbkg.png)
](//www.blogger.com/rearrange?blogID=6264947694886887540&widgetType=Label&widgetId=Label1&action=editWidget&sectionId=sidebar "Edit")
</span>
</span>
<div class='clear'></div>
</div>
</div><div class='widget AdSense' id='AdSense1'>
<div class='widget-content'>
<script type="text/javascript"><!--
google_ad_client="pub-0159785530845184";
google_ad_host="pub-1556223355139109";
google_alternate_ad_url="http://www.blogger.com/img/blogger_ad160x600.html";
google_ad_width=160;
google_ad_height=600;
google_ad_format="160x600_as";
google_ad_type="text_image";
google_ad_host_channel="0001";
google_color_border="E6E6E6";
google_color_bg="E6E6E6";
google_color_link="B8A80D";
google_color_url="999999";
google_color_text="000000";
//--></script>
<script type="text/javascript"
  src="http://pagead2.googlesyndication.com/pagead/show_ads.js">
</script>
<div class='clear'></div>
<span class='widget-item-control'>
<span class='item-control blog-admin'>
[
![](http://img1.blogblog.com/img/icon18_wrench_allbkg.png)
](//www.blogger.com/rearrange?blogID=6264947694886887540&widgetType=AdSense&widgetId=AdSense1&action=editWidget&sectionId=sidebar "Edit")
</span>
</span>
<div class='clear'></div>
</div>
</div><div class='widget PopularPosts' id='PopularPosts1'>

## Popular Posts

<div class='widget-content popular-posts'>

*   <div class='item-thumbnail-only'>
<div class='item-title'>[Apple iPhone charger teardown: quality in a tiny expensive package](http://www.righto.com/2012/05/apple-iphone-charger-teardown-quality.html)</div>
</div>
<div style='clear: both;'></div>

*   <div class='item-thumbnail-only'>
<div class='item-title'>[A Multi-Protocol Infrared Remote Library for the Arduino](http://www.righto.com/2009/08/multi-protocol-infrared-remote-library.html)</div>
</div>
<div style='clear: both;'></div>

*   <div class='item-thumbnail-only'>
<div class='item-thumbnail'>
[
![](https://lh3.googleusercontent.com/-zO_0pO2xHGA/UDXL2yu-vmI/AAAAAAAAM4k/t9d1RNi0V_I/s72-c/chargers.png)
](http://www.righto.com/2012/10/a-dozen-usb-chargers-in-lab-apple-is.html)
</div>
<div class='item-title'>[A dozen USB chargers in the lab: Apple is very good, but not quite the best](http://www.righto.com/2012/10/a-dozen-usb-chargers-in-lab-apple-is.html)</div>
</div>
<div style='clear: both;'></div>

*   <div class='item-thumbnail-only'>
<div class='item-thumbnail'>
[
![](https://lh3.googleusercontent.com/-CblJ2EJedW8/T7z8QWAtNtI/AAAAAAAAKmI/uSuHSPgSeFM/s72-c/charger-iphone4.png)
](http://www.righto.com/2012/03/inside-cheap-phone-charger-and-why-you.html)
</div>
<div class='item-title'>[Tiny, cheap, and dangerous: Inside a (fake) iPhone charger](http://www.righto.com/2012/03/inside-cheap-phone-charger-and-why-you.html)</div>
</div>
<div style='clear: both;'></div>

*   <div class='item-thumbnail-only'>
<div class='item-title'>[Apple didn't revolutionize power supplies; new transistors did](http://www.righto.com/2012/02/apple-didnt-revolutionize-power.html)</div>
</div>
<div style='clear: both;'></div>

*   <div class='item-thumbnail-only'>
<div class='item-title'>[Secrets of Arduino PWM](http://www.righto.com/2009/07/secrets-of-arduino-pwm.html)</div>
</div>
<div style='clear: both;'></div>

*   <div class='item-thumbnail-only'>
<div class='item-title'>[An Arduino universal remote: record and playback IR signals](http://www.righto.com/2009/09/arduino-universal-remote-record-and.html)</div>
</div>
<div style='clear: both;'></div>

*   <div class='item-thumbnail-only'>
<div class='item-title'>[Inside the ALU of the 8085 microprocessor](http://www.righto.com/2013/01/inside-alu-of-8085-microprocessor.html)</div>
</div>
<div style='clear: both;'></div>
<div class='clear'></div>
<span class='widget-item-control'>
<span class='item-control blog-admin'>
[
![](http://img1.blogblog.com/img/icon18_wrench_allbkg.png)
](//www.blogger.com/rearrange?blogID=6264947694886887540&widgetType=PopularPosts&widgetId=PopularPosts1&action=editWidget&sectionId=sidebar "Edit")
</span>
</span>
<div class='clear'></div>
</div>
</div><div class='widget HTML' id='HTML1'>
<div class='widget-content'>
<script type="text/javascript"><!--
amazon_ad_tag = "rightocom"; amazon_ad_width = "160"; amazon_ad_height = "600"; amazon_ad_link_target = "new"; amazon_color_background = "F6F6F6"; amazon_color_link = "0B3660";//--></script>
<script type="text/javascript" src="http://www.assoc-amazon.com/s/ads.js"></script>

<!--<script charset="utf-8" type="text/javascript" src="http://ws.amazon.com/widgets/q?ServiceVersion=20070822&MarketPlace=US&ID=V20070822/US/rightocom/8001/b3f6b083-3c2a-4444-a0de-6d75351ee9d6"> </script> <noscript>[Amazon.com Widgets](http://ws.amazon.com/widgets/q?ServiceVersion=20070822&MarketPlace=US&ID=V20070822%2FUS%2Frightocom%2F8001%2Fb3f6b083-3c2a-4444-a0de-6d75351ee9d6&Operation=NoScript)</noscript>-->
</div>
<div class='clear'></div>
<span class='widget-item-control'>
<span class='item-control blog-admin'>
[
![](http://img1.blogblog.com/img/icon18_wrench_allbkg.png)
](//www.blogger.com/rearrange?blogID=6264947694886887540&widgetType=HTML&widgetId=HTML1&action=editWidget&sectionId=sidebar "Edit")
</span>
</span>
<div class='clear'></div>
</div><div class='widget BlogArchive' id='BlogArchive1'>

## Blog Archive

<div class='widget-content'>
<div id='ArchiveList'>
<div id='BlogArchive1_ArchiveList'>

*   [
<span class='zippy toggle-open'>&#9660;&#160;</span>
](javascript:void(0))
[2014](http://www.righto.com/search?updated-min=2014-01-01T00:00:00-08:00&amp;updated-max=2015-01-01T00:00:00-08:00&amp;max-results=1)
<span class='post-count' dir='ltr'>(1)</span>

    *   [
<span class='zippy toggle-open'>&#9660;&#160;</span>
](javascript:void(0))
[February](http://www.righto.com/2014_02_01_archive.html)
<span class='post-count' dir='ltr'>(1)</span>

            *   [Bitcoins the hard way: Using the raw Bitcoin proto...](http://www.righto.com/2014/02/bitcoins-hard-way-using-raw-bitcoin.html)

*   [
<span class='zippy'>

              &#9658;&#160;
</span>
](javascript:void(0))
[2013](http://www.righto.com/search?updated-min=2013-01-01T00:00:00-08:00&amp;updated-max=2014-01-01T00:00:00-08:00&amp;max-results=24)
<span class='post-count' dir='ltr'>(24)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[November](http://www.righto.com/2013_11_01_archive.html)
<span class='post-count' dir='ltr'>(2)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[September](http://www.righto.com/2013_09_01_archive.html)
<span class='post-count' dir='ltr'>(4)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[August](http://www.righto.com/2013_08_01_archive.html)
<span class='post-count' dir='ltr'>(4)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[July](http://www.righto.com/2013_07_01_archive.html)
<span class='post-count' dir='ltr'>(4)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[June](http://www.righto.com/2013_06_01_archive.html)
<span class='post-count' dir='ltr'>(2)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[April](http://www.righto.com/2013_04_01_archive.html)
<span class='post-count' dir='ltr'>(1)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[March](http://www.righto.com/2013_03_01_archive.html)
<span class='post-count' dir='ltr'>(2)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[February](http://www.righto.com/2013_02_01_archive.html)
<span class='post-count' dir='ltr'>(2)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[January](http://www.righto.com/2013_01_01_archive.html)
<span class='post-count' dir='ltr'>(3)</span>

*   [
<span class='zippy'>

              &#9658;&#160;
</span>
](javascript:void(0))
[2012](http://www.righto.com/search?updated-min=2012-01-01T00:00:00-08:00&amp;updated-max=2013-01-01T00:00:00-08:00&amp;max-results=10)
<span class='post-count' dir='ltr'>(10)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[December](http://www.righto.com/2012_12_01_archive.html)
<span class='post-count' dir='ltr'>(1)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[November](http://www.righto.com/2012_11_01_archive.html)
<span class='post-count' dir='ltr'>(5)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[October](http://www.righto.com/2012_10_01_archive.html)
<span class='post-count' dir='ltr'>(1)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[May](http://www.righto.com/2012_05_01_archive.html)
<span class='post-count' dir='ltr'>(1)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[March](http://www.righto.com/2012_03_01_archive.html)
<span class='post-count' dir='ltr'>(1)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[February](http://www.righto.com/2012_02_01_archive.html)
<span class='post-count' dir='ltr'>(1)</span>

*   [
<span class='zippy'>

              &#9658;&#160;
</span>
](javascript:void(0))
[2011](http://www.righto.com/search?updated-min=2011-01-01T00:00:00-08:00&amp;updated-max=2012-01-01T00:00:00-08:00&amp;max-results=11)
<span class='post-count' dir='ltr'>(11)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[December](http://www.righto.com/2011_12_01_archive.html)
<span class='post-count' dir='ltr'>(2)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[July](http://www.righto.com/2011_07_01_archive.html)
<span class='post-count' dir='ltr'>(2)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[May](http://www.righto.com/2011_05_01_archive.html)
<span class='post-count' dir='ltr'>(2)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[April](http://www.righto.com/2011_04_01_archive.html)
<span class='post-count' dir='ltr'>(1)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[March](http://www.righto.com/2011_03_01_archive.html)
<span class='post-count' dir='ltr'>(1)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[February](http://www.righto.com/2011_02_01_archive.html)
<span class='post-count' dir='ltr'>(3)</span>

*   [
<span class='zippy'>

              &#9658;&#160;
</span>
](javascript:void(0))
[2010](http://www.righto.com/search?updated-min=2010-01-01T00:00:00-08:00&amp;updated-max=2011-01-01T00:00:00-08:00&amp;max-results=22)
<span class='post-count' dir='ltr'>(22)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[December](http://www.righto.com/2010_12_01_archive.html)
<span class='post-count' dir='ltr'>(2)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[November](http://www.righto.com/2010_11_01_archive.html)
<span class='post-count' dir='ltr'>(4)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[October](http://www.righto.com/2010_10_01_archive.html)
<span class='post-count' dir='ltr'>(3)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[August](http://www.righto.com/2010_08_01_archive.html)
<span class='post-count' dir='ltr'>(1)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[June](http://www.righto.com/2010_06_01_archive.html)
<span class='post-count' dir='ltr'>(1)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[May](http://www.righto.com/2010_05_01_archive.html)
<span class='post-count' dir='ltr'>(2)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[April](http://www.righto.com/2010_04_01_archive.html)
<span class='post-count' dir='ltr'>(3)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[March](http://www.righto.com/2010_03_01_archive.html)
<span class='post-count' dir='ltr'>(4)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[January](http://www.righto.com/2010_01_01_archive.html)
<span class='post-count' dir='ltr'>(2)</span>

*   [
<span class='zippy'>

              &#9658;&#160;
</span>
](javascript:void(0))
[2009](http://www.righto.com/search?updated-min=2009-01-01T00:00:00-08:00&amp;updated-max=2010-01-01T00:00:00-08:00&amp;max-results=22)
<span class='post-count' dir='ltr'>(22)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[December](http://www.righto.com/2009_12_01_archive.html)
<span class='post-count' dir='ltr'>(2)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[November](http://www.righto.com/2009_11_01_archive.html)
<span class='post-count' dir='ltr'>(5)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[September](http://www.righto.com/2009_09_01_archive.html)
<span class='post-count' dir='ltr'>(1)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[August](http://www.righto.com/2009_08_01_archive.html)
<span class='post-count' dir='ltr'>(3)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[July](http://www.righto.com/2009_07_01_archive.html)
<span class='post-count' dir='ltr'>(1)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[June](http://www.righto.com/2009_06_01_archive.html)
<span class='post-count' dir='ltr'>(3)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[April](http://www.righto.com/2009_04_01_archive.html)
<span class='post-count' dir='ltr'>(1)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[March](http://www.righto.com/2009_03_01_archive.html)
<span class='post-count' dir='ltr'>(3)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[February](http://www.righto.com/2009_02_01_archive.html)
<span class='post-count' dir='ltr'>(2)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[January](http://www.righto.com/2009_01_01_archive.html)
<span class='post-count' dir='ltr'>(1)</span>

*   [
<span class='zippy'>

              &#9658;&#160;
</span>
](javascript:void(0))
[2008](http://www.righto.com/search?updated-min=2008-01-01T00:00:00-08:00&amp;updated-max=2009-01-01T00:00:00-08:00&amp;max-results=27)
<span class='post-count' dir='ltr'>(27)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[July](http://www.righto.com/2008_07_01_archive.html)
<span class='post-count' dir='ltr'>(3)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[June](http://www.righto.com/2008_06_01_archive.html)
<span class='post-count' dir='ltr'>(1)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[May](http://www.righto.com/2008_05_01_archive.html)
<span class='post-count' dir='ltr'>(3)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[April](http://www.righto.com/2008_04_01_archive.html)
<span class='post-count' dir='ltr'>(4)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[March](http://www.righto.com/2008_03_01_archive.html)
<span class='post-count' dir='ltr'>(10)</span>

    *   [
<span class='zippy'>

                  &#9658;&#160;
</span>
](javascript:void(0))
[February](http://www.righto.com/2008_02_01_archive.html)
<span class='post-count' dir='ltr'>(6)</span>
</div>
</div>
<div class='clear'></div>
<span class='widget-item-control'>
<span class='item-control blog-admin'>
[
![](http://img1.blogblog.com/img/icon18_wrench_allbkg.png)
](//www.blogger.com/rearrange?blogID=6264947694886887540&widgetType=BlogArchive&widgetId=BlogArchive1&action=editWidget&sectionId=sidebar "Edit")
</span>
</span>
<div class='clear'></div>
</div>
</div><div class='widget HTML' id='HTML2'>
<div class='widget-content'>
[![](https://lh4.googleusercontent.com/-4nE0TSEWMU8/T7-1iAcgXbI/AAAAAAAAKpo/bUaz-b76Cv4/s800/name.png)](http://www.blogger.com/profile/08097301407311055124)
</div>
<div class='clear'></div>
<span class='widget-item-control'>
<span class='item-control blog-admin'>
[
![](http://img1.blogblog.com/img/icon18_wrench_allbkg.png)
](//www.blogger.com/rearrange?blogID=6264947694886887540&widgetType=HTML&widgetId=HTML2&action=editWidget&sectionId=sidebar "Edit")
</span>
</span>
<div class='clear'></div>
</div></div>
</div>
<!-- spacer for skins that want sidebar and main to be the same height-->
<div class='clear'>&#160;</div>
</div>
<!-- end content-wrapper -->
</div></div>
<!-- end outer-wrapper -->
<script type='text/javascript'> 
var gaJsHost = (("https:" == document.location.protocol) ? "https://ssl." : "http://www.");
document.write(unescape("%3Cscript src='" + gaJsHost + "google-analytics.com/ga.js' type='text/javascript'%3E%3C/script%3E"));
</script>
<script type='text/javascript'> 
var pageTracker = _gat._getTracker("UA-3782444-1");
pageTracker._initData();
pageTracker._trackPageview();
</script>
<script type="text/javascript">
if (window.jstiming) window.jstiming.load.tick('widgetJsBefore');
</script><script type="text/javascript" src="https://www.blogger.com/static/v1/widgets/2221937197-widgets.js"></script>
<script type="text/javascript" src="https://apis.google.com/js/plusone.js"></script>
<script type='text/javascript'>
if (typeof(BLOG_attachCsiOnload) != 'undefined' && BLOG_attachCsiOnload != null) { window['blogger_templates_experiment_id'] = "templatesV1";window['blogger_blog_id'] = '6264947694886887540';BLOG_attachCsiOnload('item_'); }_WidgetManager._Init('//www.blogger.com/rearrange?blogID\x3d6264947694886887540','//www.righto.com/2014/02/bitcoins-hard-way-using-raw-bitcoin.html','6264947694886887540');
_WidgetManager._SetDataContext([{'name': 'blog', 'data': {'blogId': '6264947694886887540', 'bloggerUrl': 'http://www.blogger.com', 'title': 'Ken Shirriff\47s blog', 'pageType': 'item', 'url': 'http://www.righto.com/2014/02/bitcoins-hard-way-using-raw-bitcoin.html', 'canonicalUrl': 'http://www.righto.com/2014/02/bitcoins-hard-way-using-raw-bitcoin.html', 'canonicalHomepageUrl': 'http://www.righto.com/', 'homepageUrl': 'http://www.righto.com/', 'blogspotFaviconUrl': 'http://www.righto.com/favicon.ico', 'enabledCommentProfileImages': true, 'adultContent': false, 'disableAdSenseWidget': false, 'analyticsAccountNumber': 'UA-3782444-1', 'searchLabel': '', 'searchQuery': '', 'pageName': 'Bitcoins the hard way: Using the raw Bitcoin protocol', 'pageTitle': 'Ken Shirriff\47s blog: Bitcoins the hard way: Using the raw Bitcoin protocol', 'encoding': 'UTF-8', 'locale': 'en', 'localeUnderscoreDelimited': 'en', 'isPrivate': false, 'isMobile': false, 'isMobileRequest': false, 'mobileClass': '', 'isPrivateBlog': false, 'languageDirection': 'ltr', 'feedLinks': '\74link rel\75\42alternate\42 type\75\42application/atom+xml\42 title\75\42Ken Shirriff\46#39;s blog - Atom\42 href\75\42http://www.righto.com/feeds/posts/default\42 /\76\n\74link rel\75\42alternate\42 type\75\42application/rss+xml\42 title\75\42Ken Shirriff\46#39;s blog - RSS\42 href\75\42http://www.righto.com/feeds/posts/default?alt\75rss\42 /\76\n\74link rel\75\42service.post\42 type\75\42application/atom+xml\42 title\75\42Ken Shirriff\46#39;s blog - Atom\42 href\75\42http://www.blogger.com/feeds/6264947694886887540/posts/default\42 /\76\n\n\74link rel\75\42alternate\42 type\75\42application/atom+xml\42 title\75\42Ken Shirriff\46#39;s blog - Atom\42 href\75\42http://www.righto.com/feeds/2262890283799972493/comments/default\42 /\76\n', 'meTag': '', 'openIdOpTag': '', 'postImageThumbnailUrl': 'https://lh4.googleusercontent.com/-anHnnkgfQw8/Ut4QeV-Q9yI/AAAAAAAAWXA/RvR5tOvR4tk/s72-c/signing-thumb.png', 'imageSrcTag': '\74link rel\75\42image_src\42 href\75\42https://lh4.googleusercontent.com/-anHnnkgfQw8/Ut4QeV-Q9yI/AAAAAAAAWXA/RvR5tOvR4tk/s72-c/signing-thumb.png\42 /\76\n', 'latencyHeadScript': '\74script type\75\42text/javascript\42\76(function() { var b\75window,f\75\42chrome\42,g\75\42jstiming\42,k\75\42tick\42;(function(){function d(a){this.t\75{};this.tick\75function(a,d,c){var e\75void 0!\75c?c:(new Date).getTime();this.t[a]\75[e,d];if(void 0\75\75c)try{b.console.timeStamp(\42CSI/\42+a)}catch(h){}};this[k](\42start\42,null,a)}var a;b.performance\46\46(a\75b.performance.timing);var n\75a?new d(a.responseStart):new d;b.jstiming\75{Timer:d,load:n};if(a){var c\75a.navigationStart,h\75a.responseStart;0\74c\46\46h\76\75c\46\46(b[g].srt\75h-c)}if(a){var e\75b[g].load;0\74c\46\46h\76\75c\46\46(e[k](\42_wtsrt\42,void 0,c),e[k](\42wtsrt_\42,\42_wtsrt\42,h),e[k](\42tbsd_\42,\42wtsrt_\42))}try{a\75null,\nb[f]\46\46b[f].csi\46\46(a\75Math.floor(b[f].csi().pageT),e\46\0460\74c\46\46(e[k](\42_tbnd\42,void 0,b[f].csi().startE),e[k](\42tbnd_\42,\42_tbnd\42,c))),null\75\75a\46\46b.gtbExternal\46\46(a\75b.gtbExternal.pageT()),null\75\75a\46\46b.external\46\46(a\75b.external.pageT,e\46\0460\74c\46\46(e[k](\42_tbnd\42,void 0,b.external.startE),e[k](\42tbnd_\42,\42_tbnd\42,c))),a\46\46(b[g].pt\75a)}catch(p){}})();b.tickAboveFold\75function(d){var a\0750;if(d.offsetParent){do a+\75d.offsetTop;while(d\75d.offsetParent)}d\75a;750\76\75d\46\46b[g].load[k](\42aft\42)};var l\75!1;function m(){l||(l\75!0,b[g].load[k](\42firstScrollTime\42))}b.addEventListener?b.addEventListener(\42scroll\42,m,!1):b.attachEvent(\42onscroll\42,m);\n })();\74/script\076', 'mobileHeadScript': '', 'adsenseClientId': 'pub-0159785530845184', 'view': '', 'dynamicViewsCommentsSrc': '//www.blogblog.com/dynamicviews/4224c15c4e7c9321/js/comments.js', 'dynamicViewsScriptSrc': '//www.blogblog.com/dynamicviews/d6f37bb30c327165', 'plusOneApiSrc': 'https://apis.google.com/js/plusone.js', 'testHtml5CssSrc': 'https://www.blogger.com/static/v1/widgets/3640646893-css_bundle_html5.css', 'sf': 'n', 'tf': ''}}, {'name': 'skin', 'data': {'vars': {'linkcolor': '#910018', 'visitedlinkcolor': '#910018', 'textcolor': '#000000', 'headerfont': 'normal 150% Chivo,Verdana,Sans-serif', 'pagetitlefont': 'normal 300% Chivo,Verdana,Sans-Serif', 'descriptionColor': '#444444', 'titlefont': 'normal 160% Verdana,Sans-Serif', 'bgcolor': '#f6f6f6', 'bordercolor': '#444444', 'titlecolor': '#444444', 'datecolor': '#777777', 'footerlinkcolor': '#968a0a', 'descbgcolor': '#f6fbf7', 'pagetitlecolor': '#444444', 'bodyfont': 'normal normal 100% \47Trebuchet MS\47,Trebuchet,Verdana,Sans-Serif', 'endSide': 'right', 'startSide': 'left', 'dateHeaderFont': 'normal bold 105% \47Trebuchet MS\47,Trebuchet,Verdana,Sans-serif', 'pagetitlebgcolor': '#f6fbf7', 'sidebarcolor': '#444444', 'sidebarlinkcolor': '#910018', 'footercolor': '#444444'}, 'override': ''}}, {'name': 'view', 'data': {'classic': {'name': 'classic', 'url': '/?view\75classic'}, 'flipcard': {'name': 'flipcard', 'url': '/?view\75flipcard'}, 'magazine': {'name': 'magazine', 'url': '/?view\75magazine'}, 'mosaic': {'name': 'mosaic', 'url': '/?view\75mosaic'}, 'sidebar': {'name': 'sidebar', 'url': '/?view\75sidebar'}, 'snapshot': {'name': 'snapshot', 'url': '/?view\75snapshot'}, 'timeslide': {'name': 'timeslide', 'url': '/?view\75timeslide'}}}]);
_WidgetManager._RegisterWidget('_LinkListView', new _WidgetInfo('LinkList2', 'sidebar', null, document.getElementById('LinkList2'), {}, 'displayModeFull'));
_WidgetManager._RegisterWidget('_LinkListView', new _WidgetInfo('LinkList1', 'sidebar', null, document.getElementById('LinkList1'), {}, 'displayModeFull'));
_WidgetManager._RegisterWidget('_LabelView', new _WidgetInfo('Label1', 'sidebar', null, document.getElementById('Label1'), {}, 'displayModeFull'));
_WidgetManager._RegisterWidget('_AdSenseView', new _WidgetInfo('AdSense1', 'sidebar', null, document.getElementById('AdSense1'), {}, 'displayModeFull'));
_WidgetManager._RegisterWidget('_PopularPostsView', new _WidgetInfo('PopularPosts1', 'sidebar', null, document.getElementById('PopularPosts1'), {}, 'displayModeFull'));
_WidgetManager._RegisterWidget('_HTMLView', new _WidgetInfo('HTML1', 'sidebar', null, document.getElementById('HTML1'), {}, 'displayModeFull'));
_WidgetManager._RegisterWidget('_BlogArchiveView', new _WidgetInfo('BlogArchive1', 'sidebar', null, document.getElementById('BlogArchive1'), {'languageDirection': 'ltr'}, 'displayModeFull'));
_WidgetManager._RegisterWidget('_HTMLView', new _WidgetInfo('HTML2', 'sidebar', null, document.getElementById('HTML2'), {}, 'displayModeFull'));
_WidgetManager._RegisterWidget('_HeaderView', new _WidgetInfo('Header1', 'header', null, document.getElementById('Header1'), {}, 'displayModeFull'));
_WidgetManager._RegisterWidget('_NavbarView', new _WidgetInfo('Navbar1', 'navbar', null, document.getElementById('Navbar1'), {}, 'displayModeFull'));
_WidgetManager._RegisterWidget('_BlogView', new _WidgetInfo('Blog1', 'main', null, document.getElementById('Blog1'), {'cmtInteractionsEnabled': false, 'showBacklinks': true, 'postId': '2262890283799972493', 'lightboxEnabled': true, 'lightboxModuleUrl': 'https://www.blogger.com/static/v1/jsbin/666586162-lbx.js', 'lightboxCssUrl': 'https://www.blogger.com/static/v1/v-css/4138445517-lightbox_bundle.css'}, 'displayModeFull'));
_WidgetManager._RegisterWidget('_PageListView', new _WidgetInfo('PageList1', 'null', null, document.getElementById('PageList1'), {'title': 'Pages', 'links': [{'href': 'http://www.righto.com/', 'title': 'Home', 'isCurrentPage': false}, {'href': 'http://www.righto.com/p/details-about-8085-registers.html', 'title': 'Details about 8085 registers', 'isCurrentPage': false}], 'mobile': false}, 'displayModeFull'));
</script>
</body>
</html>