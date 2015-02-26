# blog.harupong.com powered by Octopress

Moving my main blog `blog.harupong.com` from self-hosted wordpress to GitHub-posted Octopress.

メインのブログ `blog.harupong.com` をレンタルサーバー上の wordpress から GitHub 上の Octopress に引っ越し中｡

## TODO

1. Octopress とテーマの更新方法を書く
2. CIサービスを使った投稿の自動化方法を書く

## Initial setup 初期セットアップ
### Installing Octopress Octopress のインストール

	cd ~
	git clone git://github.com/imathis/octopress.git octopress
	cd octopress
	bundle install --path vendor/bundle

### Installing theme テーマの導入

	git clone git://github.com/alexgaribay/octoflat.git .themes/octoflat
	bundle exec rake install['octoflat']

### Miscellaneous settings 各種設定

	# GitHubの リポジトリを指定
	bundle exec rake setup_github_pages
		Enter the read/write url for your repository:
	git@github.com:harupong/harupong.github.io.git

	# Octopress の設定ファイル編集
	vi _config.yml 
		# octopress共通の設定を調整
		keep_files: ['README.md'] #この行だけ追加
		subscribe_rss: /feed/
		permalink: /:year/:month/:title/
		pagination_dir:
		twitter_user: harupong

		# octoflatテーマ用設定を追加
		navigation:
		  - text: Home
		  url: /
		  - text: Archives
		  url: /archives/
		  - text: Feed
		  url: /feed/

	# ブログのフィードとアーカイブ生成用のファイル作成
	mkdir source/feed/
	mv source/atom.xml source/feed/index.xml

	mv source/blog/archives/ source/archives/

	# 独自ドメイン用CNAME
	vi source/CNAME

	# 公開ファイル用readme
	vi source/README.md

## Posting and publishing 記事作成と投稿

	# 原稿貼り付け用ファイル生成(できたファイルに原稿を貼り付ける)
	bundle exec rake new_post

	# 投稿
	bundle exec rake gen_deploy

## Back up the source バックアップ

	# Octopress ディレクトリ(原稿含む)をGitHub にバックアップ
	git add -A ; git commit ; git push origin source
