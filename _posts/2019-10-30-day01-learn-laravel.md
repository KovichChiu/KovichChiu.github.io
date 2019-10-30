--- 
layout: post    
title:  "[DailyLearning] #01 Laravel 學習之路"    
preview: "[DailyLearning] #01 Laravel Learning"    
permalink: "/articles/2019-10-30-day01-learn-laravel"  
date:   2019-10-30 14:10:00 +08:00    
tags:    
    - "laravel"    
comments: true    
--- 

學習`Laravel`最大的困難就是安裝了吧，一開始也是安裝了老半天，那就來幫自己做個複習吧。 
<!-- more -->  

重點摘要:    
* abstact    
{:toc}  

# 安裝 Laravel
## 安裝
### 伺服器要求
Laravel 有一些對於server的要求，不過也有快捷又方便的方法 ─ `Laravel Homestead`，因此強烈堆建使用 [Homestead](#Homestead) 作為開發環境。

不過若不使用`Homestead`，Server需要滿足以下要求：
-   PHP >= 7.2.0
-   BCMath PHP Extension
-   Ctype PHP Extension
-   JSON PHP Extension
-   Mbstring PHP Extension
-   OpenSSL PHP Extension
-   PDO PHP Extension
-   Tokenizer PHP Extension
-   XML PHP Extension
 
### 安裝 Laravel
`Laravel`使用Composer來管理項目依賴，所以請確保已經安裝Composer。
#### 透過 Laravel 安裝器安裝
首先使用Composer進行Laravel安裝器的下載。

```bash
composer global  require laravel/installer
```
請確定 `Composer's system-wide vendor` 目錄有存在於系統環境變量`$PATH`中，才能使系統找到Laravel的可執行檔案，依據系統的不同，有以下幾種配置。

 - macOS and GNU / Linux ：`$HOME/.config/composer/vendor/bin`
 - Windows：`%USERPROFILE%\AppData\Roaming\Composer\vendor\bin`
安裝完成後，`laravel new`指令會在你所在的目錄創建一個全新的Laravel專案。

```bash
#laravel new [project name] 這邊以Blog為例。
laravel new Blog
#若要直接在該目錄建立Laravel專案，請使用[./]。
laravel new ./
#也可以使用Composer來製作專案，利用 composer create-project。
composer create-project --prefer-dist laravel/laravel Blog
```
#### 本機開發環境
如果你在本機安裝了`php`，可以使用 php 的內置 server 來啟用 Laravel server。

```bash
#首先，cd 至 Laravel 專案中
php artisan serve #該命令會在[http://localhost:8000]開啟server
```
不過，最好還是選擇[Homestead](#Homestead)和[Valet](#Valet)。

### 配置

#### public 路徑
安裝完成後，必須將web service的目錄指向`public/`目錄，該目錄底下的`index.php`為系統進入點以及回應所有HTTP請求的前端控制器。

#### config 文件
Laravel 框架的所有config文件都存放在 `config/` 目錄，每個選項都有文字敘述，可以查看文件來熟悉對你有用的選項。

#### 目錄權限
安裝 Laravel 後，你可能會遇到權限問題，`storage/` 和 `bootstrap/cache` 目錄在 web service 下應該是可寫的權限，否則 Laravel 無法執行，如果你用的是 [Homestead](#Homestead) 虛擬機，這些權限應該已經設置好了。

#### 程式金鑰
安裝好後下一步是設置程式金鑰(Application Key)，如果是透過 Composer 或 Laravel 安裝器安裝的，這個 key 已經透過 `php artisan key:generate` 指令安裝好了。

通常，金鑰是32字元的長度，並且被儲存在環境變量檔 `.env` 中。如果你還沒有將`.env,example` 重新命名為 `.env`，那麼請現在就重新命名吧。**如果沒有設置 key ，Client and Server的Session 和其他加密數據會不安全**。

#### 其他配置
Laravel 基本上已經可以運作了，當然你還可以在`config/app.php`更深入的了解每個註解說明，比如 `timezone` 和 `locale`。
你還可能需要配置 Laravel 的其他組件，例如：

-   [Cache(外部連結)](https://learnku.com/docs/laravel/6.x/cache#configuration)
-   [Database(外部連結)](https://learnku.com/docs/laravel/6.x/database#configuration)
-   [Session(外部連結)](https://learnku.com/docs/laravel/6.x/session#configuration)


## Server配置
### 目錄配置
Laravel 應該始終在 Server 配置的 `root dir` 也就是一開始提到的 `public/` 之外編撰，不應該嘗試從該根目錄中編撰任何程式碼，可能會暴露程式內任何的敏感文件。

### 好看的網址
#### Apache
Laravel 中包含了一個`public/.htaccess`的文件，通常用於在資源路徑中隱藏`index.php`的前端控制器。用Apache提供服務之前，先確定啟用`mod_rewrite` 模組，這樣 `.htaccess` 才能被server解析。

如果Laravel附帶的`.htaccess`文件不起作用，嘗試下面的方法替代：
```bash
Options +FollowSymLinks -Indexes 
RewriteEngine On  

RewriteCond %{HTTP:Authorization} . 
RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]  

RewriteCond %{REQUEST_FILENAME} !-d 
RewriteCond %{REQUEST_FILENAME} !-f 
RewriteRule ^ index.php [L]
```
#### Nginx
如果用 Nginx 直接配置站點位置請求在`index.php`就好
```nginx
location / {
	try_files $url $url/ /index.php?$query_string
}
```
使用 [Homestead(外部連結)](https://laravel.com/docs/6.x/homestead) 或 [Valet時(外部連結)](https://laravel.com/docs/6.x/valet)，將自動配置漂亮的URL。
