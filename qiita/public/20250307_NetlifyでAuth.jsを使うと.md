---
title: 'NetlifyでAuth.jsを使うと「UntrustedHost: Host must be trusted」エラーが発生する問題'
tags:
  - Netlify
  - Authjs
private: false
updated_at: '2025-03-07T16:49:34+09:00'
id: 1d68cebe8715b9bf3cd1
organization_url_name: null
slide: false
ignorePublish: false
---

# 発生した現象

Netlify 上で Next.js + Auth.js（NextAuth.js）を使用しようとした際に、UntrustedHost: Host must be trusted というエラーに悩まされました。

ローカル環境では問題なく動作するのに、Netlify にデプロイするとエラーが発生していました。

# version

Next.js: 15.1.7
next-auth: ^5.0.0-beta.22,
Prisma: ^6.4.1
TypeScript: ^5.7.3

# エラー内容

```
[auth][error] UntrustedHost: Host must be trusted.
URL was: https://you-lift.netlify.app/api/auth/providers
```

このエラーの原因は、Auth.js（NextAuth.js）が Netlify のデプロイ先 URL を「信頼されたホスト」として認識していない ことにあります。
NEXTAUTH_URL は正しい URL を設定済みです...

# 解決策

Auth.js の`auth.ts`という設定ファイルで、trustHost: true を追加するだけで解決しました。

```ts
import NextAuth, { Session } from "next-auth";
import Google from "next-auth/providers/google";
import { PrismaAdapter } from "@auth/prisma-adapter";
import { prisma } from "../prisma";

export const authConfig = {
  adapter: PrismaAdapter(prisma),
  providers: [Google],
  pages: {
    signIn: "/login",
  },
  callbacks: {
    authorized: async ({ auth }: { auth: Session | null }) => {
      return !!auth;
    },
  },
  trustHost: true, // ← ここを追加！！
};

export const { handlers, signIn, signOut, auth } = NextAuth(authConfig);
```
