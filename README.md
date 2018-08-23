# laravel-clickhouse

Eloquent model for ClickHouse

## Prerequisites
- PHP 7.1+
- Clickhouse Server

## Installation

Add this repository into composer.json:

```php
"require": {
    "Henryh/laravel-clickhouse": "^0.2"
}
"repositories": [
    {
        "url": "git@github.com:Henryh/laravel-clickhouse.git",
        "type": "git"
    }
]
```

Then add the code above into your config/app.php file providers section
```php
Esazykin\LaravelClickHouse\ClickHouseServiceProvider::class,
```

And add new connection into your config/database.php file. Something like this:
```php
'connections' => [
    'clickhouse' => [
        'driver' => 'clickhouse',
        'host' => env('CLICKHOUSE_HOST', 'localhost'),
        'port' => env('CLICKHOUSE_PORT', '8123'),
        'database' => env('CLICKHOUSE_DATABASE', 'test'),
        'username' => env('CLICKHOUSE_USERNAME', 'default'),
        'password' => env('CLICKHOUSE_PASSWORD', 'default'),
        'options' => [
            'timeout' => 10,
            'protocol' => 'https'
        ]
    ]
]
```

Add settings into your .env file:
```ini
CLICKHOUSE_DATABASE=test
CLICKHOUSE_USERNAME=default
CLICKHOUSE_PASSWORD=default
```

Or like this, if clickhouse runs in cluster add in config/database.php:
```php
'connections' => [
    'clickhouse' => [
        'driver' => 'clickhouse',
        'cluster' => [
            'server-1' => [
                'host' => '',
                'port' => '',
                'database' => '',
                'username' => '',
                'password' => '',
                'options' => [
                    'timeout' => 10,
                    'protocol' => 'https'
                ]
            ],
            'server-2' => [
                'host' => '',
                'port' => '',
                'database' => '',
                'username' => '',
                'password' => '',
                'options' => [
                    'timeout' => 10,
                    'protocol' => 'https'
                ]
            ]
        ]
    ]
]
```

Then create model
```php
<?php

use Esazykin\LaravelClickHouse\Database\Eloquent\Model;

class Payment extends Model
{
    protected $table = 'payments';
}
```

And use it
```php
Payment::select(raw('count() AS cnt'), 'payment_system')
    ->whereBetween('payed_at', [
        Carbon\Carbon::parse('2018-01-01'),
        now(),
    ])
    ->groupBy('payment_system')
    ->get();

```