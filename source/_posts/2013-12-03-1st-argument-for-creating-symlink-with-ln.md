---
layout: post
title: ln -s するときに『どっちが TARGET？』といつも迷うので
date: 2013/12/03 00:00
comments: true
---

Linux でシンボリックリンクを張ろうとしてヘルプを見ると

```
% ln --help
Usage: ln [OPTION]... [-T] TARGET LINK_NAME
```

[『どっちがどっち～？』][40]感がひじょーに強いメッセージ...で、結局ググりがち。

**第1引数は実ファイル**

**第1引数は実ファイル**

はい、大事なことなので二回言いました。

## もう迷わない ln -s 相対パス編

プロジェクト内のディレクトリ間でシンボリックリンクを張るとき[^01]など、相対パスでファイルのありかを指定する方が都合がいい場合があります。

1. 実ファイルのパスを確認
2. シンボリックリンクを設置したい(張りたい)ディレクトリに移動
3. ln コマンドの引数で、1. で確認したパスを『相対パス』で指定

の手順で作業すると、3.の手順時に相対パスを補完しながら指定できるので楽ちんです。具体的には

```
% cd /path/to/actual_file_dir
% pwd
/path/to/actual_file_dir
% cd /path/to/the_dir/to_place_symlink
% ln -s ../../actual_file_dir/actual_file symlink_name #<= ここで補完が使えて楽
```

実ファイルのファイル名とシンボリックリンクが同じ名前でいいときは、`ln -s` の第2引数は省略できるようです。

[40]: http://petitlyrics.com/kashi/137853/

[^01]: Git のサブモジュールとして取得したファイルに、2階層くらい上のディレクトリからシンボリックリンクを張りたいケースに遭遇しました