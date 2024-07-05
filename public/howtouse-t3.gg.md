---
title: 【t3.gg】Nextjs向けテンプレツールのt3を使ってみた
tags:
  - 't3.gg'
  - 'Next.js'
  - 'Prisma'
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---

# まえがき
本当に使い始めで間違いが多いかもしれません。スマヌ。

# T3とは?

私も最近使い始めたばかりなのですが、Nextjsでテンプレートを作ってくれるツールみたいで、```create-next-app```や```create-react-app```と似たようなものっぽいです。
一番の違いとしては、NextjsからDBに接続するときに使う[Prisma](https://www.prisma.io/)や[Drizzle](https://orm.drizzle.team/)、さらにtrpcやTailwindCSSの設定も自動でやってくれます。

今までは手動で設定していたので環境構築だけでもかなり時間がかかっていたのですが、こういうツールがあると便利ですね。

# 使い方

```bash
yarn create t3-app
```
自分はyarnをいつも使っているのでこれでいきますが、npm,pnpm,bunにも対応しています。bunに対応しているのはすごいですね。bunはリリースしてからそんなに経っていない記憶。

実行するといくつか質問されるので答えます

質問は
- プロジェクト名
- Typescript or Javascript
- TailwindCSSを使うか
- tRPCを使うか
- NextAuth(認証用ライブラリ)を使うか
- ORMは何にするか
- AppRouterを使うか
- DBは何を使うか(Postgresなど)
- Gitの設定をするか
- yarn で実行するか(yarn createで実行したため)
- import文のエイリアス

![スクリーンショット 2024-07-05 141931.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2081812/c40ff7dc-8de9-088f-48ef-a1f730a1faf4.png)

作成できるとこんな感じで表示されます。

# テスト動作

起動してみますー。

と思ったんですが、テンプレそのままだとダメっぽい

StackoverFlowから

https://stackoverflow.com/questions/73526664/how-to-fix-the-invalid-environment-variables-error-in-t3-stack

上記では.envファイルの変数名を変更していますが、私の環境の場合は.envファイルの構成や配置場所をいろいろ工夫しないとダメでした。

具体的には、
- .env.exampleファイルを削除し.envのみに
- src/env.jsを変更

env.jsにかんしてはなぜかDiscordの変数読み込みをしている部分があったので消しました

```diff_javascript
import { createEnv } from "@t3-oss/env-nextjs";
import { z } from "zod";

export const env = createEnv({
  /**
   * Specify your server-side environment variables schema here. This way you can ensure the app
   * isn't built with invalid env vars.
   */
  server: {
    DATABASE_URL: z.string().url(),
    NODE_ENV: z
      .enum(["development", "test", "production"])
      .default("development"),
    NEXTAUTH_SECRET:
      process.env.NODE_ENV === "production"
        ? z.string()
        : z.string().optional(),
    NEXTAUTH_URL: z.preprocess(
      // This makes Vercel deployments not fail if you don't set NEXTAUTH_URL
      // Since NextAuth.js automatically uses the VERCEL_URL if present.
      (str) => process.env.VERCEL_URL ?? str,
      // VERCEL_URL doesn't include `https` so it cant be validated as a URL
      process.env.VERCEL ? z.string() : z.string().url()
    ),
-    DISCORD_CLIENT_ID: z.string(),
-   DISCORD_CLIENT_SECRET: z.string(),
  },

  /**
   * Specify your client-side environment variables schema here. This way you can ensure the app
   * isn't built with invalid env vars. To expose them to the client, prefix them with
   * `NEXT_PUBLIC_`.
   */
  client: {
    // NEXT_PUBLIC_CLIENTVAR: z.string(),
  },

  /**
   * You can't destruct `process.env` as a regular object in the Next.js edge runtimes (e.g.
   * middlewares) or client-side so we need to destruct manually.
   */
  runtimeEnv: {
    DATABASE_URL: process.env.DATABASE_URL,
    NODE_ENV: process.env.NODE_ENV,
    NEXTAUTH_SECRET: process.env.NEXTAUTH_SECRET,
    NEXTAUTH_URL: process.env.NEXTAUTH_URL,
-    DISCORD_CLIENT_ID: process.env.DISCORD_CLIENT_ID,
-   DISCORD_CLIENT_SECRET: process.env.DISCORD_CLIENT_SECRET,
  },
  /**
   * Run `build` or `dev` with `SKIP_ENV_VALIDATION` to skip env validation. This is especially
   * useful for Docker builds.
   */
  skipValidation: !!process.env.SKIP_ENV_VALIDATION,
  /**
   * Makes it so that empty strings are treated as undefined. `SOME_VAR: z.string()` and
   * `SOME_VAR=''` will throw an error.
   */
  emptyStringAsUndefined: true,
});

```

起動後はいつもの通り http://localhost:3000/　オープンです

開くとこんな感じ。

![スクリーンショット 2024-07-05 174246.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2081812/12bf260b-6d29-363b-bc4b-c6141c21f4f3.png)

Nextjsとおんなじ感じですね

# あとがき
t3は結構がっつりフルスタックでやる人向けのツールっぽいです。ただめちゃ便利なので今後はガンガン活用したいですね。


# 参考サイト

### 公式
https://create.t3.gg/

https://t3.gg/


### 開発者(?)のYoutube

https://www.youtube.com/@t3dotgg

https://github.com/t3-oss/create-t3-app
