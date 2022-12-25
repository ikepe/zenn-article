# Zenn CLI

## 記事の書き方
1. MDファイルを作成する
  ```
  npx zenn new:article
  ```
2. 記事の設定を書く
  ```
  ---
  title: "記事タイトル" 
  emoji: "😸" # アイキャッチとして使われる絵文字（1文字だけ）
  type: "tech" # tech: 技術記事 / idea: アイデア記事
  topics: [] # タグ。["markdown", "rust", "aws"]のように指定する
  published: true # 公開設定（falseにすると下書き）
  ---
  ここから本文を書く
  ```
3. プレビューする
  ```
  npx zenn preview
  ```
4. 公開する
  記事設定のpublishedをtrueにすると公開

詳細は**Zenn CLIで記事・本を管理する方法**を確認する

* [📘 Zenn CLIで記事・本を管理する方法](https://zenn.dev/zenn/articles/zenn-cli-guide)
* [✏️ MD記法一覧](https://zenn.dev/zenn/articles/markdown-guide)