---
layout: post
title: ".bashrc と .bash_profile の使い分けを工夫する"
date: 2013-11-25 00:00
comments: true
categories: 
---

工夫と言うほど凝ってもいませんが、はじめの一歩ということで。`.zshrc` と `.zprofile` の使い分けも同様に。

## .bashrc

bash の動作に関する設定を書くファイル。補完、色付け、履歴などなど。どのマシンでも共通。

## .bash_profile

bash 起動時に読み込まれる環境設定を書いておくファイル。`$PATH` を通したり独自の環境変数を定義したり。マシンごとに違う。

## 使い分けると嬉しいこと

1. cron を設定するときに嬉しい[^02]
2. ドットファイルをバージョン管理したいときに嬉しい[^01]
3. **Vim と同じ** と覚えると覚えやすい[^04]

最後のが一番嬉しいですね！！！！

### 小難しい話 ～ログインシェルと対話型シェル～

公式マニュアル読んでも、日本語解説読んでもピンとこない。

1. **対話型ログインシェル** のように、両者は共存できる[^03]
2. cron が使うシェルはそのどちらでもない(っぽい)

この辺はもうちょっと勉強してくる...続きはまた今度。

[^01]: profile 系のファイルを .gitignore に入れておくと、パスワードやシークレットキーを環境変数として .bash_profile に設定しても誤って GitHub とかにあげちゃうことにならずに済む

[^02]: `/bin/bash -l` とすると、良しなに環境変数を呼び出してくれる

[^03]: サーバーに SSH ログインすると出てくるシェルはまさにこのパターン

[^04]: Vim の動作に関する設定は vimrc に書くでしょ。Bash も同じだよ、と覚える