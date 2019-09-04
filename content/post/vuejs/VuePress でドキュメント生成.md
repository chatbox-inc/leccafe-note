---
title: VuePressでドキュメントを生成する
categories:
 - Vue
date: 2019-02-12
summary: | 
  Vue.js 製のドキュメント生成ツール VuePress を使ってドキュメントサイトを作成してみましょう。
---

## VuePress 

VuePress は Vue.js 製の静的サイトジェネレータです。

Nuxt.js よりもシンプルに テーマやプラグインの機能を援用しながらドキュメントサイトを構築する事ができます。

執筆時点の現在では、v1.0 系列が アルファでリリースされており、公式のドキュメントも この アルファの v1.0 系列の機能がベースとなっています。

[Introduction \| VuePress](https://vuepress.vuejs.org/guide/)

## Install

VuePress は特定のテンプレート構造を必要とせず、任意のディレクトリ内に簡単にインストールすることができます。

v1 系列の VuePress をインストールするには `@next` を付与して yarn を用いてインストールする必要があります。

```bash
$ yarn add -D vuepress@next
```

一般的なフレームワークが、プロジェクトルートに特定のフォルダ構成を要求するのに対し、
VuePress は任意のフォルダ内でドキュメントを展開する事ができます。

例えば ドキュメントに `docs` というフォルダを利用する場合、`package.json` に以下のようなかたちで `scripts` を定義しましょう。

```json
{
  "scripts": {
    "docs:dev": "vuepress dev docs",
    "docs:build": "vuepress build docs"
  }
}
```

試しに `docs` フォルダを作成して以下の様な内容で `docs/README.md` を作成してみましょう。

`npm run docs:dev` を実行することでテストサーバが起動し、ドキュメントサイトが生成されるのを確認することができます。

## VuePress のディレクトリ構成

VuePress のドキュメントフォルダに、任意のフォルダ、ファイル名で md ファイルを置けば、
自動的にその パス名を URL としたドキュメントページが生成されます。

`README.md` ファイルは `index.html` のように振る舞います。

```text
├── docs
│   ├── README.md
│   ├── guide
│   │   └── README.md
│   └── config.md
└── package.json
```

上記の構成では `/` `/guide` `/config` の ３ページが生成されます。

また、ドキュメントのフォルダに `.vuepress` というフォルダを置くことで、サイトに設定を加える事ができます。

```
├── docs
│   └── .vuepress 
│       ├── public 
│       └── config.js 
└── package.json
```

`public` フォルダは画像やフォントなどサイトの静的なファイルを配置する箇所です。`public` フォルダに設置されたファイル群は、ビルド時にサイトルートに展開されます。

`config.js` は VuePress の設定ファイルで、サイトの詳細な設定を行うことができるようになっています。

https://vuepress.vuejs.org/config/

