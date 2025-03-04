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
