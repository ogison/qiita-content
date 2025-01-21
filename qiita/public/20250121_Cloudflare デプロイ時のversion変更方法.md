---
title: Cloudflare デプロイ時のversion変更方法
tags:
  - Node.js
  - cloudflare
private: false
updated_at: '2025-01-21T09:52:09+09:00'
id: 433cfdebad24b4b25b7f
organization_url_name: null
slide: false
ignorePublish: false
---

# はじめに

Cloudflare のデプロイ時の言語やツールの version を変更する方法をまとめます。

:::note info
2025/01/21 時点の情報で説明します。
本記事の内容は執筆時点の情報をもとにしています。ソフトウェアやツールのバージョンは変更される可能性があるため、最新の公式ドキュメントを確認してください。（各章に、当時参考にした公式ドキュメントの URL を記載しています。）
:::

# デプロイ中に発生したエラー

Cloudflare のデプロイ時に使用されている言語やツールの version と実際に使用されている version が異なるため、以下のようなエラーが発生しました。

yarn.lock の version と Cloudflare のデプロイ時の yarn の version が異なることが起因のエラー

```
00:11:22.079	➤ YN0000: ┌ Post-resolution validation
00:11:22.079	➤ YN0028: │ The lockfile would have been modified by this install, which is explicitly forbidden.
00:11:22.080	➤ YN0000: └ Completed
00:11:22.080	➤ YN0000: Failed with errors in 12s 220ms
00:11:22.138	Error: Exit with error code: 1
00:11:22.138	    at ChildProcess.<anonymous> (/snapshot/dist/run-build.js)
00:11:22.138	    at Object.onceWrapper (node:events:652:26)
00:11:22.138	    at ChildProcess.emit (node:events:537:28)
00:11:22.138	    at ChildProcess._handle.onexit (node:internal/child_process:291:12)
00:11:22.146	Failed: build command exited with code: 1
```

noden の version が異なることが起因のエラー

```
00:30:17.085	warning tailwind-variants@0.1.20: The engine "pnpm" appears to be invalid.
00:30:17.086	error next@15.1.5: The engine "node" is incompatible with this module. Expected version "^18.18.0 || ^19.8.0 || >= 20.0.0". Got "18.17.1"
00:30:17.096	error Found incompatible module.
00:30:17.096	info Visit https://yarnpkg.com/en/docs/cli/install for documentation about this command.
00:30:17.128	Error: Exit with error code: 1
```

# version 変更方法

https://developers.cloudflare.com/pages/configuration/build-configuration/#environment-variables

Settings -> Variables and Secrets から version を指定する。
以下の画像のように、`NODE_VERSION`と`YARN_VERSION`を指定してあげて、ビルドをする。
![cloudflare_2.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2603981/e190d1e2-e28b-e6d8-e8fb-c1b66c0a71d6.png)

Settings の一番上に、デプロイ方法を`Production`か`Preview`のどちらかを指定する箇所があり、それぞれ version を指定してあげる必要があるので注意。
![cloudflare_3.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2603981/7f02fc02-2a44-bf76-d32d-fa3e12d2023e.png)

# 変更可能な言語とツール一覧

https://developers.cloudflare.com/pages/configuration/build-image/#supported-languages-and-tools

こちらのページを確認すると、サポートされている言語とツールが一覧化されてます。各言語とツールの Environment variable を指定してあげれば、version を変更することができます。

![cloudflare_1.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2603981/e3a508ce-ada0-327a-4cdf-8436046774db.png)
