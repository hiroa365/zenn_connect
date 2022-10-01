---
title: GitHubとZennを連携する手順
emoji: "📚"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [GitHub,Zenn]
published: false
---


## GitHubとZennを連携する手順

1. GitHubにzennと連携するリポジトリを作成
1. Zennにログインし、[GitHub連携](https://zenn.dev/dashboard/deploys)を開き、「リポジトリを連携するボタン」 ＞ 「連携へ進む」ボタンを押す
![](/images/zenn_github_connect/20221001130028.png)

1. GitHubの認証画面で、「Only select repositories」を選択し、連携するリポジトリを選び、「Install & Authorize」ボタンを押します。環境にもよるとは思いますが、自分はパスワード入力の必要がありました。
![](/images/zenn_github_connect/20221001130620.png)

1. GitHubの認証が完了すると、[GitHub連携](https://zenn.dev/dashboard/deploys)のページが以下のようになります。
![](/images/zenn_github_connect/20221001132007.png)

mainブランチにファイルをpushすれば、投稿が出来るようになるようです。


## Zenn CLIをインストールする

CLIを使うことで、記事を作成したり、プレビューを見たりできるようです。

+ [Node.js](https://nodejs.org/ja/)をインストール
+ Zenn CLIをインストール
  + Zennと連携しているリポジトリに移動して、以下のコマンドを入力します。
```
> npm init --yes
> npm install zenn-cli
```
以下のコマンド入力で記事の作成が出来ます。
```
> npx zenn init

  🎉  Done!
  早速コンテンツを作成しましょう

  👇  新しい記事を作成する
  $ zenn new:article

  $ zenn new:book

  👇  投稿をプレビューする
  $ zenn preview
```

ヘルプ表示もできます。
```
> npx zenn --help

Command:
  zenn init           コンテンツ管理用のディレクトリを作成. 初回のみ実行
  zenn preview        コンテンツをブラウザでプレビュー
  zenn new:article    新しい記事を追加
  zenn new:book       新しい本を追加
  zenn list:articles  記事の一覧を表示
  zenn list:books     本の一覧を表示
  zenn --version, -v  zenn-cliのバージョンを表示
  zenn --help, -h     ヘルプ

  👇  詳細
  https://zenn.dev/zenn/articles/zenn-cli-guide
```
各コマンドのヘルプも確認できました。
```
> npx zenn new:article　--help

Command:
  zenn new:article  新しい記事を追加

Usage:
  npx zenn new:article [options]

Options:
  --slug      SLUG     記事のスラッグ. `a-z0-9`とハイフン(`-`)とアンダースコア(`_`)の12〜50字の組み合わせ
  --title     TITLE    記事のタイトル
  --type      TYPE     記事のタイプ. tech (技術記事) / idea (アイデア記事) のどちらかから選択
  --emoji     EMOJI    アイキャッチとして使われる絵文字（1文字だけ）
  --published          公開設定. true か false を指定する. デフォルトで"false"
  --publication-name   Publication名. Zenn Publication に紐付ける場合のみ指定
  --machine-readable   作成成功時にファイル名のみを出力する

  --help, -h       このヘルプを表示

Example:
  npx zenn new:article --slug enjoy-zenn-with-client --title タイトル --type idea --emoji ✨

  👇  詳細
  https://zenn.dev/zenn/articles/zenn-cli-guide
```

## 記事を作成する手順

スラッグ付きの記事も簡単に作成可能です。本記事は以下のコマンドで作成しています。

```
npx zenn new:article --slug zenn_github_connect
```

画像を表示する場合は、リポジトリ直下に、/images ディレクトリを作成してファイルを配置します。
/images ディレクトリ配下の構造は自由なようです。
ファイル名を日時にしていますが、ファイル名に関しても自由なようです。
![](/images/zenn_github_connect/20221001161535.png)

画像の指定方法は以下となります。
```
![](images/zenn_github_connect/20221001161535.png)
```

## プレビューの確認方法

プレビューは以下のコマンドで可能です。
```
npx zenn preview
```
プレビューすると、http://localhost:8000/ にプレビューが表示されます。

ファイルを保存すると、プレビューもホットリロードで更新されるのでプレビューを見ながら記事を書くことが可能です。

ただ、プレビュー表示中はコンソールを握られた状態になるので、プレビュー表示用と、git操作用の2つのコンソールを立ち上げています。



## 参考

https://zenn.dev/zenn/articles/connect-to-github

https://zenn.dev/zenn/articles/install-zenn-cli

https://zenn.dev/zenn/articles/zenn-cli-guide


