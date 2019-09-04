---
title: Vue.js で利用する Bootstrap のモーダル
categories:
 - Vue
date: 2019-03-03
summary: | 
  Vue.js で Bootstrap のモーダルを利用する方法を紹介します。
---

CSS コンポーネントとして有名な Bootstrap ですが、
その一部の JS コンポーネント周りは Vue.js でスムーズに利用できなかったりします。

Vue.js で Bootstrap の JS コンポーネントはがうまく動作しないのは、
主に jQuery の `onload` で実行される処理が、うまく機能しないためのものなので、
このあたりの 調整をうまく行ってやれば それぞれの機能を活用することは可能です。

今回は Vue.js で Bootstrap のモーダル機能を利用してみましょう。

Modal を記述するために以下のようなモーダルコンポーネントを作成します。

```vue
<template>
  <div>
    <div @click.self="$emit('close')" class="modal show" tabindex="-1">
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">
              Modal Title
            </h5>
            <a @click="$emit('close')" class="close">
              <span aria-hidden="true">&times;</span>
            </a>
          </div>
          <div class="modal-body">
            ...
          </div>
          <div class="modal-footer">
            <a @click="$emit('close')" class="btn btn-secondary" tabindex="">Close</a>
          </div>
        </div>
      </div>
    </div>
    <div class="modal-backdrop fade show" />
  </div>
</template>

<style scoped>
  .modal{
    display: block;
  }
</style>
```

モーダルの Close は親に `$emit('close')` で送信する仕様にします。

backdrop のクリックイベントは小要素にBubling しないように 
`@click` ではなく`@click.self` で設定する必要があります。

モーダルの表示非表示は一旦親の v-if で制御する方が楽なので、
`modal show` のクラスを付与して、コンポーネント単位では常に表示するように設定しています。

このモーダルを利用する側の親コンポーネントでは以下のように記述することができます。

```vue
<template>
  <div>
    <a class="btn btn-light" @close="modal=false" tabindex="">open</a>
  </div>
  <my-modal v-if="modal" @close="modal=false"/>
</template>

<script>

import MyModal from "@/components/modal/Modal.vue"

export default {
  components:{
     MyModal
  },
  data(){
    return {
      modal: false
    }
  },
}
</script>
```

これでモーダルの ON/OFF の表示を実現できるようになりました。

モーダルの開閉にアニメーションを付与する場合は、transition を利用して以下のように実現可能です。

```vue
<template>
  <div>
    <a class="btn btn-light" @close="modal=false" tabindex="">open</a>
  </div>
  <transition name="fade">
    <my-modal v-if="modal" @close="modal=false"/>
  </transition>
</template>

<script>
  ...
</script>

<style scoped lang="scss">
  .fade-enter-active, .fade-leave-active {
    transition: opacity .5s;
  }
  .fade-enter, .fade-leave-to {
    opacity: 0;
  }
</style>
```
