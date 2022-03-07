# Laravel

https://laravel.com/


## Installation Via Composer

```sh
composer create-project laravel/laravel example-app
cd example-app
php artisan serve
```

## Database Configure

### sqlite
```sh
cp -n .env.example .env
sed -i '' 's/DB_CONNECTION=mysql/DB_CONNECTION=sqlite/' .env
sed -i '' 's/DB_DATABASE=laravel/#DB_DATABASE=laravel/' .env
touch database/database.sqlite
```
 
## API Backend

 routing, Laravel Sanctum, and the Eloquent ORM.
 
### Authentication

Passport Or Sanctum?

- https://laravel.com/docs/8.x/fortify
- Laravel Breeze: https://laravelacademy.org/post/22170
- https://jetstream.laravel.com/

#### Passport
- https://www.avyatech.com/build-laravel-rest-api-with-test-driven-development/
- https://remotestack.io/how-to-create-secure-rest-api-in-laravel-with-passport/

#### laravel-breeze-api
- https://codelapan.com/post/laravel-8-rest-api-authentication-with-sanctum
- https://codelapan.com/post/how-to-create-a-crud-rest-api-in-laravel-8-with-sanctum

- https://hub.fastgit.org/nrikiji/laravel-breeze-api

```sh
composer require nrikiji/breeze-api
php artisan breeze-api:install
composer require laravel/sanctum
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
php artisan migrate
```

```diff
    public function definition()
    {
        return [
            'name' => $this->faker->name(),
-           'email' => $this->faker->unique()->safeEmail(),
+           'email' => 'user@email.com',
            'email_verified_at' => now(),
            'password' => '$2y$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi', // password
            'remember_token' => Str::random(10),
        ];
    }
```

```sh
#tinker
User::factory(1)->create();
```

app/Http/Kernel.php
```diff
protected $middlewareGroups = [
    'api' => [
        'throttle:api',
        \Illuminate\Routing\Middleware\SubstituteBindings::class,

+        # Added the following three items
+        \Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful::class,
+        \App\Http\Middleware\EncryptCookies::class,
+        \Illuminate\Session\Middleware\StartSession::class,
    ],
];
```

App\Exceptions\AuthorizationMissingException.php

```php
<?php

namespace App\Exceptions;

use Exception;

class AuthorizationMissingException extends Exception
{
    protected $message = 'authorization missing';
}

```

App\Exceptions\Handler.php
```diff
+    public function render($request, Throwable $exception)
+    {
+        $errorCode = 1;
+        $errorMsg = $exception->getMessage();
+
+        if($exception instanceof \Illuminate\Auth\Access\AuthorizationException){
+            $errorCode = 403;
+        }
+
+        if($exception instanceof \Symfony\Component\HttpKernel\Exception\NotFoundHttpException){
+            $errorCode = 404;
+            $errorMsg = 'not found';
+        }
+
+        if($errorMsg=='permission denied'){
+            $errorCode = 403;
+        }
+
+        if($errorMsg=='authorization missing'){
+            $errorCode = 401;
+        }
+
+        return response()->json([
+            'code' => $errorCode,
+            'success' => false,
+            'message' => $errorMsg
+        ]);
+    }
```

App\Http\Middleware\Authenticate.php

```diff
    protected function redirectTo($request)
    {
        if (! $request->expectsJson()) {
            // return route('login');
+            throw new AuthorizationMissingException();
        }
    }
```

routes/api.php
```diff
+ Route::group(['middleware' => ['auth:sanctum']], function () {
      Route::resource('employees',EmployeeAPIController::class);
+ });
```

#### Jetstream

https://jetstream.laravel.com/2.x/features/api.html

```sh
composer require laravel/jetstream

php artisan jetstream:install inertia --teams

npm install
npm run dev

php artisan migrate
or
php artisan migrate:fresh

php artisan db:seed
```

##### Enabling API Support
config/jetstream.php
```php
'features' => [
    Features::profilePhotos(),
    Features::api(),
    Features::teams(),
],
```

##### Defining Permissions

```php
Jetstream::defaultApiTokenPermissions(['read']);

Jetstream::permissions([
    'post:create',
    'post:read',
    'post:update',
    'post:delete',
]);

return $request->user()->id === $post->user_id &&
       $request->user()->tokenCan('post:update')
```

