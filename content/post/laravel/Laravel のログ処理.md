---
title: Laravel と ログの取扱い
categories:
 - Laravel
tag: 
 - Laravel
date: 2018-10-18
summary: | 
  Laravel 5.6 から導入された新しいログのハンドリング方法について紹介してきます。 
---

## Laravel Log

Laravel 5.6 からログの仕組みが大きく変更となり、柔軟なログ管理が可能になりました。

### Laravel におけるログの送信

ログの記述方法について、大きな変化はありません。 `Log` Facade を利用して、
ログレベルに応じたログを送信する事ができます。

```php
Log::emergency($message);
Log::alert($message);
Log::critical($message);
Log::error($message);
Log::warning($message);
Log::notice($message);
Log::info($message);
Log::debug($message);
```

配列などの構造データをログで出力したい場合には、 第二引数で指定することができます。

```php
Log::info('User failed to login.', ['id' => $user->id]);
```

### ログチャンネルの設定

ログの出力管理に関する設定は、チャンネルと呼ばれる単位に分割され、より柔軟な管理が可能になりました。

チャンネルは、`driver` と `level` の値を持つ配列で `config/logging.php` にてその設定を確認することができます。

従来は 環境変数 `APP_LOG_LEVEL` を利用してグローバルな ログレベル管理が行われてきましたが、
5.6 以降はチャンネルごとに個別でログレベルを設定するように変更されています。

ログをどこに出力するかを設定するのが `driver` で、 出力されるログのレベルはチャンネルごとに設定出来るようになっています。
Laravel では標準で以下 8つの `driver` が提供されています。

- `stack`	複数のチャンネルにログを出力する際に利用されるドライバ
- `single`	StreamHandler を利用して単一のファイルにログを書き出すドライバ
- `daily`	RotatingFileHandler を利用して日毎に個別のファイルにログを書き出すドライバ
- `slack`	SlackWebhookHandler を利用して Slack にログを書き出すドライバ
- `syslog` SyslogHandler を利用してサーバのシステムログにログを書き出すドライバ
- `errorlog` ErrorLogHandler を利用して標準エラー出力にログを書き出すドライバ
- `monolog`	Monolog ベースのログ出力設定が可能なドライバ
- `custom`	ログ出力処理を詳細に設定可能なドライバ

#### stack 

`stack` は 複数のログチャンネルの設定に対して ログを出力するドライバです。

`channels` に **チャンネル名** を指定して、どのチャンネルを組み合わせるかを指定することができます。

出力されるログレベルは チャンネル個別の設定に依存します。

```php
<?php
return [
  //...
  'channels' => [
    'stack' => [
        'driver' => 'stack',
        'channels' => ['syslog', 'slack'],
    ],

    'syslog' => [
        'driver' => 'syslog',
        'level' => 'debug',
    ],

    'slack' => [
        'driver' => 'slack',
        'url' => env('LOG_SLACK_WEBHOOK_URL'),
        'username' => 'Laravel Log',
        'emoji' => ':boom:',
        'level' => 'critical',
    ],
  ],
];
```

#### single / daily

`single` や `daily` は　ファイルにエラーを出力するドライバです。
ともに `path` キーでファイルパスを指定します。

`daily` では `days` キーで ローテートのタイミングを調整することも可能です。

```php
<?php 
return [
    //...
    'channels' => [
        'single' => [
            'driver' => 'single',
            'path' => storage_path('logs/laravel.log'),
            'level' => 'debug',
        ],
        'daily' => [
            'driver' => 'daily',
            'path' => storage_path('logs/laravel.log'),
            'level' => 'debug',
            'days' => 7,
        ],
];
```

### Slack

`slack`ドライバは　Slack へのログ送信を可能にします。

利用には Slack の Webhook URL の取得が必要です。

```php
<?php

return [
    //...
    'channels' => [
        'slack' => [
            'driver' => 'slack',
            'url' => env('LOG_SLACK_WEBHOOK_URL'),
            'username' => 'Laravel Log',
            'emoji' => ':boom:',
            'level' => 'critical',
        ],
    ],
];


```


### syslog / errorlog

`syslog` `errorlog` は システムログ、標準エラー出力にログを出力します。

```php
<?php
return [
    //...
    'channels' => [
        'syslog' => [
            'driver' => 'syslog',
            'level' => 'debug',
        ],
        'errorlog' => [
            'driver' => 'errorlog',
            'level' => 'debug',
        ],
    ],

];
```

### monolog

`monolog` ドライバは任意の Monolog ベースのハンドラ、フォーマッタを利用することができます。

```php
<?php
return [
    //...
    'channels' => [
        'browser' => [
            'driver' => 'monolog',
            'handler' => Monolog\Handler\BrowserConsoleHandler::class,
            'formatter' => Monolog\Formatter\HtmlFormatter::class,
            'formatter_with' => [
                'dateFormat' => 'Y-m-d',
            ],
        ],
    ],

];
```

Monolog ベースで作成されたハンドラ、フォーマッタの一覧は以下のMonolog ドキュメントから確認可能です。

https://seldaek.github.io/monolog/doc/02-handlers-formatters-processors.html

また Moonolog の公式ドキュメント上の手順を利用して、自分で任意の ハンドラを定義することも可能です。

https://seldaek.github.io/monolog/doc/04-extending.html

### custom 

`custom` ドライバを利用するケースでは、任意のクラスにログの処理を委譲できます。

```php
<?php
return [
    //...
    'channels' => [
        'custom' => [
            'driver' => 'custom',
            'via' => App\Logging\CreateCustomLogger::class,
        ],
    ],
];
```

指定されたクラスでは以下のようにログの処理を実装します。

```php
<?php
namespace App\Logging;

use Monolog\Logger;

class CreateCustomLogger
{
    public function __invoke(array $config)
    {
        // \Monolog\Logger クラスのインスタンスを返す
        return new Logger(...);
    }
}
```

### チャンネルを指定したログ送信

`Log::info($message)` などを利用したログ送信では、
デフォルトのログチャンネルを利用したログ配信が実施されます。

デフォルトのログチャンネルは、`config/logging.php` の `default` キーで設定されており、
環境変数 `LOG_CHANNEL` を操作することで変更可能となっています。

特定のログチャンネルにログを送信する場合には `Log::channel()` メソドが利用できます。

```php
// 単一のチャンネルへの送信
Log::channel('slack')->info('Something happened!');
// 複数のチャンネルへの送信
Log::stack(['single', 'slack'])->info('Something happened!');
```

https://laravel.com/docs/5.7/logging
