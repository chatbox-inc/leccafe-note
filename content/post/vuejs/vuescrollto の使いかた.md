---
title: VueScrollTo の使いかた
categories:
 - Vue
date: 2019-01-09
is_open: true
summary:   
  Vue.js でスクロール処理をするための モジュール vue scrollTo の紹介です。
---

## Vue ScrollTo 

Vue ScrollTo は Vue.js で利用する スクロール制御のためのモジュールです。

一般的に利用される Vue Smooth Scroll に比べ様々なオプションが利用可能な他、
`Vue.use` を利用しなくても その機能を利用できるため、
Nuxt.js で利用する際の プラグインを記述する必要がない、などの利便性があります。

### Vue ScrollTo の使いかた

Vue.js / Nuxt.js ともに NPM で インストールするだけで利用可能です。

```bash
$ npm install vue-scrollto
```

スクロール実行する際には、 import して 利用可能です。

```js
import VueScrollTo from 'vue-scrollto'
 
export default {
  methods: {
    scroll() {
      const cancelScroll = VueScrollTo.scrollTo(element, duration, options)
    }
  }
}
```

element には JavaScript の DOM のほか、`querySelector` の引数として利用可能な
クエリ文字列が利用可能です。 

duration は ミリ秒単位の数値でスクロール時間を指定します(default は `500`)

options には オブジェクトの形式で、以下のオプション設定が利用可能です。

- `options.offset` ヘッダ分など スクロールにズレを設定したい場合に数値で指定します。
- `options.onStart` スクロール開始時のコールバック関数を設定します。
- `options.onDone` スクロール完了時のコールバック関数を設定します。
- `options.onCancel` スクロールキャンセル時のコールバック関数を設定します。

戻り値は 関数で、コールすることでスクロール処理をキャンセルすることができます。

詳しいオプションの全容は公式の資料を確認してください。

[https://www.npmjs.com/package/vue-scrollto#options](https://www.npmjs.com/package/vue-scrollto#options)

### デフォルトの設定

`VueScrollTo.setDefaults` を用いることでデフォルトの オプション値をまとめて設定する事ができます。

```js
VueScrollTo.setDefaults({
  ...
})
```

### ディレクティブとしての利用

`v-scroll-to` ディレクティブを利用して スクロール処理を記述することもできます。

```html
<a href="#" v-scroll-to="'#element'">Scroll to #element</a>
 
<div id="element">
    Hi. I'm #element.
</div>
```

`import`しなくても、 `this` からの参照でモジュールを利用することも可能です。

```js
export default {
  methods: {
    scroll() {
      cancelScroll = this.$scrollTo(element, duration, options)
    }
  }
}
```
