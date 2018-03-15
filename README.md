# CORS

[![StyleCI](https://styleci.io/repos/125001413/shield?branch=master)](https://styleci.io/repos/125001413)
[![Build Status](https://travis-ci.org/medz/cors.svg?branch=master)](https://travis-ci.org/medz/cors)

PHP CORS (Cross-origin resource sharing) middleware.

## Support

- Array, Native PHP coding.
- Using PSR-7
- Laravel
- Symfony

## Using

```shell
composer require medz/cors
```

Example:

```php
use Medz\Cors\Cors;

$cors = new Cors($config); // The $config 👉 `config/cors.php` file.
$cors->setRequest($requestType, $request); // The $request is empty array or is `NULL` or $_REQUEST
$cors->setResponse($responseType, $response); // The $response is empty array.
$cors->handle();

$response = $cors->getResponse();
```

Both `$requestType` and `$responseType` are of any type that is individually set to support, but if used in a framework, these two values are usually the same. Because you can set it up individually, you can deliver different values depending on your needs to achieve your goals.

### Configure

The config example:

```php
$config = [
    'allow-credentiails' => false, // set "Access-Control-Allow-Credentials" 👉 string "false" or "true".
    'allow-headers'      => ['*'], // ex: Content-Type, Accept, X-Requested-With
    'expose-headers'     => [],
    'origins'            => ['*'], // ex: http://localhost
    'methods'            => ['*'], // ex: GET, POST, PUT, PATCH, DELETE
    'max-age'            => 0,
];
```

### Array

```php
use Medz\Cors\Cors;

$cors = new Cors($config);
$cors->setRequest('array', $request);
$cors->setResponse('array', $response);
$cors->handle();

$response = $cors->getResponse();
```

### PSR-7

```php
use Medz\Cors\Cors;

$cors = new Cors($config);
$cors->setRequest('psr-7', $request);
$cors->setResponse('psr-7', $response);

$response = $cors->getResponse();
```

### Laravel

> The package only support Laravel >= **`5.5`**.

The package `ServiceProvider` default perend to `app/Http/Kernel.php` `$middleware`, If you want to customize the order of the middleware execution, please add it to the `$middleware` manually:


```php
protected $middleware => [
    // ...
    Medz\Cors\Laravel\Middleware\Cors::class,
    // ...
];
```

#### Configure

You run Laravel Artisan command: 

```shell
php artisan vendor::publish --force --class="\Medz\Cors\Laravel\Probiders\LaravelServiceProvider"
```

publish `cors.php` file to `config` dir.

There are some configurations that you can write directly in the `.env` file:

| Name | Desc |
|----|----|
| CORS_ALLOW_CREDENTIAILS | `Access-Control-Allow-Credentials` |
| CORS_ACCESS_CONTROL_MAX_AGE | `Access-Control-Max-Age` |
| CORS_LARAVEL_ALLOW_ROUTE_PERFIX | Prefix settings for allowing cross domains. |
| CORS_LARAVEL_ROUTE_GROUP_MODE | Whether routing group matching is enabled, if open, only the startup routing group allows setting cross domain information. |

#### Route Group Mode

This package allows you to configure the cross-domain routed middleware groups individually. If this mode is enabled, only routes configured with the `Medz\Cors\Laravel\Middleware\ShouldGroup` middleware will allow you to add cross-domain settings.

To facilitate your memory, you can set the middleware alias directly in your `app/Http/Kernel.php` file:

```php
protected $routeMiddleware = [
    // ...
    'cors-should' => \Medz\Cors\Laravel\Middleware\ShouldGroup::class,
];
```

You can set it directly to the routing middleware:

```php
Route::middleware('cors-should') // Route::middleware(\Medz\Cors\Laravel\Middleware\ShouldGroup::class)
    ->get('/cors-test', ...);
```

You can also set it to the middleware group you allow, we use the `api` group as an example(`app/Http/Kernel.php`):


```php
protected $middlewareGroups = [
    // ...
    'api' => [
        // ...
        \Medz\Cors\Laravel\Middleware\ShouldGroup::class, // If you have aliased the middleware, you can write the middleware alias directly.
        // ...
    ],
    // ...
];
```

> Allow group functions and route prefix matching functions to be processed together.

#### Route Prefix

Routing prefixes, also known as route matching, allow you to configure routing rules. Only routes that meet the rules are allowed to cross domains.

You can modify the `config.cors.php` `laravel.allow-route-perfix` value to configure, or you can use `CORS_LARAVEL_ALLOW_ROUTE_PERFIX` to set rules in `.env`.

The default setting is `*`.

### Symfony

> ⚠️The framework has not provided a method yet. Please wait!

### Other

Because of the interface features provided by this package, you can implement it in a small amount of code in any other framework.
