---
title: Gemini Code Assist
tags:
  - "Gemini Code Assist"
  - "Gemini"
  - "PR"
private: false
updated_at: ""
id: null
organization_url_name: null
slide: false
ignorePublish: false
---

# はじめに

Gemini Code Assist で PR レビューやってみたかったので、導入してみました！

https://cloud.google.com/blog/ja/topics/developers-practitioners/gemini-code-assist

![スクリーンショット 2025-03-04 111231.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2603981/3fac3210-4552-47b3-90d7-878b6c5ff759.png)

# PR レビュー タイミング

PR レビューは基本、自動で行われます。新しい PR を作成した段階で、Gemini Code Assist が最初のレビューを実施します。

手動で行いたい時は、gemini 特有のコマンドを PR のコメントに入力すれば行うことができます。
![スクリーンショット 2025-03-04 111944.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2603981/83bd003c-186d-4d23-bf21-1bec39d02d67.png)

コマンド一覧は以下になります。

| コマンド          | 説明                                           |
| ----------------- | ---------------------------------------------- |
| `/gemini summary` | pull リクエストの変更の概要を投稿              |
| `/gemini review`  | pull リクエストの変更のコードレビューを投稿    |
| `/gemini`         | Gemini Code Assist を手動で呼ぶ                |
| `/gemini help`    | 使用可能なコマンドの概要（この表の説明が表示） |

# PR レビュー 設定

`.gemini/`フォルダに、 Gemini Code Assist の構成ファイルを配置することで、PR レビューの設定を変更できます。
構成ファイルは 2 つ存在していて、
config.yaml：設定の有効無効を変更
styldguide.md：コードレビュー実行時に特定のルールを設定できます。

| プロパティ                     | 型        | 説明                                                                                         | デフォルト値  |
| ------------------------------ | --------- | -------------------------------------------------------------------------------------------- | ------------- |
| `have_fun`                     | `boolean` | プルリクエストの概要に詩などの楽しい要素を追加するかを設定します。                           | `true`        |
| `code_review`                  | `object`  | コードレビューの設定をまとめるオブジェクトです。                                             | `-`           |
| ├ `disable`                    | `boolean` | Gemini がプルリクエストのコードレビューを実施するかどうかを制御します。                      | `false`       |
| ├ `comment_severity_threshold` | `string`  | レビューコメントの最小重大度を指定します。（`UNKNOWN`, `LOW`, `MEDIUM`, `HIGH`, `CRITICAL`） | `MEDIUM`      |
| ├ `max_review_comments`        | `integer` | 投稿されるコードレビューの最大コメント数を指定します。`-1` を指定すると無制限になります。    | `-1` (無制限) |
| ├ `pull_request_opened`        | `object`  | プルリクエストが開かれた際の Gemini の動作を制御します。                                     | `-`           |
| │ ├ `help`                     | `boolean` | プルリクエスト作成時に、Gemini のヘルプメッセージを投稿するかどうかを設定します。            | `false`       |
| │ ├ `summary`                  | `boolean` | プルリクエスト作成時に、概要を投稿するかどうかを設定します。                                 | `true`        |
| │ ├ `code_review`              | `boolean` | プルリクエスト作成時に、Gemini によるコードレビューを実行するかを設定します。                | `true`        |
