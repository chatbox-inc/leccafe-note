---
title: Factory によるテストデータ生成
categories:
 - Laravel
tags:
 - Laravel 
date: 2019-02-15T00:00:00+09:00
summary: | 
  Laravel 5.6 から導入された新しいログのハンドリング方法について紹介してきます。 
---


## Laravel における factory


Factory はテストデータ生成用の関数です。

Eloquent を利用して対応するテーブルにテストデータを投入する事ができます。

## Factory の定義と利用

Factory の定義は、`database/factories` ディレクトリに格納します。
例えば、Eloquent クラスの `\App\User` に　Factory を定義する場合、`database/factories/UserFactory.php` を作成して以下のように記述します。

```php
<?php
use Faker\Generator as Faker;

$factory->define(App\User::class, function (Faker $faker) {
    return [
        'name' => $faker->name,
        'email' => $faker->unique()->safeEmail,
        'email_verified_at' => now(),
        'password' => '$2y$10$TKh8H1.PfQx37YgCzwiKb.KjNyWgaHb9cbcoQgdIVFlYg7B77UdFm', // secret
        'remember_token' => str_random(10),
    ];
});
```

::: tip
`php artisan make:factory PostFactory`のようにartisan コマンドを利用して、factory ファイルを自動で生成することも可能です。
:::

定義した factory は テストやSeeder から以下のようなかたちで利用する事ができます。

```php
<?php
// 単一行のユーザ情報を追加
$user = factory(App\User::class)->create();
// 複数件のデータを作成する場合は、第二引数に数を定義
$users = factory(App\User::class, 5)->create();
```

`create` がコールされたタイミングで、データベースにテストデータが格納されます。

`create` の引数にデータを渡すことで、任意のデータで上書きしながらテストデータを作成することも可能です。

```php
<?php
$user = factory(App\User::class)->create([
    'name' => 'Abigail',
]);
```

create ではなく make をコールすると、データベースへの格納は行われず、
Eloquent モデルの生成のみが実施されます。

```php
<?php
// 単一行のユーザ情報を追加
$user = factory(App\User::class)->make();
// 複数件のデータを作成する場合は、第二引数に数を定義
$users = factory(App\User::class, 5)->make();
```

### State の利用

定義した Factory に state を定義して、同一のEloquent に対して複数パターンの Factory を定義することができます。

```php
<?php
$factory->state(App\User::class, 'address', function ($faker) {
    return [
        'address' => $faker->address,
    ];
});
```

実際にデータを生成する場面では、 states 関数に適用する state を指定します。
state は factory 生成時に、複数指定することも可能です。

```php
<?php
$users = factory(App\User::class, 5)->states('premium', 'delinquent')->make();
```




## サブテーブルへのデータ格納



`create` は戻り値に Eloquent (複数の場合は Eloquent の Collection) を返すため、
サブテーブルへの データ格納を行うことも可能です。

以下は Collection の `each` 関数を利用して、生成したそれぞれの User モデルにサブテーブルの情報を格納する例です。

```php
<?php
$users = factory(App\User::class, 3)
           ->create()
           ->each(function ($user) {
                $user->posts()->save(factory(App\Post::class)->make());
            });


```

また、Factory を定義する際に、クロージャを利用して実行時に任意の値を計算させることも可能です。

これを利用して、データ生成時に親テーブルのデータを作成したり、データ生成時に、任意のDB内のデータを取得してきたりすることも可能です。

```php
<?php
$factory->define(App\Post::class, function ($faker) {
    return [
        'title' => $faker->title,
        'content' => $faker->paragraph,
        'user_id' => function () {
            return factory(App\User::class)->create()->id;
        },
        'user_type' => function (array $post) {
            return App\User::find($post['user_id'])->type;
        }
    ];
});
```
