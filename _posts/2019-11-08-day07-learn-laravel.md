--- 
layout: post      
title:  "[DailyLearning] #07 Laravel 學習之路"      
preview: "[DailyLearning] #07 Laravel Learning"      
permalink: "/articles/2019-11-08-day07-learn-laravel"    
date:   2019-11-08 09:30:00 +08:00      
tags:      
    - "laravel"      
comments: true      
---

在使用 Laravel Eloquent 的時候與到的一些問題，整理在這了。
<!-- more -->    

# Laravel Eloquent
重點摘要:      
* abstact      
{:toc} 

## 想要查找一條數據存在與否
比如說想做登入的時候，需要比對帳號資料，若有該條資料則在繼續比對是否密碼正確。那要怎麼檢查該數據是否存在呢？

比如搜尋條件如下：

```php
$user  =  User::where('email',  '=',  Input::get('email'));
```
結論分成是否要繼續使用 `$user`：

```php
$user  =  User::where('email',  '=',  Input::get('email'))->first();  
if  ($user  ===  null)  {  
    //沒有紀錄
}
```

單純想檢查的話：

```php
if  (User::where('email',  '=',  Input::get('email'))->count()  >  0)  {  
    // 有紀錄  
}
```
或是有更好的方法：

```php
if  (User::where('email',  '=',  Input::get('email'))->exists())  {  
    // 有紀錄  
}
```

## Eloquent 實現 InnerJoin

假設現在有三張表 `order` ,`ticket`, `u_account`，分別的結構如下：

- order

```
o_id(PK)
o_no
o_time
o_uid(index → u_account.u_id)
o_tid(index → ticket.t_id)
o_tpics
```
- ticket

```
t_id(PK)
t_name
t_price
t_content
t_pics
```
- u_account

```
u_id(index)
u_acc(PK)
u_pswd
u_name
u_vali
```

如以上三表，如需查找及對應 `order` 資料需要在 `order` 中 InnerJoin `ticket` & `u_account` 兩張表，使用 `belongsto()`。

```php
// App\Model\Order.php

public function user()  
{  
    return $this->belongsTo('App\User', 'o_uid', 'u_id');
}  
  
public function ticket()  
{  
    return $this->belongsTo('App\Ticket', 'o_tid', 't_id');  
}
```
後面還可以加上 `where()` 等屬性。

在 Controller 上使用的話使用 `with()`，再用 `get()` 取得資料。可以利用 `orderBy()` 來排序資料。其中，`with()` 中的資料為 `order` 中 `functions` 的名稱，可以多個。

```php
$order = Order::with('user', 'ticket')->get();
return view('order', ['data' => $order]);
```
資料輸出的方法：

```php
<!-- views\order -->
@foreach($data as $value)  
    //直接抓欄位只能抓到 `order` 的資料。
    {{ $value->o_id }} 
    //若要取 `user` & `u_account` 的資料需要指向剛剛`with()`中間夾的相對應的名稱再指向欄位。
    {{ $value->user->u_name }} 
@endforeach
```




[https://learnku.com/laravel/wikis/27722](https://learnku.com/laravel/wikis/27722)
[https://stackoverflow.com/questions/29165410/how-to-join-three-table-by-laravel-eloquent-model](https://stackoverflow.com/questions/29165410/how-to-join-three-table-by-laravel-eloquent-model)
