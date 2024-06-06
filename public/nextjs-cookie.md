---
title: NextjsでCookieを設定&ロードをする
tags:
  - cookie
  - Next.js
private: false
updated_at: '2024-06-06T16:18:40+09:00'
id: 112d858790a2dc2e47b5
organization_url_name: null
slide: false
ignorePublish: false
---

# なぜライブラリを使わないといけないのか?

NextjsはSSR(サーバーサイドレンダリング)ベースなので、基本的にサーバーでHTMLを生成してからそれをブラウザに転送します。そういった使用上、cookieはもちろんwindowやdocumentオブジェクトも通常では使用できません。

Nextjsのレンダリングについて
[Rendering - nextjs.org](https://nextjs.org/docs/pages/building-your-application/rendering)

# 今回使用するライブラリ

https://www.npmjs.com/package/cookies-next

## 基本的な使い方
今回はTypescript版のNextjsを使います  
```tsx
import { setCookie } from 'cookies-next';

setCookie('key', 'value', options);
```

setCookieという関数でセット、getCookieで取得します。optionsはCookieの使用可能時間などを設定します。

## 実装方法

これだけだとエラーになってしまうのでいろいろ追加して実装してみました。

```tsx
import { setCookie,getCookie } from 'cookies-next';
import { useEffect, useState } from "react";

export default function Cookie () {
    const [loginedData, setLoginedData] = useState<string>()
    useEffect(() => {
      // セット
      setCookie('key', 'value', {maxAge: 30 * 24 * 60 * 60});
      //　取得
      setLoginedData(getCookie('userids'));
  }, [])
  return (
    <>
      <p>{loginedData}</p>
    </>
  )
}
```

こんな感じで開発者コンソールでも見れます。
![スクリーンショット 2024-06-06 161442.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2081812/73338053-ffb6-c739-3512-d123c45ed700.png)

以上でーす。

# まとめ
こんな感じでツールを使わないとCookieが取れないのはNextjsのデメリットの一つですね💦

あとそのほかのSNSもよろしく(宣伝)

https://nknighta.github.io/
