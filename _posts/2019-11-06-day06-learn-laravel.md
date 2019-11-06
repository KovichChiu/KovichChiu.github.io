--- 
layout: post      
title:  "[DailyLearning] #06 Laravel 學習之路"      
preview: "[DailyLearning] #06 Laravel Learning"      
permalink: "/articles/2019-11-06-day06-learn-laravel"    
date:   2019-11-06 08:42:00 +08:00      
tags:      
    - "laravel"      
comments: true      
---

Laravel  Controller
<!-- more -->    

# Controller
重點摘要:      
* abstact      
{:toc} 

## 介紹
替代 Route 檔中以閉包的形式定義的所有請求處理邏輯。被存放在 `app/Http/Controllers/` 中。
## 基礎 Controller
### 定義 Controller
下面為例子，該 Controller 繼承了 Laravel 的基礎 Controller。可以直接使用 `middleware()`。

```php
<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use App\User;

class UserController extends Controller
{
    /**
     * Show the profile for the given user.
     *
     * @param  int  $id
     * @return View
     */
    public function show($id)
    {
        return view('user.profile', ['user' => User::findOrFail($id)]);
    }
}
```
路由定義方法：

```php
Route::get('user/{id}', 'UserController@show');
```
`UserController` 現在被以 `show()` 執行，而 `{id}` 將直接傳入。

> Tips. \
> Controller 不是 **必須** 繼承基礎類別，只為了可使用更方便的功能。Ex . `middleware`、`validate`、`dispatch`。

### Controllers & Namespaces
定義 Controllers 不用完整的寫出 Namespaces，只需指定 `App\Http\Controllers` 之後的部分就好。如果完整的 Namespaces 為 `App\Http\Controllers\Photos\AdminController`，Route 的部分應該如下表示。

```php
Route::get('foo', 'Photos\AdminController@method');
```
### 單動 Controllers
用 `__invoke` 來定義只有一個動作的 Controllers。

```php
<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use App\User;

class ShowProfile extends Controller
{
    /**
     * Show the profile for the given user.
     *
     * @param  int  $id
     * @return View
     */
    public function __invoke($id)
    {
        return view('user.profile', ['user' => User::findOrFail($id)]);
    }
}
```
單動 Controllers 不需要指定 Function。

```php
Route::get('user/{id}', 'ShowProfile');
```
可以透過 Artisan 的 `make:controller` 中 `--invokable` 選項來產生。

```bash
php artisan make:controller ShowProfile --invokable
```
### Controllers Middleware
Middleware 分配給 Controllers route。

```php
Route::get('profile', 'UserController@show')->middleware('auth');
```
也可以在 Controllers 中指定 Middleware。

```php
class UserController extends Controller
{
    /**
     * Instantiate a new controller instance.
     *
     * @return void
     */
    public function __construct()
    {
        $this->middleware('auth');

        $this->middleware('log')->only('index');

        $this->middleware('subscribed')->except('store');
    }
}
```
也可以用一個閉包註冊 Middleware。

```php
$this->middleware(function ($request, $next) {
    // ...

    return $next($request);
});
```
> Tips.\
> Middleware 可以被分配給 Controllers 的一個子集，但可能會有「複雜化」的提示，建議可以拆分為數個小型 Controllers。

### 資源 Controllers
Laravel 的資源 Route 將典型的「CURD(增刪改查)」Route 分配給 Controllers。以下是例子：

首先建立一個 Controller 來處理保存「照片」的所有 HTTP 請求。

```bash
php artisan make:controller PhotoController --resource
```
這會生成每個可用資源的操作方法，然後我們註冊 Route。

```php
Route::resource('photos', 'PhotoController');
```
`resource` 等同於「多個」Route 來處理各種行為，而 Controller 中針對每個方法都有註釋標明對應的行為。也可以傳送數組到 `resourse` 來一次性執行資源 Controller。

```php
Route::resources([
    'photos' => 'PhotoController',
    'posts' => 'PostController'
]);
```
#### 資源 Controllers 操作處理

| Verb      | URI                    | Action  | Route Name     |
| :-------- | :--------------------- | :------ |:-------------- |
| GET       | `/photos`              | index   | photos.index   |
| GET       | `/photos/create`       | create  | photos.create  |
| POST      | `/photos`              | store   | photos.store   |
| GET       | `/photos/{photo}`      | show    | photos.show    |
| GET       | `/photos/{photo}/edit` | edit    | photos.edit    |
| PUT/PATCH | `/photos/{photo}`      | update  | photos.update  |
| DELETE    | `/photos/{photo}`      | destroy | photos.destroy |

#### 指定資源 Model
生成 Controllers 加上 `--model` 選項。

```cash
php artisan make:controller PhotoController --resource --model=Photo
```
#### 偽造 Form Method
前面提過，HTML 沒有`put`、`patch`、`delete`method，所以需要加上 `_method`，用 blade 的 `@method` 指令就可以串造字段。