### Resource Controllers

```php
php artisan make:controller PhotoController --resource


use App\Http\Controllers\PhotoController;

Route::apiResource('photos', PhotoController::class);
```

### Test

    Feature and Unit

#### Creating Tests


```sh
php artisan make:test UserTest
php artisan make:test UserTest --unit

vendor/bin/phpunit 
or 
php artisan test
```

## Tinker
```sh
composer require laravel/tinker
php artisan tinker
```

fix quit problem
~/.config/psysh/config.php
```php
<?php
return [
  'usePcntl' => false,
];
```

# Crud

## crud-policies
```sh
composer require kwaadpepper/crud-policies
```

# 3rd Login
# Multi Lang
# RBAC

## laravel-permission
- https://github.com/spatie/laravel-permission
- https://websolutionstuff.com/post/laravel-8-user-role-and-permission
- https://www.gateforlearner.com/laravel-8-user-roles-and-permissions-tutorial/

```sh
composer require spatie/laravel-permission
composer show spatie/laravel-permission
php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider"
php artisan optimize:clear
php artisan migrate
```

Kernel.php

```diff
protected $routeMiddleware = [
    ....
+    'role' => \Spatie\Permission\Middlewares\RoleMiddleware::class,
+    'permission' => \Spatie\Permission\Middlewares\PermissionMiddleware::class,
+    'role_or_permission' => \Spatie\Permission\Middlewares\RoleOrPermissionMiddleware::class,
]
```

Controller

```php
    function __construct()
    {
         $this->middleware('permission:product-list|product-create|product-edit|product-delete', ['only' => ['index','show']]);
         $this->middleware('permission:product-create', ['only' => ['create','store']]);
         $this->middleware('permission:product-edit', ['only' => ['edit','update']]);
         $this->middleware('permission:product-delete', ['only' => ['destroy']]);
    }
```

# AutoCode
- https://github.com/sunshinev/laravel-gii
- https://github.com/InfyOmLabs/laravel-generator
- https://github.com/InfyOmLabs/generator-builder

## laravel
### laravel-generator

```sh
composer require infyomlabs/laravel-generator
#for Generate from Table 
composer require doctrine/dbal
php artisan vendor:publish --provider="InfyOm\Generator\InfyOmGeneratorServiceProvider"
php artisan infyom:publish
```

app\Providers\RouteServiceProvider.php

```diff
    public function boot()
    {
        $this->configureRateLimiting();

        $this->routes(function () {
-           Route::prefix('api')
-               ->middleware('api')
-               ->namespace($this->namespace)
-               ->group(base_path('routes/api.php'));
+           Route::prefix('api')
+               ->middleware('api')
+               ->as('api.')
+               ->namespace($this->app->getNamespace().'Http\Controllers\API')
+               ->group(base_path('routes/api.php'));

            Route::middleware('web')
                ->namespace($this->namespace)
                ->group(base_path('routes/web.php'));
        });
    }
```

#### Generator Commands
https://infyom.com/open-source/laravelgenerator/docs/8.0/getting-started
```sh
php artisan infyom:api $MODEL_NAME
php artisan infyom:scaffold $MODEL_NAME
php artisan infyom:api_scaffold $MODEL_NAME

php artisan infyom:scaffold $MODEL_NAME --fieldsFile=

php artisan infyom:api $MODEL_NAME --tableName=custom_table_name
php artisan infyom:api $MODEL_NAME --fromTable --tableName=$TABLE_NAME
php artisan infyom:api $MODEL_NAME --fromTable --tableName=$TABLE_NAME --connection=connectionName
```

#### Input from Console
```sh
php artisan infyom:api Employee

name string
age integer

```

seed
```sh
php artisan tinker

\App\Models\Employee::factory(20)->create();
```

#### Input from Table
```sh
php artisan infyom:api Role --fromTable --tableName=roles
php artisan infyom:api Permission --fromTable --tableName=permissions
```


#### Give permissions

