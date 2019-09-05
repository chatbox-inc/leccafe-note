---
title: rollbar-laravel で エラー収集
categories: 
 - Laravel
tag: 
 - Laravel
 - Rollbar
date: 2019-09-04
summary: | 
  Laravel Rollbar を使ったエラー収集の方法を紹介します。 
---

## rollbar-laravel を使う

エラー収集ツール Rollbar では、 laravel-rollbar という Composer ライブラリが用意されており、
かんたんに Laravel でのエラートラッキング環境を構築できます。

https://github.com/rollbar/rollbar-php-laravel

導入方法については、
[公式ドキュメント](https://docs.rollbar.com/docs/laravel#section-installation)でも紹介されているように Laravel 5.6 以上の環境であればかんたんに導入することができます。


### Usage

まずは、 composer 経由でライブラリをインストールします。

```
$ composer require rollbar/rollbar-laravel
```

環境変数 `ROLLBAR_TOKEN` に サーバ用のトークンをセットします。
サーバ用のトークンは、 `Rollbar.com` から `Settings` → `Project Access Tokens` で取得可能です。

```
ROLLBAR_TOKEN={{YOUR_POST_SERVER_ITEM_TOKEN}}
```

`config/logging.php` に以下の設定を追加して対応は完了です。

```php
    'rollbar' => [
        'driver' => 'monolog',
        'handler' => \Rollbar\Laravel\MonologHandler::class,
        'access_token' => env('ROLLBAR_TOKEN'),
        'level' => 'debug',
    ],
```

登録した Log チャンネルの `rollbar` を用いて Rollbar にエラーを送信することができます。

```php
Log::channel("rollbar")->debug("this is sample debug message")
```

## rollbar-laravel を用いたログ運用

`rollbar-laravel` は デフォルトでは、 Log チャンネルを追加するだけなので、
明示的に `Log::channel("rollbar")->debug()` のような形で、 
`rollbar` チャンネルに指定してログを送らない限り、Rollbar にメッセージを送信することはできません。

Laravel 5.6 以上のバージョンでは、 Log の標準設定は `stack` に設定されているため、
`config/logging.php` の `stack` 設定に `rollbar` を追加することで、
標準のログ出力先に Rollbar を追加することができます

```php
    'channels' => [
        'stack' => [
            'driver' => 'stack',
            'channels' => ['daily','rollbar'],
            'ignore_exceptions' => false,
        ],
        //...
        'rollbar' => [
            //...
        ]
    ],
```

Heroku を利用していて `LOG_CHANNEL` の設定が 標準の `stack` から変更されている場合は、
以下のような形で別途 stack の設定を追加して設定すると良いでしょう。

```php
    'channels' => [
        'stack' => [
            'driver' => 'stack',
            'channels' => ['daily','rollbar'],
            'ignore_exceptions' => false,
        ],
        'heroku' => [
            'driver' => 'stack',
            'channels' => ['errorlog','rollbar'],
            'ignore_exceptions' => false,
        ],
        //...
        'rollbar' => [
            //...
        ]
    ],
```
