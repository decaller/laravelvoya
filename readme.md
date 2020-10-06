# requirement
..* shared hosting with terminal access

# Backgorund

## Why not clean install? 
Some shared hosting provider limit php memory size, unlike local pc which the php.ini memmory_limit size can be set to -1 for limitless memory usage.
This limitation killed `composer require tcg/voyager` command.

## Why main laravel app on diffrent folder?
To prevent unauthorized user access laravel files.

## Why the dataabase caveat?
Some shared hosting have older MySql/MariaDB database type.

# TODO
..* Make script for automation

# How to install to shared hosting or local server (like Xampp)

1. Delete public_html files and move public files to it
> If you use subdomain, use your subdomain folder name instead
2. Upload to rapotapp shared hosting folder
3. Change parameter in rapotapp/server.php
```php
require_once __DIR__.'/public/index.php';

// to

require_once __DIR__.'../*public_html or subdomain folder*/index.php';
```
4. Make .env file
5. Set permission rapotapp/storage to 777 recursively
6. Do `composer install`
7. Do `php artisan voyager:install`
8. Do `php artisan voyager:admin your@email.com --create`
9. Do `php artisan key:generate`
10. Do `php artisan migrate`


# How to remake

## Main Installation
1. Install composer and laravel locally
2. Make a new laravel project and `cd` to it
3. Then do `composer require tcg/voyager`

## To Test Locally with Development Mode
1. Set .env file
2. do `php artisan voyager:install`
3. do `php artisan voyager:admin your@email.com --create`
4. do `php artisan serve`


## Diffrent folder caveat
1. Change parameter in public/index.php
```php
require __DIR__.'/../vendor/autoload.php';

$app = require_once __DIR__.'/../bootstrap/app.php';

// to

require __DIR__.'/../rapotapp/vendor/autoload.php';

$app = require_once __DIR__.'/../rapotapp/bootstrap/app.php';
```

## Older mysql caveat
1. Change mysql parameter in config/database.php
```php
'mysql' => [
            'driver' => 'mysql',
            'url' => env('DATABASE_URL'),
            'host' => env('DB_HOST', '127.0.0.1'),
            'port' => env('DB_PORT', '3306'),
            'database' => env('DB_DATABASE', 'forge'),
            'username' => env('DB_USERNAME', 'forge'),
            'password' => env('DB_PASSWORD', ''),
            'unix_socket' => env('DB_SOCKET', ''),
            'charset' => 'utf8mb4',
            'collation' => 'utf8mb4_unicode_ci',
            'prefix' => '',
            'prefix_indexes' => true,
            'strict' => true,
            'engine' => null, //this one
            'options' => extension_loaded('pdo_mysql') ? array_filter([
                PDO::MYSQL_ATTR_SSL_CA => env('MYSQL_ATTR_SSL_CA'),
            ]) : [],

// to 
'mysql' => [
            'driver' => 'mysql',
            'url' => env('DATABASE_URL'),
            'host' => env('DB_HOST', '127.0.0.1'),
            'port' => env('DB_PORT', '3306'),
            'database' => env('DB_DATABASE', 'forge'),
            'username' => env('DB_USERNAME', 'forge'),
            'password' => env('DB_PASSWORD', ''),
            'unix_socket' => env('DB_SOCKET', ''),
            'charset' => 'utf8mb4',
            'collation' => 'utf8mb4_unicode_ci',
            'prefix' => '',
            'prefix_indexes' => true,
            'strict' => true,
            'engine' => 'InnoDB', //this one
            'options' => extension_loaded('pdo_mysql') ? array_filter([
                PDO::MYSQL_ATTR_SSL_CA => env('MYSQL_ATTR_SSL_CA'),
            ]) : [],
```
2. Add these code in app/Providers/AppServiceProvider.php
```php
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Illuminate\Support\Facades\Schema; // this and...

class AppServiceProvider extends ServiceProvider
{
    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        //
    }

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        //
        Schema::defaultStringLength(191); //this
    }
}

```
