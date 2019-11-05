--- 
layout: post      
title:  "[DailyLearning] #04 Laravel 學習之路"      
preview: "[DailyLearning] #04 Laravel Learning"      
permalink: "/articles/2019-11-05-day04-learn-laravel"    
date:   2019-11-05 09:10:00 +08:00      
tags:      
    - "laravel"      
comments: true      
---

Laravel 路由 
<!-- more -->    

# Routing
重點摘要:      
* abstact      
{:toc} 

## 基本 Routing
只需要[URI](https://zh.wikipedia.org/wiki/%E7%BB%9F%E4%B8%80%E8%B5%84%E6%BA%90%E6%A0%87%E5%BF%97%E7%AC%A6) + `Closure` (閉包)。

```php
Route::get('foo', function () {
    return 'Hello World';
});
```
### 預設 Routing 檔案
Laravel 的 route 都由 `routes/` 目錄中的檔案定義，框架會 Auto load 這些檔案。`web.php` 定義了 `web` 介面的 route，這些 route 會被分配給 `web` 的中介軟體，並提供 Session 狀態與 CSRF 保護等功能。而定義在 `api.php` 中的 route 都是沒有狀態的，並且分配給 `api` 中介軟體。

專案大部分的架構，都是在 `web.php` 中定義路由，可以用 browser 來直接對 Server 提出 Requests，而 Server 也會針對 Requests 經由 route 給出相對應的回應。你可以瀏覽 `http://your-app.dev/user`，而 route 會處理這段網址，並且指向與 `/user` 對應的 Controller 及其中的函式。

```php
Route::get('/user',  'UserController@index');
```
這邊就是接收到網址後方的 `user` 並且指向並執行 `UserController` 中的 `index()` 函數。

`api.php` 定義 route 經過 `RouteServiceProvider` 時，會被圈在一個 route group 中，在 route group 裡面，會自動添加 URL 前墜 `/api` 到這個檔案中的每個 route 上。

### 可用的路由方法
任何可以回應 HTTP 請求的 route：

```php
Route::get($uri, $callback);
Route::post($uri, $callback);
Route::put($uri, $callback);
Route::patch($uri, $callback);
Route::delete($uri, $callback);
Route::options($uri, $callback);

# 回應多個請求
Route::match(['get', 'post'], '/', function () {
    //
});

# 回應所有請求
Route::any('/', function () {
    //
});
```
### CSRF 保護
任何指向 `web.php` 中定義的 `POST`、`PUT`、`DELETE` route，都必須包含 CSRF 令牌，否則直接 reject requests。[CSRF documentation](https://laravel.com/docs/6.x/csrf)。

```html
<form method="POST" action="/profile">
    @csrf
    ...
</form>
```
### 重新定位 Route

```php
Route::redirect('/here', '/there');
```

預設會返回 `302`，也可以自定義。或是用 `Route::permanentRedirect` 來返回 `301`

```php
Route::redirect('/here', '/there', 301);

Route::permanentRedirect('/here',  '/there');
```

### View Route
不經過 Controller 就可以呈現的 pages 可以使用 `Route::view`，共有三個參數。第一個必填，view 名稱的 URI；第二個必填，view 名稱；第三個則為選填，若有需要丟入的資料，可以放置在此，但僅限一組資料。

```php
Route::view('/welcome', 'welcome');

Route::view('/welcome', 'welcome', ['name' => 'Taylor']);
```
##  Route 參數
### 必填參數
可以獲取 URL 的片段，再把那丟進後續 function 用。如果有多個參數，則是依序放進變數中。參數會被放在 `{}` 中，內容不得包含 `-` 符號，可以用 `_` 代替。
 
```php
Route::get('user/{id}', function ($id) {
    return 'User '.$id;
});
```

```php
Route::get('posts/{post}/comments/{comment}', function ($postId, $commentId) {
    //
});
```
### 可選填參數
在參數後面加上 `?` 來實現，但要把相對應的值都設定好預設值。

```php
Route::get('user/{name?}', function ($name = null) {
    return $name;
});

Route::get('user/{name?}', function ($name = 'John') {
    return $name;
});
```
### 正規表達式
加上 `where` 來限制參數格式。

```php
Route::get('user/{name}', function ($name) {
    //
})->where('name', '[A-Za-z]+');

Route::get('user/{id}', function ($id) {
    //
})->where('id', '[0-9]+');

Route::get('user/{id}/{name}', function ($id, $name) {
    //
})->where(['id' => '[0-9]+', 'name' => '[a-z]+']);
```
### 全域規則
如果某個具體的 Route 都遵守同一個正規表達式， `RouteServiceProvider` 的 `boot` 中，用 `pattern` 來定義。

```php
public function boot()
{
    Route::pattern('id', '[0-9]+');

    parent::boot();
}
```
定義後變化套用至使用該參數的 Route 上。

```php
Route::get('user/{id}', function ($id) {
    // Only executed if {id} is numeric...
});
```
### 正斜線
因為 Laravel  中，Route 避掉了 `/`，其他符號皆可使用，如果要使用，就必須在 `where` 允許。

```php
Route::get('search/{search}', function ($search) {
    return $search;
})->where('search', '.*');
```

### 將 Route 命名
方便指定 URL 或者 Redirect，利用 `name()` 來命名。 

```php
Route::get('user/profile', function () {
    //
})->name('profile');
```
也可以指定 Controller function 的名稱。

```php
Route::get('user/profile', 'UserProfileController@show')->name('profile');
```
### 產生指定的 URL
產生 route 後，便可以使用 route 來生成連結。

```php
// Generating URLs...
$url = route('profile');

// Generating Redirects...
return redirect()->route('profile');
```
route 的第二個參數可以放值，會自動插入對應 URL 的位置。

```php
Route::get('user/{id}/profile', function ($id) {
    //
})->name('profile');

$url = route('profile', ['id' => 1]);
```
### 檢查當前 Route
`named` 可以檢查目前 `request` 是否指向某個命名過的 Route。
 
```php
public function handle($request, Closure $next)
{
    if ($request->route()->named('profile')) {
        //
    }

    return $next($request);
}
```

## Route group
可以在數個 Route 中，共享屬性，中介軟體、命名空間等。屬性應該由陣列的形式傳進 `Route::group` 方法的第一參數中。

這個陣列會嘗試合併屬性及其父組，中介軟體和 `where` 在附加名稱、命名空間和前綴時被合併，分隔線與斜線會自動添加到URI中。

### 中介軟體
`group` 之前使用 `middleware`，中介軟體會依照順序執行。

```php
Route::middleware(['first', 'second'])->group(function () {
    Route::get('/', function () {
        // Uses first & second Middleware
    });

    Route::get('user/profile', function () {
        // Uses first & second Middleware
    });
});
```
### 命名空間
使用 Namespace 將相同的 php 命名空間分配給 Route group 中的所有 Controller。

```php
Route::namespace('Admin')->group(function () {
    // Controllers Within The "App\Http\Controllers\Admin" Namespace
});
```
預設情況下，`RouteServiceProvider` 會在命名空間組中引入 Route 檔案，不用指定完整的 `App\Http\Controllers` 的命名空間前綴，只需填之後的部分。

### 子域名 Route
Route group 可以處理子域名，像 Route URI 一樣被分配參數，抓得到一部分子域名作為參數給 Route & Controller 使用。定義 `group` 之前調用 `domain` 來指定子域名。

```php
Route::domain('{account}.myapp.com')->group(function () {
    Route::get('user/{id}', function ($account, $id) {
        //
    });
});
```
 
> Tips. **註冊根域名前註冊子域名** \
> 確保子域名可被訪問，應先在註冊根域名前註冊子域名 ，來防止相同 URI 的根域名覆蓋子域名。

### Route 前綴
可以用 `prefix()` 來為 Route group 給定的 URL 增加前綴，Ex. group 中所有 Route 加上 admin 前綴。

```php
Route::prefix('admin')->group(function () {
    Route::get('users', function () {
        // Matches The "/admin/users" URL
    });
});
```

### Route Name 前綴
`name()` 可以用來給整個 group 的每個 Route 一個預訂的字串。Ex. 以 `admin` 為所有 Route 增加前綴。為了防止預定的字串與 Route Name 前綴相同，所以我們在字串結尾增加 `.`。

```php
Route::name('admin.')->group(function () {
    Route::get('users', function () {
        // Route assigned name "admin.users"...
    })->name('users');
});
```

## Route Model 的綁定
當 Route 或 Controller 對 Model 有一些動作時，就需要查詢對應的 Model。Laravel 提供自動為 Route Model 綁定一個實例 Model 加入倒 Route 的方法。Ex. 可以加入與預訂一個 ID 去匹配整個 User 實例 Model，而不是直接加入 user ID。

### 隱藏的綁定
Laravel 會自動處理與類型提示的變數名稱相匹配的 Route Name 的 Eloquent Model。

```php
Route::get('api/users/{user}', function (App\User $user) {
    return $user->email;
});
```
在這個例子中，`$user` 被提示為 Eloquent Model `App/User`，變數名稱也與 URI 的 `{user}` 匹配，因此，Laravel 會自動請求 URI 中傳遞的值的 Model 實例。如果找不到會回傳 `404`。

### 客製化 Key Name

想使用除了 `id` 之外來查找 DB，可以在 Eloquent Model 上重寫 `getRouteKeyName()`。

```php
/**
 * Get the route key for the model.
 *
 * @return string
 */
public function getRouteKeyName()
{
    return 'slug';
}
```
### 明顯的綁定
用 `model()` 來給定指定類別。`RouteServiceProvider` 中的 `boot()` 可以定義。

```php
public function boot()
{
    parent::boot();

    Route::model('user', App\User::class);
}
```
然後定義一個 `{user}` Route

```php
Route::get('profile/{user}', function (App\User $user) {
    //
});
```
`profile/1` 的請求會要求 DB 給出 ID = 1 的 `User` 實例。

### 自訂邏輯解析

`Route::bind()`。傳遞到 `bind` 的 `閉包` 會接受 URI 中大括號的對應的值，並且返回相對應實例。

```php
/**
 * Bootstrap any application services.
 *
 * @return void
 */
public function boot()
{
    parent::boot();

    Route::bind('user', function ($value) {
        return App\User::where('name', $value)->first() ?? abort(404);
    });
}
```
或是重寫 `resolveRouteBinding()`。

```php
/**
 * Retrieve the model for a bound value.
 *
 * @param  mixed  $value
 * @return \Illuminate\Database\Eloquent\Model|null
 */
public function resolveRouteBinding($value)
{
    return $this->where('name', $value)->first() ?? abort(404);
}
```

## Fallback Routes
一般來說，異常處理會回傳 `404`，可以在 `routes/web.php` 中定義 `fallback` Route。

```php
Route::fallback(function () {
    //
});
```
> Tips. \
> fallback route 應該要是放在 `web.php` 中的最後一個 route。

## Form method 偽造
因為 HTML 的 `<form>` 不支持 `PUT` 、`PATCH` 或 `DELETE`，所以要偽造。

使用 `_method` 作為 HTTP 請求方法。

```html
<form action="/foo/bar" method="POST">
    <input type="hidden" name="_method" value="PUT">
    <input type="hidden" name="_token" value="{{ csrf_token() }}">
</form>
```
也可以用 `@method`。

```html
<form action="/foo/bar" method="POST">
    @method('PUT')
    @csrf
</form>
```
## 其他
想更了解所有方法，請參考 API 文檔：[underlying class of the Route facade](https://laravel.com/api/6.x/Illuminate/Routing/Router.html) 、 [Route instance](https://laravel.com/api/6.x/Illuminate/Routing/Route.html) .

