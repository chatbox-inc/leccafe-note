---
title: Laravel Telescope
categories:
 - Laravel
tags: 
 - Laravel
date: 2019-01-28
summary: | 
  Laravel 5.7 から導入されたTeleScope の利用方法について解説します。 
---

## Laravel Telescoope を使う

```
$ composer require laravel/telescope
```

```
$ php artisan telescope:install

$ php artisan migrate
```

Install することで以下のファイルが生成されます。

- TelescopeServiceProvider
- config/telescope.php 
- public/vendor/telescope　配下の assets 群

また config/app.php にも ServiceProvider の追加が行われます。

バージョンアップ等に伴う更新には、`php artisan telescope:publish` を利用することで対応可能です。

migrate することで必要なテーブルが生成されますが、テーブル等がない場合でもエラー等起きること無く通常通りの動作が行われます。

migrate でテーブルを作成したタイミングから アプリケーションのログが開始され、
データは `http://localhost:8000/telescope` のアドレスで確認することができるようになっています。

## Dashboard 

telescope のアドレスにアクセスすることでアプリケーション上の様々な情報にアクセスすることが可能になります。

`http://localhost:8000/telescope`

telescope へのデータ格納は、
アプリケーションの動作に応じで都度自動で行われていき、ユーザは常にその履歴を確認することができます。

telescope の挙動は、bool 値の環境変数 `TELESCOPE_ENABLED` で確認が可能となっており、
false に設定した場合、アプリケーションのログ監視は行われなくなります(ダッシュボードは有効)。

### Dashboard の認証

telescope の管理画面は デフォルトの状態では、環境変数 `APP_ENV=local` の状態でのみ有効です。

staging や production の環境で利用するためには、
Laravel の認証機能を利用して、

`\App\Providers\TelescopeServiceProvider::gate`

に信頼できるユーザのEmail アドレスを追加する必要があります。
(ログイン機能等は自分で実装する必要があります。)

### 利用可能な機能

- Request
- Commands
- Schedule
- Job
- Exception
- Logs
- Dumps
- Queries
- Models
- Mail
- Notification
- Gates
- Cache
- Redis

### データの削除

telescope のデータはデータベース内に自動的に蓄積されていきます。

これらのデータを削除する場合、`telescope:prune` メソドでデータを削除できます。

```
$ php artisan telescope:prune
```

古いデータのみ削除する場合には、`--hours` オプションでデータの期限を指定することも可能です。

```
$ php artisan telescope:prune --hours=48
```
