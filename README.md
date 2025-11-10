# Qiita & Zenn Content Repository

このリポジトリは、Qiita と Zenn の記事を同一のワークスペースで管理・更新するためのコンテンツリポジトリです。Zenn 用のルートディレクトリと、Qiita CLI 用の `qiita/` ディレクトリに分かれており、どちらのプラットフォーム向けの記事もローカルで作成・プレビュー・公開できます。

## ディレクトリ構成

```
.
├─ articles/            # Zenn 記事 (Markdown)
├─ books/               # Zenn 本 (Markdown)
├─ qiita/               # Qiita CLI プロジェクト
│  ├─ public/           # 下書き・投稿用の Markdown
│  └─ qiita.config.json # Qiita CLI の設定
└─ README.md
```

## 必要要件

- Node.js 16 以上
- npm または yarn
- Qiita への投稿時に必要となる個人アクセストークン（`qiita login` で設定）

## セットアップ

1. リポジトリをクローンします。
2. Zenn 用の依存パッケージをインストールします。

   ```bash
   npm install
   ```

3. Qiita CLI 用の依存パッケージをインストールします。

   ```bash
   cd qiita
   npm install
   ```

## Zenn での記事作成・プレビュー

- 記事作成

  ```bash
  npx zenn new:article
  ```

- 本の作成

  ```bash
  npx zenn new:book
  ```

- ローカルプレビュー

  ```bash
  npx zenn preview
  ```

  ブラウザで `http://localhost:8000` を開き、記事や本の内容を確認します。

## Qiita での記事作成・プレビュー・投稿

Qiita CLI を使用する際は `qiita/` ディレクトリで作業します。

- Qiita へのログイン（初回のみ）

  ```bash
  cd qiita
  npx qiita login
  ```

- 記事のプレビュー

  ```bash
  npx qiita preview
  ```

- 記事の投稿

  ```bash
  npx qiita publish
  ```

  投稿時には Qiita CLI が投稿対象の記事を選択するプロンプトを表示します。

## 運用メモ

- 記事は Markdown で管理されます。Zenn と Qiita で共通化できる場合はファイルを共有し、片方専用の記事はそれぞれのディレクトリに配置してください。
- プレビューで確認した内容をコミットし、Pull Request でレビューしたうえで公開する運用を推奨します。

## 参考リンク

- [Zenn CLI 公式ドキュメント](https://zenn.dev/zenn/articles/zenn-cli-guide)
- [Qiita CLI 公式ドキュメント](https://github.com/increments/qiita-cli)
