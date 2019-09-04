---
title: Vue.js アプリケーションのサイト内スクロール制御
categories:
 - Vue
date: 2018-10-02
summary: | 
  vue smooth scroll プラグインを利用して Vue.js アプリケーションにサイト内スクロールの制御をいれる方法を紹介します。

---

## Netlify の デプロイ設定

```bash
$ npm i vue-smooth-scroll
```

```js
import vueSmoothScroll from 'vue-smooth-scroll'
Vue.use(vueSmoothScroll)
```


Usage
<a href="#div-id" v-smooth-scroll>Anchor</a> 
<div id="div-id"></div> 
Custom options
Defaults
  {
    duration: 500, // Animation speed
    offset: 0, // Offset from element, you can use positive or negative values
  }
Example:
<a href="#div-id" v-smooth-scroll="{ duration: 1000, offset: -50 }">Anchor</a> 
<div id="div-id"></div> 
Without Browserify or Webpack
<body>
  <div id="app">
    <a href="#app" v-smooth-scroll>Anchor</a>
  </div>
  <script src="https://unpkg.com/vue/dist/vue.js"></script>
  <script src="https://unpkg.com/vue-smooth-scroll@1.0.11"></script>
  <script>
  Vue.use(VueSmoothScroll)
  var app = new Vue({
    el: '#app',
    data: {
      message: 'Hello Vue!'
    }
  })
</script>
</body>
