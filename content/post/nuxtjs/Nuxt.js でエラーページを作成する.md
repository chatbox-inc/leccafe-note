---
title: Nuxt.js で エラーページを作成する。
categories:
 - Nuxt.js
tags:
 - Error handling
date: 2018-10-11
summary: | 
  Nuxt.js でのエラーページの実装方法を紹介します。
---

## Nuxt.js でのエラーページ実装

Nuxt.js では、 JS処理でエラーが発生した際や、登録されていないルートへのアクセスが有った際に、
エラーのページが表示されます。

このエラーページをカスタマイズする際には、
`layouts/error.vue` ファイルを作成して、エラーページをカスタマイズします。

```html
<template>
  <div>
    <h1>ページが見つかりません</h1>
    <a href="/">ホーム</a>
  </div>
</template>
<script>
export default {
  props: ['error'],
  layout: 'blog' // エラーページ用のカスタムレイアウトを指定できます
}
</script>
```

通常静的サイトでは、十分なテストを行えばエラーに関しては、考えなくても良いので、
上記のように 404 想定のエラーページを用意すれば十分です。

spa などでは、JSのエラーも考慮する必要がありますが、
props 経由で、errorのオブジェクトを取得することが可能なため、以下のようにしてエラーメッセージを切り替えることが可能です。


```html
<h1 v-if="error.statusCode === 404">ページが見つかりません</h1>
<h1 v-else>エラーが発生しました</h1>
```

エラーページは、`layouts` 内部に配置しますが、 layout プロパティを指定して、別のレイアウトを流用することも可能です。

また上記のような形で エラーページをカスタマイズした場合、
開発中にエラー情報が見れなくなり、不便が有るかもしれません。
mounted のセクションに以下のようなコードを追加しておけば、開発モード時にのみ、
エラーの情報を ブラウザの console から閲覧可能です。

```js
export default {
  // ...
  mounted(){
    if(process.env.NODE_ENV !== 'production'){
      console.error(this.error)      
    }
  }
}
```

参考: https://ja.nuxtjs.org/guide/views/

## エラーページのジェネレート

`npm run generate` (`nuxt generate`) 実行時には、通常 `200.html` の名前で 404のページが排出されます。

Github Pages や Netlify を使用する場合、 404 のエラーページソースは `404.html` で認識されるため調整が必要です。

Nuxt.js では、 `nuxt.config.js` にて `generate.fallback` を `true` に設定することで 404 エラーページのアドレスを `404.html` に変更することができます。

```js
module.exports = {
  // ...
  generate: {
    fallback: true,
  }
}
```

エラーページの出力を任意の名前、例えば `error.html` に変更したい場合は、以下のように設定すると良いでしょう。

```js
module.exports = {
  // ...
  generate: {
    fallback: 'error.html',
  }
}
```
