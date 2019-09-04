---
title: Nuxt.js で Yaml のコンテンツファイルを差し込む
categories:
  - Nuxt.js
tags: 
  - yaml 
date: 2018-10-02
is_open: true
summary: | 
  Nuxt.js に Yaml で作ったコンテンツファイルや設定ファイルを差し込む方法を紹介します。
---

## Nuxt.js で Yaml を利用できるようにする

Yaml を利用する場合、 `fs` でファイルを読み込んでYamlをパースする、といった方法もありますが、
Webpack が動作している Nuxt.js の場合、 `yaml-loader` を利用する方法が簡単です。

```bash
$ npm i yaml-loader
```

上記コマンドで `yaml-loader` をインストールすると、Webpack から Yaml ファイルをロードする事ができます。

コード内で Yaml ファイルをロードする場合は、次のように記述します。

```js
var content = require("json-loader!yaml-loader!./file.yml");
```

読み込むファイルのパス `./file.yml` の前に `json-loader!yaml-loader!` をつけることで、
該当のファイルが Yaml として読み込まれます。

`nuxt.config.js` 経由で Webpack の設定を `extend` する方法もありますが、
局所的な Yaml ファイルの利用であれば上記方法のほうが楽です。


## Yaml ファイルの上手な使い方

設定ファイルやコンテンツファイルをYaml で管理する場合、概ねその内容はそこそこの大きさになります。

Nuxt.js の場合、各 page コンポーネント内で require されたコンテンツは、
それぞれのページコンテンツとして配信されるため、 アプリケーショングローバルに全体で利用するデータについては、
Vuex Store に配置して利用するほうが効率的です。

Yaml ファイルから `topics` と `features`を取り出してアプリケーション全体で利用する場合、
下記のようなファイルを `store/index.js` に配置して Vuex Store に取り込みます。

```js
var content = require("json-loader!yaml-loader!./file.yml");

export const state = () => {
  return {
    topics: content.topics,    
    features: content.features    
  }
}
```

上記のコードは以下のように書くこともできます。

```js
var {topics,features} = require("json-loader!yaml-loader!./file.yml");

export const state = () => {
  return {
    topics,    
    features
  }
}
```

`state` の初期値に設定した値は、アプリケーションの初期ロードに組み込まれるため、
ページ遷移のたびに同じ内容をロードし直すといったようなことが起こりません。

アプリケーションの初期ロード に組み込むには大きすぎる内容や、
様々な箇所で個別に利用するような内容に関しては、 Yaml ファイルを json 化し、
static フォルダに配置することで API として利用するほうが良いでしょう。

