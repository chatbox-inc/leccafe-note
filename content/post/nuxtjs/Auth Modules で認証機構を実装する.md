---
title: Auth Modules で認証機構を実装する
categories:
 - Nuxt.js
tags:
 - Auth Module
date: 2018-10-16
summary: | 
  Nuxt.js で Auth Module を利用して 認証付きアプリケーションを構築する手法を紹介します。
---

## Nuxt.js Auth Modules

Nuxt.js の Auth Modules は Nuxt.js アプリケーションに簡単に認証機構を提供するモジュールです。

https://auth.nuxtjs.org/

それぞれのページのルートに 認証の要不要を実装したり、
ログイン情報を自動でVuex Storeに格納し、再アクセス時のための永続化まで行ってくれます。

Auth Modulesを利用するには npm 経由でモジュールをインストールしてください。

```bash
$ npm i npm install @nuxtjs/auth @nuxtjs/axios
```

モジュールのインストールができたら `nuxt.config.js` にてモジュールを有効化します。

```js
modules.export = {
  //...
  modules: [
    '@nuxtjs/axios',
    '@nuxtjs/auth'
  ],
  auth: {
    // Options
  }  
}
```

なお、 `auth-module` 利用時には  Vuex Store を有効化する必要があります。

まだ store フォルダに何も格納していない場合は、 `store/index.js` を作成して Vuex の機能を有効化してください。

## Auth Modulesの設定

Auth Modules を利用する際には 「ログイン関連処理時に発行するAPI URL」と
「ログイン関連処理時にリダイレクトするルート」とをモジュールに通知する必要があります。

### ログイン関連処理時に発行する API URLの設定

ログイン関連処理時に発行する API URLの設定 等を取りまとめたものを Auth Modules では 
`Strategies` と呼びます。

Github や Facebook / Google などの Oauth を使った認証では、
デフォルトで `Strategies` の設定パターンが用意されているため、
サービスから提供される client_id 等の情報のみで設定は完了します。

Githubの例:

```js
modules.export = {
  auth: {
    strategies: {
        github: {
          client_id: '...',
          client_secret: '...'
        },
    }
  }  
}
```

これらの認証情報は JSのソースとしてアプリケーションに組み込まれる点には注意が必要です。

任意のスクラッチで用意した認証APIを利用する場合には `local` の strategies が利用可能です。

```js
modules.export = {
  auth: {
    strategies: {
      local: {
        endpoints: {
          login: { 
            url: '/api/auth/login', 
            method: 'post', 
            propertyName: 'token' 
          },
          logout: { 
            url: '/api/auth/logout', 
            method: 'post' 
          },
          user: { 
            url: '/api/auth/user', 
            method: 'get', 
            propertyName: 'user' 
          }
        },
      }
    }
  }
}
```

`endpoints` 内の各セクションに有効なAPIの URL や method, ヘッダー情報等を記述します。
構造は `$axios` のオプション構造となっています。

- `login` ログイン時に発行されるAPI
- `logout` ログアウト時に発行されるAPI
- `user` ユーザ情報取得時に発行されるAPI

Auth Module は内部でAPIを発行する際に、自動的にレスポンスを解釈しようとします。
`login` API の場合は `token` キーからトークンを
`user` API の場合は `user` キーからユーザ情報を取得するようになっていますが、
任意のキーでレスポンスを設定したい場合には `propertyName` にそのキー名を指定してください。

ユーザ情報の取得など 認証が必要なAPI は `login` API 経由で取得したトークンを用いて
認証付きのリクエストが実施されます。
トークンは `Bearer` 方式で各API のヘッダに自動的に付与されるため、
アプリケーション内部の `$axios` 全てで、認証を意識せずAPIリクエストを送出することができます。

### ログイン関連処理時にリダイレクトするルート の設定

Auth Module ではAPI URLの他に ログイン関連処理で必要なルートアドレスも設定するすることができます。

```js
modules.export = {
  redirect: {
    login: '/login',
    logout: '/',
    callback: '/login',
    home: '/'
  }
}
```

- `login` は 未ログイン時に認証ルートへアクセスした際のリダイレクトURLです。
- `logout` はログアウト時のリダイレクトURLです。
- `callback` はOauth認証等で必要となる コールバックルートです。
- `home` はログイン後のリダイレクトURLです。

## Auth Modulesを用いたログイン処理

Auth Module の設定が終わったら実際にログインの処理を実行してみましょう。

Auth Module では各種ログイン関連処理を`$auth` 経由で実行することができ、
ログインについては、 `$auth.loginWith()` 関数が用意されています。

```js
const { data } = await this.$auth.loginWith('local', {...opt})
```

`$auth.loginWith()` は 第一引数で指定した strategies を利用して,ログイン処理を実行します。
それぞれの ログイン処理は strategies の実装に依存しますが、
`local` の場合、`$axios` を利用した API コールが行われ Promise が return されます。
フォーム等経由で取得されるログイン情報は axios の Config 形式で第二引数に渡すことが可能です。

ログイン後のユーザ情報は `this.$auth.user` 経由で取得可能です。

これは実際には Vuex に格納されたグローバルなデータとなっているため、
Vue.js の開発ツール上で確認したり、 Vuex の形式でアクセスすることも可能です。

```js
// Access using $auth
this.$auth.user
​
// Access using vuex
this.$store.state.auth.user
```
