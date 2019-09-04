---
title: Nuxt.js 2.0 の変更点を確認してみる
slug: vue-smooth-scroll
categories:
 - Nuxt.js
tags:
 - Auth Module
date: 2018-10-07
is_open: true
summary: | 
  先日リリースされたNuxt2 の機能の一部を紹介します。
---

## Nuxt.js 2.0

先日9月に Nuxt.js 2 がリリースされました。

1.0 との互換性は維持されつつ、様々な機能追加・改良が行われたとのことなので、その機能の一部を確認してみましょう。

## 気になった変更点

変更点の一覧は 以下の日本語ブログでの紹介や、公式のリリースノート等で確認できます。

http://studio-andy.hatenablog.com/entry/nuxt-v2

https://github.com/nuxt/nuxt.js/releases/tag/v2.0.0

ここでは、多数ある変更点の中でも気になったものを中心に紹介していきます。

### npx 経由でのテンプレート作成が可能に

```bash
$ npx create-nuxt-app <project-name>
```

https://nuxtjs.org/guide/installation

### pageComponentに `watchQuery` が追加

`query` の変更時に watchQuery が発動するようになりました。

spa などのプリレンダリング挙動では、generate 時にのみ実行される fetch 等でしたがこれでブラウザサイドでも実行されるようになります。

`mounted` が Query の変更時に実施される、というわけではないので注意は必要です。

### Webpack4 に対応

これにより以下のような恩恵が得られるようです。

- ビルドが早くなった
- `mjs` のサポートに対応

中でも mjs のサポートは大きく、
`import/export` の ESM 記法で作成したモジュールが、 
`nuxt.config.js` でも使えるようになります。

なお、`mjs` 形式のモジュールを作成する場合には、 `process.env` 周りとのエラーの兼ね合いに注意しなければなりません。

`webpack4` と `ProvidePlugin` との関係で、`mjs` ファイル内で `process.env` を利用すると、
ブラウザ側で以下のようなエラーが発生するようになりました。

```text
require is not defined
```

詳しくは 以下の Issue を参考にしてください。

https://github.com/webpack/webpack/issues/7032

対応策としては とりあえず `process.env` の内容を 外部から注入できる形でコードを改善するのがよいでしょう。

`.vue`や`.js`などの通常のファイルでの　`process.env` は今まで通り利用可能です。

