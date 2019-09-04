---
title: Vuex のデータをローカルストレージに格納する
categories:
 - Nuxt.js
date: 2018-09-10
summary: | 
  Vue.jsアプリケーションのデータをシンプルに localStorage に格納してみましょう。
---

## localStorage の利用

APIのキャッシュや認証情報など、アプリケーション上で利用するデータを、
ブラウザ内部に保存したい場合、 localStorage を利用するのが一般的です。

localStorage は `localStorage.get()` や `localStorage.set()` 等のブラウザAPI を利用して
簡単に利用することができますが、オブジェクトの格納時に JSON 化したり、
Vuex のデータを保存する場合、 mutation やデータ初期化にフックしたりと、
何かと実装が面倒です。

Vuex を利用したアプリケーションでストアの情報を localStorage に格納する場合、
`vuex-persistedstate` を利用することで任意のStore情報を自動的に localStorage に連携してくれます。

### vuex-persistedstate の使い方

`vuex-persistedstate` を利用するには、まず npm 経由でライブラリをインストールします。

```bash
$ npm i vuex-persistedstate
```

Vuex で利用する場合、Vuex のプラグインとして記述すればOKです。

```js
import createPersistedState from 'vuex-persistedstate'

const store = new Vuex.Store({
  // ...
  plugins: [createPersistedState()],
})
```

また Nuxt.js で利用する場合には、 Nuxt.js にプラグインを記述することで利用が可能になります。

`vuex-persistedstate` は localStorage を利用するため、 SSR フェーズで利用されないよう、
`ssr: false`のフラグを忘れず指定してください。

```js
// nuxt.config.js
module.exports = {
  //...
  plugins: [{ src: '~/plugins/localStorage.js', ssr: false }],
  //...
}
```

```
// ~/plugins/localStorage.js

import createPersistedState from 'vuex-persistedstate'

export default ({store}) => {
  createPersistedState({})(store)
}
```

### vuex-persistedstateのカスタマイズ

`createPersistedState` 関数にオプションを渡すことで、`vuex-persistedstate`の挙動をカスタマイズできます。

```js
createPersistedState({
    key: 'yourkey',
    paths: [ '...' ]
    // ...
})
```

`key` は localStorage で使用するキー名です。デフォルトは `vuex` になります。

`paths` はlocalStorage に格納する対象のstate のパスです。 
Vuex モジュール名を指定してモジュール単位での格納も可能です。
何も指定しない(空の配列)場合は、全ての state を localStorage に格納します。

## Tips 

## 

Nuxt.js で`vuex-persistedstate` 経由で再生したStore情報をもとに、
ページのレンダリングを行った場合、以下のようなエラーが表示されるケースがあります。

```text

```

Vuex の プラグインを自分で記述して、localStorege から Vuex の初期データを注した際にも
同じような問題に直面します。・

対応策としては該当箇所のHTML記述を `no-ssr` コンポーネントで囲むことで
SSRフェーズでのHTML生成を無効化するといった方法があります。

また、別の解決策として、`vuex-persistedstate` による localStorage の初期化タイミングを遅らせるという方法もあります。 

```
window.onNuxtReady(()=>{

})
```

しかし、この方法では mounted のタイミングで vuex 上からデータを参照することができず、不便なケースも多いでしょう。

該当の問題は以下のIssue でも詳細に触れられています。

https://github.com/robinvdvleuten/vuex-persistedstate/issues/54

　
