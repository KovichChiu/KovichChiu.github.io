--- 
layout: post      
title:  "[DailyLearning] #05 Laravel 學習之路"      
preview: "[DailyLearning] #05 Laravel Learning"      
permalink: "/articles/2019-11-05-day05-learn-laravel"    
date:   2019-11-05 16:00:00 +08:00      
tags:      
    - "laravel"      
comments: true      
---

Laravel Middleware
<!-- more -->    

# Middleware
重點摘要:      
* abstact      
{:toc} 

## 介紹
Middleware 提供了一種方便的機制來過濾所有 HTTP 請求。Ex. Laravel 有一個驗證用戶身分的 Middleware 。如果可以通過驗證，將進一步定位到其他 page，反之，重新定位到登入頁面。

當然可以自己編寫其他 Middleware。Laravel 自帶一些 Middleware ，都放在 `app/Http/Middleware/` 中。

## 定義一個 Middleware 
`make:middleware` 指令，來建立新的 Middleware。

```bash
php artisan make:middleware CheckAge
```
他會在 `app/Http/Middleware/` 新增 `CheckAge`。在此，我們僅允許 `age` 大於200的請求對 Route進行訪位，否則重新定位到 `home`。

```php
<?php

namespace App\Http\Middleware;

use Closure;

class CheckAge
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        if ($request->age <= 200) {
            return redirect('home');
        }

        return $next($request);
    }
}
```

可以將 Middleware 想像成一連串的 HTTP 請求，必須經過才能進入專案層，每層都會檢查。

> Tips. \
> 所有的 Middleware 都是經過 [service container](https://laravel.com/docs/6.x/container)(服務容器)，所以可以在 Middleware 設計任何需要的過濾方式。

### 前置&後置 Middleware 
在處理請求之前：

```php
<?php

namespace App\Http\Middleware;

use Closure;

class BeforeMiddleware
{
    public function handle($request, Closure $next)
    {
        // Perform action

        return $next($request);
    }
}
```

之後：

```php
<?php

namespace App\Http\Middleware;

use Closure;

class AfterMiddleware
{
    public function handle($request, Closure $next)
    {
        $response = $next($request);

        // Perform action

        return $response;
    }
}
```
## 註冊 Middleware
### Global
如果要讓 Middleware 處理每個請求，在 `app/Http/Kernel.php` 中的 `middleware` 列出。
### 為 Route 分配 Middleware
首先，在 `app/Http/Kernel.php` 為該 Middleware 新增一個鍵。一般情況下，`routeMiddleware` 屬性底下都是 Laravel 內建的 Middleware，自訂的 Middleware 加入 `routeMiddleware`，並給他一個鍵。

```php
// Within App\Http\Kernel Class...

protected $routeMiddleware = [
    'auth' => \App\Http\Middleware\Authenticate::class,
    'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
    'bindings' => \Illuminate\Routing\Middleware\SubstituteBindings::class,
    'cache.headers' => \Illuminate\Http\Middleware\SetCacheHeaders::class,
    'can' => \Illuminate\Auth\Middleware\Authorize::class,
    'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
    'signed' => \Illuminate\Routing\Middleware\ValidateSignature::class,
    'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
    'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
];
```
只要加入後便可透過 `middleware()` 來分配給Route。

```php
Route::get('admin/profile', function () {
    //
})->middleware('auth');
```
也可以分配多個 Middleware。

```php
Route::get('/', function () {
    //
})->middleware('first', 'second');
```

### Middleware Groups
用 `Http` 的 `$middlewareGroups` 把數個 Middleware package成一個 group。

Laravel 內建了可以直接使用的 `web` & `api`，其中包含 Web UI 和 API 經常會用到的 Middleware Groups。

```php
/**
 * The application's route middleware groups.
 *
 * @var array
 */
protected $middlewareGroups = [
    'web' => [
        \App\Http\Middleware\EncryptCookies::class,
        \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
        \Illuminate\Session\Middleware\StartSession::class,
        \Illuminate\View\Middleware\ShareErrorsFromSession::class,
        \App\Http\Middleware\VerifyCsrfToken::class,
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
    ],

    'api' => [
        'throttle:60,1',
        'auth:api',
    ],
];
```
使用方法如下：

```php
Route::get('/', function () {
    //
})->middleware('web');

Route::group(['middleware' => ['web']], function () {
    //
});

Route::middleware(['web', 'subscribed'])->group(function () { 
    //
});
```
> Tips. \
> `RouteServiceProvider` 就有預設給 `routes/web.php` 吃到 `web` 的設定。

### 排序 Middleware
可以以特定順序要求 Middleware 以給定順序執行。從 `app/Http/Kernel.php` 中修改 `$middlewarePriority`。

```php
/**
 * The priority-sorted list of middleware.
 *
 * This forces non-global middleware to always be in the given order.
 *
 * @var array
 */
protected $middlewarePriority = [
    \Illuminate\Session\Middleware\StartSession::class,
    \Illuminate\View\Middleware\ShareErrorsFromSession::class,
    \App\Http\Middleware\Authenticate::class,
    \Illuminate\Session\Middleware\AuthenticateSession::class,
    \Illuminate\Routing\Middleware\SubstituteBindings::class,
    \Illuminate\Auth\Middleware\Authorize::class,
];
```

## Middleware 參數
如果需要在執行操作之前驗證使用者是否符合給定的「角色」，創建 `CheckRole` 由它來接受「角色」作為不加參數。

附加的參數應該在 `$next` 之後傳遞。

```php
<?php

namespace App\Http\Middleware;

use Closure;

class CheckRole
{
    /**
     * Handle the incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @param  string  $role
     * @return mixed
     */
    public function handle($request, Closure $next, $role)
    {
        if (! $request->user()->hasRole($role)) {
            // Redirect...
        }

        return $next($request);
    }

}
```
定義 route 用 `:` 來隔開 Middleware  name 和參數，多個就用逗號分開。

```php
Route::put('post/{id}', function ($id) {
    //
})->middleware('role:editor');
```
## Terminable Middleware
有時候準備好 HTTP 響應之後，還是有一些 Middleware 還要工作。Ex. Laravel 的 `session` 會在完全準備好之後，將 session 數據寫入存儲。如果有定義一個 `terminate` 方法，並且使用FastCGI，那它就會自動調用。

```php
<?php

namespace Illuminate\Session\Middleware;

use Closure;

class StartSession
{
    public function handle($request, Closure $next)
    {
        return $next($request);
    }

    public function terminate($request, $response)
    {
        // Store the session data...
    }
}
```
`terminate` 應該同時接收請求和回應。定義後要將它加入 route list 或  `app/Http/Kernel.php` 讓它可以全域使用。

當你在 Middleware 上調用 `terminate` 方法的時候， Laravel將從[服務容器](https://learnku.com/docs/laravel/6.x/container)中解析出一個新的 Middleware 實例。如果在調用 `handle` 和 `terminate` 方法的同時使用相同的 Middleware 實例，請使用容器的 `singleton` 方法在容器中註冊 Middleware。
