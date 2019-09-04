---
title: Laravelにおけるトランザクション
tags: 
 - Laravel
date: 2019-03-13
summary: | 
  Laravel におけるトランザクションの設定方法などを紹介します。
---

## Laravel における factory

Laravel では DB クラスを利用してトランザクションを利用することができます。

```php
DB::transaction(function () {
    DB::table('users')->update(['votes' => 1]);

    DB::table('posts')->delete();
});
```

function 内のDB操作はアトミックに処理され、途中でエラーが発生した場合には自動的にロールバックがかかります。

また以下のような記述を用いて、トランザクションの開始とコミット、ロールバックを明示的に記述することも可能です。

```php
DB::beginTransaction();

DB::rollBack();

DB::commit();
```

### アプリケーション全体でのトランザクション

アプリケーション全体で自動的にトランザクションを有効化する場合には、
ミドルウェアを利用するのが便利です。

以下の様なミドルウェアを作成してルート全体に適用することで、
アプリケーション全体にトランザクションを適用できます。

```
<?php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Database\DatabaseManager;

class LaravelTransactionMiddleware
{
    protected $db;

    public function __construct(DatabaseManager $db)
    {
        $this->db = $db;
    }

    public function handle($request, Closure $next)
    {
        return $this->db->transaction(function()use($request, $next){
            return $next($request);
        });
    }
}
```

この ミドルウェアは、Laravel のプラグインとしても用意されているため、
以下のコマンドでインストールして利用することも可能です。

```bash
$ composer require chatbox-inc/laravel-transaction
```

詳しくは、以下の Github リポジトリをご確認ください。

https://github.com/chatbox-inc/laravel-transaction