```html
<form action="/foo/bar" method="POST">
    @method('PUT')
</form>
```
### 部分資源 Route
聲明路由時，可以指定 Controller 的部分行為，而不是默認所有行為。

```php
Route::resource('photos', 'PhotoController')->only([
    'index', 'show'
]);

Route::resource('photos', 'PhotoController')->except([
    'create', 'store', 'update', 'destroy'
]);
```
#### API 資源 Route
當用 APIs Route 通常需要排除顯示 HTML 模板的 Route (如 `create` 或 `edit`)。可以使用 `apiResource()` 來自動排除。

```php
Route::apiResource('photos', 'PhotoController');
```
也可以丟一個陣列過去同時註冊多個 APIs 資源 Controller。

```php
Route::apiResources([
    'photos' => 'PhotoController',
    'posts' => 'PostController'
]);
```
或是在一開始製作 Controller 時，使用 `--api` 選項。

```bash
php artisan make:controller API/PhotoController --api
```
### 為資源路由命名
預設情況下，每個行為都會有一個 Route Name。你可以傳入 `names` 陣列覆蓋預設。

```php
Route::resource('photos', 'PhotoController')->names([
    'create' => 'photos.build'
]);
```
### 為 Route 參數命名
預設情況下，`Route::resource` 會根據資源名稱的「單數」形式產生參數，可以用 `parameters` 來覆蓋。`parameters` 應為資源名稱 & 參數名稱的相關。

```php
Route::resource('users', 'AdminUserController')->parameters([
    'users' => 'admin_user'
]);
```
上述將會為 `show` 這個 route 生成如下的 URI：

```
/users/{admin_user}
```
### 本地化資源 URI
預設情況下，`Route::resource` 會用 `Verbs` 產生資源 URI，如果需要本地化 `create`、`edit`行為動作名，可以在 `AppServiceProvider` 中 `boot` 使用 `Route::resourceVerbs()`來實現。

```php
use Illuminate\Support\Facades\Route;

/**
 * Bootstrap any application services.
 *
 * @return void
 */
public function boot()
{
    Route::resourceVerbs([
        'create' => 'crear',
        'edit' => 'editar',
    ]);
}
```
動作被定義之後，像 `Route::resource('fotos', 'PhotoController')` 就會產生：

```
/fotos/crear

/fotos/{foto}/editar
```
### 補充資源 Controllers
想增加額外路由，應該要在 `Route::resource` 之前定義route。否則由 `resource` 定義的可能會無意中覆蓋補充的route。

```php
Route::get('photos/popular', 'PhotoController@method');

Route::resource('photos', 'PhotoController');
```
>Tips.\
>盡量保持 Controller 的專一性，除非有一些典型的操作，否則可以考慮將 Controller 拆開。

## 依賴注入 & Controllers
### 構造函數注入
Laravel 使用 [服務容器](https://learnku.com/docs/laravel/6.x/container) 來解析所有 Controller。所以可以在 Controller 的構造函數中使用類型提示所需要的依賴項，而聲名的依賴像會自動解析並注入 Controller 實例中。

```php
<?php

namespace App\Http\Controllers;

use App\Repositories\UserRepository;

class UserController extends Controller
{
    /**
     * The user repository instance.
     */
    protected $users;

    /**
     * Create a new controller instance.
     *
     * @param  UserRepository  $users
     * @return void
     */
    public function __construct(UserRepository $users)
    {
        $this->users = $users;
    }
}
```
只要能夠被解析，也可以使用 [Laravel 契約](https://learnku.com/docs/laravel/6.x/contracts)。

#### 方法注入
最常見的就是將 `Illuminate\Http\Request` 注入到 Controller 中。

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class UserController extends Controller
{
    /**
     * Store a new user.
     *
     * @param  Request  $request
     * @return Response
     */
    public function store(Request $request)
    {
        $name = $request->name;

        //
    }
}
```
如果 Controller 需要從 route 參數中獲取輸入內容，只需要在其他依賴項後列出 route 參數即可。

```php
Route::put('user/{id}', 'UserController@update');
```
依然可以透過類型提示 `Illuminate\Http\Request` 並通過定義 Controller 來得到 id 參數。

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class UserController extends Controller
{
    /**
     * Update the given user.
     *
     * @param  Request  $request
     * @param  string  $id
     * @return Response
     */
    public function update(Request $request, $id)
    {
        //
    }
}
```
## Route 緩存
> Tips.\
> 基於閉包的路由不能被緩存。如果要使用 route 緩存，你必須將所有的閉包 route 轉換成Controllers route。

如果專案中只使用了 Controllers 的 Route，應該充分利用 Route 緩存，將極大的減少註冊所有 route 的時間。

```bash
php artisan route:cache
```
若有新的路由要添加，再執行一次 `route:cache`。

也可以使用 `route:clear` 來清除 route 緩存。

```bash
php artisan route:clear
```
