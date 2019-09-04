---
title: Nuxt.js に Bootstrap を追加する
categories:
 - Nuxt.js
date: 2018-10-07
summary: | 
  Nuxt.js に Bootstrap を追加する方法をいくつか紹介します。
---

## bootstrap-vue を利用する

最も簡単な方法は bootstrap-vue を利用する方法です。

利用方法は、 bootstrap-vue をインストールして、 `nuxt.config.js` の `modules` セクションに追記するだけです。

```bash
$ npm i bootstrap-vue
```

```js
{
  modules: [
    'bootstrap-vue/nuxt',
  ]
}
```

bootstrap-vue は 通常の bootstrap のCSS 定義に加え、
jQuery 周りのコンポーネントが、Nuxt.js 上でも動作するような FIX が加わったモジュールです。

通常の bootstrap class を用いたコーディングが行えるほか、
`b-` 系の専用のコンポーネントでコーディングを進めることができます。

## CSS として追加する

bootstrap-vue の `b-` 系コンポーネントを利用したくない場合は、
通常の bootstrap を導入するのがおすすめです。

Bootstrap を単純にCSS として追加する場合、CSS を `nuxt.config.js` の `head` に追加します。

```js
{
    link: [
      { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' }
      { rel: 'stylesheet', href: 'https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css' }
    ]  
}
```

CDN を利用せず `static` ディレクトリ以下にBootstrapを展開する場合、
以下のような記述になります。

```js
{
    link: [
      { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' }
      { rel: 'stylesheet', href: '/css/bootstrap/4.1.3/css/bootstrap.min.css' }
    ]
}
```

## SCSS として追加する
 
Nuxt.js で SCSS を利用する場合は、追加モジュールのインストールが必要です。

```bash
$ npm i node-sass sass-loader
```

SCSS で Bootstrap を利用する場合、`npm i bootstrap` でBootstrap をインストールしてから、
`assets/scss/common.scss` を作成して以下のように記述します。

```scss
@import "~~/node_modules/bootstrap/scss/bootstrap.scss";
```

`~~` は Project Rootを表す Webpack エイリアスです。

`~~` は srcDir を示す `~` と同じ値になるケースがほとんどですが、 
srcDir を変更した際には、これらの値は変わってきます。

SCSS が用意できたら、nuxt.config.js に以下の記述を追加して準備は完了です。

```js
{
  css: [
    "~assets/scss/common.scss"
  ],  
}
```

この方法で Bootstrap を導入する場合、 SCSS を利用したカスタマイズや、
`bootstrap-grid.scss` `bootstrap-reboot.scss` による、部分的な導入も可能になります。
