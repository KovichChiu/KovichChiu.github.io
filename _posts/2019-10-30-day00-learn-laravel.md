--- 
layout: post    
title:  "[DailyLearning] #00 Laravel - Learn init"    
preview: "[DailyLearning] #00 Laravel - Learn init"    
permalink: "/articles/2019-10-30-day00-learn-laravel"  
date:   2019-10-30 14:10:00 +08:00    
tags:    
    - "laravel"    
comments: true    
---  

一開始學`laravel`的時候只有翻寫，並不完全了解MVC架構，藉此讓自己在學習laravel的路上可以更有收穫。 
<!-- more -->  
  
重點摘要:    
* abstact    
{:toc}    
----

參考文獻：

 1. [Laravel 6 中文文档](https://learnku.com/docs/laravel/6.x)
 2. [Laravel](https://laravel.com/)
 3. [Laravel 4.2 入門](https://legacy.gitbook.com/book/tony915/laravel4/details)

# 認識 Laravel
## Laravel 是什麼
php開發框架的一種，以目前有被使用來開發的php框架，Laravel為最受大眾使用的開發框架。

## 框架是什麼
框架(Framwork)，是一個被設計用來完成特定事務的規範。Programmer必須遵循規範來開發軟體或網站。現在大多框架都參考 [MVC架構](https://zh.wikipedia.org/wiki/MVC) 為概念來設計，因為早期都是 `HTML` + `php` 混雜的方式編撰，但這只會讓後續要維護的工程師非常困擾，光是在一堆html中找到php可以說是非常麻煩，長期維護是個大問題。
於是有人想到了把這些有各自任務的部份切割開來。MVC 架構可以把這些部份各自獨立成 Model-View-Controller（模型－視圖－控制器）。Model 屬於資料的部份，可能是商業邏輯或是資料庫存取等；View 屬於顯示的部份，像是 HTML、CSS 等；Controller 會偵對請求做出回應及處理，例如從 Model 中取得資料，並要求 View 來顯示。

## Laravel 框架
Laravel 是基於 MVC 架構模式來打造的框架，並且設計出許多讓開發者更有效率的工具。Laravel 的 Artisan 提供許多指令，讓你可以使用這些指令，快速的完成許多任務；它的 Blade 樣板系統，將程式碼與 HTML 頁面完整的分離，讓你專注在網頁頁面的設計；它的 Routing 機制，簡單卻強大的管理網址與頁面的路徑指定；利用 Controller 將程式邏輯隱藏在背後；Eloquent ORM 讓你再也不必撰寫任何的 SQL 指令就能和資料庫互動；利用 Migration 工具，讓資料庫的遷移不再是一件惱人的事。還有很多強大的功能，您將在使用後愛上它。
