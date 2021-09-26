# Laravel

https://laravel.com/


## Installation Via Composer

```
composer create-project laravel/laravel example-app

cd example-app

php artisan serve
```

## Next Step

Request Lifecycle
Configuration
Directory Structure
Service Container
Facades

## Full Stack Framework
 routing, views, Eloquent ORM, Laravel Mix
 
## API Backend

 routing, Laravel Sanctum, and the Eloquent ORM.
 
### Sanctum

```
composer require laravel/sanctum
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
php artisan migrate
```

app/Http/Kernel.php
```
'api' => [
    \Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful::class,
    'throttle:api',
    \Illuminate\Routing\Middleware\SubstituteBindings::class,
],
```

### Test

    Feature and Unit

#### Creating Tests


```
php artisan make:test UserTest
php artisan make:test UserTest --unit

vendor/bin/phpunit 
or 
php artisan test
```


# 加速

https://pkg.phpcomposer.com/
