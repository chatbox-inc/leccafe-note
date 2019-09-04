---
title: axios モジュールの使い方
categories:
 - Nuxt.js
tags:
 - Nuxt.js
 - axios
date: 2018-12-20
summary: | 
  Axios モジュールで Nuxt.js アプリケーションの REST 発行周りを管理する手法を紹介します。
---


公式ドキュメントはこちら

[Introduction - Axios Module](https://axios.nuxtjs.org/)

## Install 

```bash
$ npm  install @nuxtjs/axios
```

```js
module.exports =  {

 modules:  [
   '@nuxtjs/axios',
 ],
 axios:  {
   // 何か設定があればこのセクションに書き込みます
 }
}
```

## Usage

`.vue` コンポーネントや Vuex Store Actions の内部では `this.$axios` の形式でアクセスすることが可能です。

```js
methods: {
  async fetchSomething() {
    const ip = await this.$axios.$get('http://icanhazip.com')
    this.ip = ip
  }
}
```


```
async asyncData({ $axios }) {
  const ip = await $axios.$get('http://icanhazip.com')
  return { ip }
}
```

## Configuration

axios モジュールでは アプリケーション内の axios の設定を `nuxt.config.js` の axios セクションにまとめる事ができます。

もちろん、個別のリクエストごとに `this.$axios.$get('URL',  options)` のような形で、オプションを指定することも可能です。

### `baseURL` `browserBaseURL`

`baseURL` は SSR フェーズでAPIが発行される際に利用される APIの base URL で、
`browserBaseURL` は ブラウザ上でAPIが発行される際に利用される APIの base URL です。

`browserBaseURL` のデフォルト値は`baseURL` となっているため、SSR と ブラウザでAPIの使い分けをしない場合に、
二重に設定する必要はありません。


### `retry`

APIのリクエストが失敗した際に、任意の回数API発行をリトライすることができます。
`true` をセットした際には 3 回のリトライが行われ、任意の回数リトライ指定を行う場合は、数値で設定します。
デフォルトの値は `false` でAPI発行エラー時のリトライを行いません。

### `debug`

`true` にセットすることで、 request と response のログが発行されます。
デフォルトの値は `false` です。

[Options - Axios Module](https://axios.nuxtjs.org/options)

## 環境変数による設定の切り替え

axios モジュールの機能の一つとして、環境変数を用いた 設定の切り替えがあります。

- `API_URL` : `baseURL` の値を上書きします。
- `API_URL_BROWSER` : `browserBaseURL` の値を上書きします。



