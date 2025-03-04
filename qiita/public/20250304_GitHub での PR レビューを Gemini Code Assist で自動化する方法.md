---
title: GitHub での PR レビューを Gemini Code Assist で自動化する方法
tags:
  - pr
  - Gemini
  - GeminiCodeAssist
private: false
updated_at: "2025-03-04T12:49:48+09:00"
id: 7f75e1091e730a9ccac6
organization_url_name: null
slide: false
ignorePublish: false
---

# はじめに

Gemini Code Assist を使って PR の自動レビューを試してみました！

![スクリーンショット 2025-03-04 111231.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2603981/3fac3210-4552-47b3-90d7-878b6c5ff759.png)

# PR レビュー タイミング

PR レビューは基本、**自動**で行われます。新しい PR を作成した時点で、Gemini Code Assist が最初のレビューを実行します。

手動でレビューを実行したい場合は、Gemini のコマンドを PR のコメントに入力することで実行できます。
![スクリーンショット 2025-03-04 111944.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2603981/83bd003c-186d-4d23-bf21-1bec39d02d67.png)

### コマンド一覧

| コマンド          | 説明                                           |
| ----------------- | ---------------------------------------------- |
| `/gemini summary` | pull リクエストの変更の概要を投稿              |
| `/gemini review`  | pull リクエストの変更のコードレビューを投稿    |
| `/gemini`         | Gemini Code Assist を手動で呼ぶ                |
| `/gemini help`    | 使用可能なコマンドの概要（この表の説明が表示） |

# PR レビュー 設定

`.gemini/`フォルダに、 Gemini Code Assist の構成ファイル を配置することで、PR レビューの挙動をカスタマイズできます。
![スクリーンショット 2025-03-04 121303.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2603981/d43af1d7-6eec-409e-a942-7c4c72e3eed9.png)
構成ファイルには以下の 2 つがあります。

config.yaml：Gemini の設定（有効/無効、レビューの重大度など）

styleguide.md：コードレビューのルール（言語、スタイルガイドなど）

## config.yaml

config.yaml では、PR レビューの挙動や、新規 PR 作成時の Gemini の動作を設定できます。

```config.yaml
have_fun: true
code_review:
  disable: false
  comment_severity_threshold: MEDIUM
  max_review_comments: -1
  pull_request_opened:
    help: false
    summary: true
    code_review: true
```

### プロパティの詳細

| プロパティ                     | 型        | 説明                                                                                     | デフォルト値  |
| ------------------------------ | --------- | ---------------------------------------------------------------------------------------- | ------------- |
| `have_fun`                     | `boolean` | PR の概要に詩などの楽しい要素を追加するかどうか。。                                      | `true`        |
| `code_review`                  | `object`  | コードレビューの設定。                                                                   | `-`           |
| ├ `disable`                    | `boolean` | Gemini が PR のコードレビューを実施するかどうか。。                                      | `false`       |
| ├ `comment_severity_threshold` | `string`  | レビューコメントの最小重大度を指定。。（`UNKNOWN`, `LOW`, `MEDIUM`, `HIGH`, `CRITICAL`） | `MEDIUM`      |
| ├ `max_review_comments`        | `integer` | 投稿されるコードレビューの最大コメント数を指定。`-1` を指定すると無制限になる。          | `-1` (無制限) |
| ├ `pull_request_opened`        | `object`  | プルリクエスト作成時の Gemini の動作を制御。                                             | `-`           |
| │ ├ `help`                     | `boolean` | プルリクエスト作成時に、Gemini のヘルプメッセージを投稿するかどうか。                    | `false`       |
| │ ├ `summary`                  | `boolean` | プルリクエスト作成時に、概要を投稿するかどうか。                                         | `true`        |
| │ ├ `code_review`              | `boolean` | プルリクエスト作成時に、Gemini によるコードレビューを実行するかどうか。                  | `true`        |

## styleguide.md

styleguide.md に コードレビューのルール を記述することで、Gemini の挙動をカスタマイズできます。

### 設定例：日本語でレビューを行う

```styleguide.md
Posts a code review in Japanese language.
```

このように記述すると、Gemini が レビューコメントを日本語で投稿 するようになります。

他にも、スタイルガイド（命名規則、コードフォーマットのルールなど）を記述することで、Gemini に合わせたコードレビューを実施できます。

# まとめ

Gemini Code Assist を導入することで、レビューの重要度を調節したり、カスタムルールを適用させて、自動コードレビューすることが可能です！

カスタムルールのアイデアによっては、新しい使い方ができるのかなと感じたので、いろいろ試してみたいです！

（↓ の記事とかおもしろそう）

https://medium.com/google-cloud/more-enjoyable-code-reviews-with-gemini-85ca661b843d
