---
title: Nuxt.js のフロントエラーをrollbar で収集する
categories:
 - Nuxt.js
tags:
 - error handling
 - rollbar
date: 2018-03-31
summary: | 
  Nuxt.js で rollbar を使って フロントのエラーを収集する方法を紹介します。
---

## Nuxt.js Rollbar Modules

Rollbar は エラートラッキングのサービスで、システム上で発生したエラーを監視・記録することができます。

JavaScript のアプリケーションで発生したブラウザ上でのエラーは、
エラートラッキングツールを利用することで収集・監視することができるようになります。

Nuxt.js では Rollbar を利用するためのモジュールとして 
`nuxt-rollbar-module` が用意されているため、これを利用して Nuxt.js 上で発生したエラー情報をトラッキングしてみましょう。

モジュールのインストールは、`npm`経由で行います。

```bash
$ npm i nuxt-rollbar-module
```

モジュールを利用するため `nuxt.config.js` に以下のようにして設定を追加します。

```js
{
  modules: [
    // Simple usage
    'nuxt-rollbar-module',
 
    // With options
    ['nuxt-rollbar-module', {
      serverAccessToken: 'YOUR_ROLLBAR_SERVER_TOKEN',
      clientAccessToken: 'YOUR_ROLLBAR_CLIENT_TOKEN',
      config: {
        // Addtional config
      }
    }],
 ]
}
```

デフォルトでは、 環境変数 `ROLLBAR_CLIENT_KEY` `ROLLBAR_SERVER_KEY` を利用して、
Rollbar のトークンを指定することができます。

環境変数を利用しない場合は、上記のようにオプションで トークンを設定します。

モジュールを有効化すると、`this.rollbar` を利用して、エラーメッセージを Rollbar に送信できます。

```js
this.rollbar.debug('Yohyo!');
```

### エラーの自動報告

`nuxt-rollbar-module` は内部で  `vue-rollbar` を利用しているため、
 `vue-rollbar` は `rollbar` を利用しています。
 
このため、`nuxt-rollbar-module` でも、`rollbar` モジュールの持つ、エラーの自動報告機能を利用することが可能です。

https://docs.rollbar.com/docs/browser-js

https://docs.rollbar.com/docs/rollbarjs-configuration-reference#section-reference

上記のドキュメントにあるような Config の設定を `nuxt-rollbar-module` で利用する場合、
`nuxt.config.js` は以下のように記述します。

```js
{
  modules: [
    [ 'nuxt-rollbar-module',{
      config: {
        captureUncaught: true,
        captureUnhandledRejections: true,
        environment: process.env.NODE_ENV,
      }
    }]
  ]
}
```

`captureUncaught` を true にセットすることで、
ブラウザ側で処理されなかったエラーを自動的に Rollbar に送信することができます。