app\Traits\CrudPermission.php
```php
<?php
namespace App\Traits;

trait CrudPermission
{
    static $crudActions = [ 'index', 'show', 'store', 'update', 'destroy' ];

    public function authorizeCrud($model) {
        $arr = explode('\\',$model);
        $modelName = strtolower( array_pop( $arr ) );

        info($arr);
        info( $modelName );

        foreach(self::$crudActions as $action){
            $actionPerm = $modelName.'-'.$action;
            info($actionPerm);
            $this->middleware('permission:'.$actionPerm, ['only' => [$action]]);
        }
    }

    public static function create($model)
    {
        $arr = explode('\\',$model);
        $modelName = strtolower( array_pop( $arr ) );
        info($arr);

        foreach (self::$crudActions as $action) {
            # code...
            \Spatie\Permission\Models\Permission::create(['name' => $modelName.'-'.$action]);
        }

        dump(
            collect(self::$crudActions)->map(function($v, $k) use($modelName){
                return $modelName.'-'.$v;
            })
        );
    }
}
```

```sh
use Spatie\Permission\Models\Permission
Permission::where('id','>',0)->delete()

App\Traits\CrudPermission::create(Employee::class)

$permission = Permission::create(['name' => 'employee-index'])
$user = User::find(1)
$user->givePermissionTo('employee-index')
$user->syncPermissions(['employee-index', 'employee-show'])

use Spatie\Permission\Models\Role
Role::find(1)->permissions
Role::find(1)->givePermissionTo('employee-index');
Role::find(1)->syncPermissions(['employee-index']);

```


#### Controller
: authorizeCrud, Search and Paginate
```diff
+   use \App\Traits\CrudPermission;

    public function __construct(EmployeeRepository $employeeRepo)
    {
+       $this->authorizeCrud(Employee::class, 'employee');
        $this->employeeRepository = $employeeRepo;
    }
    
    public function index(Request $request)
    {

+       $employees = $this->employeeRepository->allQuery(
+           $request->only($this->employeeRepository->getFieldsSearchable())
+       )->paginate($request->input('pageSize', 10));

        return $this->sendResponse($employees->toArray(), 'Employees retrieved successfully');
    }
```

policy
```sh
php artisan make:policy EmployeePolicy --model=Employee
```

## React
## Route and Menu
## Api Doc

# Excel Imp&Exp
# Action Log
# packages
## Data
### Tree
- https://github.com/jiaxincui/closure-table

```sh
composer require jiaxincui/closure-table
php artisan make:model Menu -f -m
```
database/migrations/create_menus_table.php
```diff
    public function up()
    {
-       Schema::create('menus', function (Blueprint $table) {
-           $table->id();
-           $table->timestamps();
-       });

+       Schema::create('menus', function (Blueprint $table) {
+                   $table->increments('id');
+                   $table->string('name');
+                   $table->unsignedInteger('parent')->default(0);
+               });

+       Schema::create('menu_closure', function (Blueprint $table) {
+                   $table->unsignedInteger('ancestor');
+                   $table->unsignedInteger('descendant');
+                   $table->unsignedTinyInteger('distance');
+                   $table->primary(['ancestor', 'descendant']);
+               });
    }
```

```sh
php artisan migrate
```

Models/Menu.php
```diff
+ use Jiaxincui\ClosureTable\Traits\ClosureTable;

class Menu extends Model
{
    use HasFactory;
+     use ClosureTable;
}

```

```sh
#tinker
Menu::create(['name'=>'root'])
Menu::get()
$root = Menu::find(1)
$sub1 = Menu::create(['name'=>'sub1'])
$root->addChild($sub1)
$root->createChild(['name'=>'sub2'])

$root->getDescendants()
$root->getTree()
Menu::all()->toTree()
```

- https://github.com/lyxxxh/lartree

## Others
- https://github.com/lcobucci/jwt
- https://github.com/phpseclib/phpseclib
- https://github.com/spatie/laravel-csp
- https://github.com/spatie/laravel-artisan-dd

# Debug
```php
info('some log message ...');
dd($user);
dump($user);
```

# Docs
- https://laravelacademy.org/books/laravel-docs-8

## FAQ
### CSRF token mismatch.
- app\Http\Middleware\VerifyCsrfToken.php添加白名
```php
    protected $except = [
        'api/*'
    ];
```

- app\Http\Kernel.php 注释VerifyCsrfToken

# 加速

- https://developer.aliyun.com/composer
- https://pkg.phpcomposer.com/
