---
title: v-html にマークダウン文字列を挿入する
slug: markdown_on_vhtml
categories:
 - Vue
summary: | 
  カスタムディレクティブを使って、v-htmlにMD文字列を渡す方法の紹介 
---

画面に md を挿入する際など、 v-html で 共通の関数を利用したいケースは多々あると思います。

Vue.js における値の変換では、 filter を利用するのが一般的で以下のようにして簡単に fileter 関数を定義するっことができます。

```js
Vue.filter('md', function (value) {
    if (!value) return ''
    return marked(value)
})
```


しかし公式ドキュメントにもあるように、 filter は
mustache 展開と v-bind 式、2 つの場所でのみ使用できます。

[フィルター — Vue.js](https://jp.vuejs.org/v2/guide/filters.html)

v-html で filter を利用するには、以下の Qiita 記事のように、 `v-html="$options.filters.capitalize(message)"` と記述して、`$options` 経由での filters を参照しなければならず、やや仕様が困難です。

[v-htmlでfilterを使いたい - Qiita](https://qiita.com/pokkur/items/e6c7b20852471a9eca29)

このようなケースでは filter を利用するよりも、 カスタムのディレクティブを作成する方が便利なケースもあります。

画面描画時に 指定されたマークダウン文字列を処理するディレクティブ `v-md` を作成するは以下のようなコードを記述すればOKです。

```js
Vue.directive('md', {
    bind: (el, binding) => {
        el.innerHTML = marked(binding.value)
    }
})
```

ディレクティブの作成はやや複雑ですが、バインド時の処理のみ記述する程度であれば、比較的かんたんに記述することができます。
詳細なカスタムディレクティブの作成については公式ドキュメントを参考にしてください。

[カスタムディレクティブ — Vue.js](https://jp.vuejs.org/v2/guide/custom-directive.html)
