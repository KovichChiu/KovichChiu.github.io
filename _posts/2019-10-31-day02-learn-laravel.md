--- 
layout: post    
title:  "[DailyLearning] #02 Laravel 學習之路"    
preview: "[DailyLearning] #02 Laravel Learning"    
permalink: "/articles/2019-10-31-day02-learn-laravel"  
date:   2019-10-31 09:10:00 +08:00    
tags:    
    - "laravel"    
comments: true    
--- 
  
`Laravel`把所有的應用設定都放在那裡了，去找吧，想要的話全部都給你 ─ `config/`。 
目錄結構像是地牢嗎？請容我娓娓道來...
<!-- more -->    

重點摘要:      
* abstact      
{:toc}

# 組態
## 環境組態
對於整個應用在運作的過程中，環境變數的設定很重要。比如：你可能會希望 local 與 server 所使用的快取是不同的。
為了做到這一點，Laravel 使用了 [DotEnv](https://github.com/vlucas/phpdotenv)，全新的Laravel專案中，root目錄底下會有一個 `.env.example`，透過 Composer 安裝 Laravel，則會自動更名`.env`，否則需要手動喔。

*`.env` 包含許多敏感憑證，加上每個 Developer 使用到的環境變數是不同的，所以 `.env` 是不能放進 VC SourceCode 中的*

整個開發的過程，會希望 `.env.example` 還包含在 root 目錄底下，可以快速地切換環境變數，讓你更快速、清楚的感受到差異。你也可以創建一個 `.env.testing` 在 run PHPUnit 進行測試時，或是以 `--env=testing` 當作選項執行 `artisan` 指令時，將會直接 cover  `.env` 的設定。

> Tip: `.env` 中的所有變數都可以被**外部**變數(Server 或 System等級的環境變數)覆蓋。

## 環境變數型態
`.env` 所有的變數的型態都被 parsed(解析) 成 <font color=red>String</font>，為此，可以使用 `env()`，來得到更多變數型態。

| `.env`值  |  `env()`值  |
| -------- | ----------- |
|   true   | (bool)true  |
|  (true)  | (bool)true  |
|   false  | (bool)false |
|  (false) | (bool)false |
|   empty  | (String)""  |
|  (empty) | (String)""  |
|   null   | (null)null  |
|  (null)  | (null)null  |

**如果需要使用「包含」空格的變數，請用雙引號包起來**

```php
APP_NAME="My Application"
```

## 查看環境組態
當接收到請求的時候，所有被列出的變數，將會被載入到 PHP的超級全域變數 `$_ENV`中。然而，你可能會使用 `env` helper 來查詢所有組態變數的值。如果你看遍所有 Laravel 組態檔案，你可能會發現，一些選項已經加入 `env()` 。

```php
'debug' => env('APP_DEBUG', false), //env()會回傳'APP_DEBUG'的值(K-V Pair)
```
> 第二個值 `false` 是「**預設值**」，目的是當該環境變數不存在值時(key沒有value)，便會使用 `false`。

## 確定當前環境
整個應用環境都是靠著 `.env` 中 `APP_ENV` 來決定的，可以用 `App` [facade](https://laravel.com/docs/6.x/facades) 中的 `environment` 函數來訪問。

```php
$environment = App::environment();
```
你可以傳遞參數給 `environment()` 檢查是否與給定值相同，若相同回傳 `true`。

```php
if (App::environment('local')) {
    // The environment is local
}

if (App::environment(['local', 'staging'])) {
    // The environment is either local OR staging...
}
```
> 因為會被 server 級的 `APP_ENV` 覆蓋，所以這方法非常好用，當你需要為不同的環境配上同一個應用程式時，可以用配給「服務器配置中的給定環境」一個「給定的主機」的方式來實現。

## 在測試站隱藏環境變數
如果有一個例外(exception)沒有被抓到而且 `APP_DEBUG` 這個環境變數回傳是為 `true`，那這個測試站會秀出所有的環境變數的 Key - Value。在某些情況下，我們可以藉由設定 `config/app.php` 中的 `debug_blacklist` 選項，來隱藏某些變數。

在**環境變數**和**伺服器/請求資料**中有一些接可以使用的變數，你需要將 `$_ENV` 跟 `$_SERVER` 加入黑單。

```php
return [

    // ...

    'debug_blacklist' => [
        '_ENV' => [
            'APP_KEY',
            'DB_PASSWORD',
        ],

        '_SERVER' => [
            'APP_KEY',
            'DB_PASSWORD',
        ],

        '_POST' => [
            'password',
        ],
    ],
];
```

## 訪問組態資料
任何時間任何位置你都可以在應用中利用全域函數 `config` 來輕鬆地取得組態資料。你可以用「.」來連接**檔案名稱**以及**任何你需要的Key**以取得組態資料。也可以指定 `default value` ，若選項不存在則 `return default value`。

```php
$value = config('app.timezone'); //app.php 中的 timezone 選項
```
也可以丟陣列給 `config` 函數來設定選項的值。

```php
config(['app.timezone' => 'Asia/Taipei']);
```
`timezone`內容請參考：[維基百科Wikipedia: # List of tz database time zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)

## 快取組態
如果想要提升整體應用的速度，我們可以利用 `artisan` 指令 `config:cache`，來把所有的組態都快取到一個檔案中，這樣框架就能快速載入它。

通常會將 `php artisan config:cache` 當作專案生產部署常規的一部分去執行。但在整個專案的開發過程中，由於環境變數很容易被改變，所以不會將該指令在本地開發的時候執行。

> 注意：當處理部署的時候如果要執行 `config:cache`，應該要確定在環境組態的檔案中，你只用到 `env()` 函數。一旦環境組態被快取了，`.env` 將不會再次被載入，而所有使用 `env()` 函數呼叫的回應都會是 `null`。

## 站點維護模式
當專案進入維護模式後，所有的回應將會被一個固定的頁面遮蓋，讓我們可以更輕鬆的去更新整個專案。維護模式檢查被包含在框架的中間軟體堆(middleware stack，中間軟體→介於複雜的系統中間，協助通訊或應用程式的port；堆→一組協同工作，以支援軟體的**開發**、**維護**和**操作技術**。)，且在維護模式中，將會丟出 503 狀態，還有一個 `MaintenanceModeException` 例外。

執行 `down` 這個 Artisan 指令就可以進入站點維護模式囉。

```php
php artisan down
```
我們可以用 `down` 來下 `message` 跟 `retry` 這兩個項目，`message` 可以顯示或記錄狀態；而 `retry` 可以設定 HTTP header `Retry-After` 的值。

```php
php artisan down --message="Upgrading Database" --retry=60
```
也可以設定在站點維護模式時，允許進入的IP位置。

```php
php artisan down --allow=127.0.0.1 --allow=192.168.0.0/16
```
當需要結束站點模式時，`up` 專案吧。

```php
php artisan up
```
> 我們可以從 `resources/views/errors/503.blade.php` 來客製化自己的站點維護模式頁面喔。

### 維護模式與隊列
在維護模式的狀態下，會暫停一切隊列，直到回復到正常模式才會繼續被執行。

### 維護模式的替代方案
維護模式的時候會有幾秒鐘的停機部署，可以考慮使用像是 [Envoyer](https://envoyer.io/) 的替代方案來實現 Laravel 的**零停機部署**。

# 目錄結構
## 簡介
為了要給開發者有更好的體驗，Laravel幾乎可以說是對於目錄本身沒有任何限制。只要你有任何可以由 Composer Autoload 的項目，都可以很輕鬆的被載入你的專案中，甚至可以什麼事都不做。

### 放 Models 的目錄呢？去哪了？
建立起專案後，大家一定都很困惑：「欸阿我把 Laravel 都弄起來了，Models 勒？怎麼找不到」。
好啦，Laravel 故意的，不過這是因為「Models」對於世界各地的開發者來說代表很多不一樣的意思，簡單來說，太曖昧了啦，所以才將它拿掉的。
有些開發者們將「Models」作為整個專案的邏輯庫；也有些將它製作成與資料庫有高度關係的資料庫交互類別。

以上種種原因，Laravel 選擇將充滿爭議的「Models」預設放進 `app/` 目錄，當然你也可以依照個人需求改變它的位置。

## 根目錄
### App 目錄
這個 `app/` 目錄，裡面放著整個專案的核心程式碼，幾乎所有的類別都應該要放在這邊，稍後會更詳盡的探討它。

### Bootstrap 目錄
`bootstrap` 目錄包含了引導整個框架的 `app.php`，也包含了 `cache/` 目錄，`cache/` 目錄底下存放著由框架針對強化性能所產生的檔案，例如路由、伺服器快取檔。

### Config 目錄
裡面包含了整個專案的所有組態檔案，可以藉由研讀這些檔案更深入的了解所有可以使用的選項。

### Database 目錄
`database/` 目錄包含遷移檔、Model庫、還有一些源種子碼，你也可以用他來實現一個SQLite DB。

### Public 目錄
`public/` 目錄包含了整個專案回應所有請求的進入點 ─ `index.php`，這邊也會放著所有你會用到的資源，比如 Images、JavaScript 和 CSS。

### Resources 目錄
`resources/` 目錄包含了視圖及還沒編譯的資源，比如：LESS、SASS 和 Javascript，也包含了所有語言檔。

### Routes 目錄
`routes/` 目錄包含了整個專案的路由定義，預設有 `web.php`、`api.php`、`console.php` 和 `channels.php` 四個路由定義。

#### web.php
包含了放在 `web` 這個中間軟體群的 `RouteServiceProvider` 路由，它提供了 Session 狀態、CSRF 保護還有 cookie 加密。如果你的專案沒有提供「無狀態的」、「RESTful API(被轉化成Rest架構的Web API)」，則所有的路由最後都建立在 `web.php` 中。

#### api.php
包含了放在 `api` 這個中間軟體群的 `RouteServiceProvider` 路由，它提供了速度限制，這些路由都是無狀態的，所以藉由這些路由進入的所有請求，主要是為了經過 tokens 來取得認證，且拿不到任何 Session 狀態。

#### console.php
可以在這裡定義所有根據控制台指令的閉包，每個閉包都被綁上一個命令實例，允許一種簡單的方法來與每個命令的 IO 方法進行交互。雖然這個檔案沒有定義HTTP路由，依然有定義根據控制台的進入點(路由)來進入到整個專案。

#### channels.php
只要你的專案能夠支援，你可以在這上面註冊任何事件廣播的頻道。

### Storage 目錄
`storage/` 目錄包含了我們編譯的 Blade 模板、Session所生成的檔案、快取檔案和其他由框架所生成的檔案。這個目錄被分隔成 `app/`、`framework/`、`logs/`三個目錄。`app/` 存放任何由你的專案產生的檔案；`framework/` 存放了由框架所產生的檔案與快取；`logs/` 包含了整個專案的 log 檔。

在 `storage/app/` 中有一個 `public/` 目錄，用來存放一些可以被公開的資料，比如：使用者的大頭貼等。你應該創建一個象徵性的鏈結 `public/storage` 來指向這個目錄。

```bash
php artisan storage:link //你可以下這個指令來建立
```
### Tests 目錄
`tests/` 目錄包含了自動化的測試檔案，[PHPUnit](https://phpunit.de/) test 就是一個開箱即用的很好的範例。每個測試類別，都應以 `Tset` 為後墜，可以用 `phpunit` 或  `php vendor/bin/phpunit` 指令來測試。

### Vendor 目錄
`vendor/` 放著所有 [Composer](https://getcomposer.org/) 的依存檔案。

## App 目錄
我們的專案大多都是放在 `app/` 下，一般來說，這個目錄被放在 `App` 這個命名空間下，而且藉由 Composer 根據 [PSR-4 autoloading standard](https://www.php-fig.org/psr/psr-4/) 來自動載入。

`app/` 下包含了 `Console`、`Http`、`Providers`，將 `Console` 和 `Http` 目錄視為向專案的核心提供 API。HTTP 協議和 CLI 都是與應用程式交互的機制，但實際上並不包含應用程式邏輯。換句話說，它們是向你的應用程式發出指令的兩種方式。 `Console/` 包含所有的 Artisan 指令，而 `Http/` 目錄包含你的Controllres，中間軟體和請求。

如果有其他的目錄想要放進app中，可以用 `make` 這個 Artisan 指令。舉例來說，我今天想要在 `app/` 下建立一個新的類別 `job/`，那我只能用 `make:job` 的方式來新建它，直接建立目錄是不會被 Laravel 吃到的喔。

> 因為會友許多類別通過 Artisan 指令產生在`app/`下。為了查看可用的指令可以執行以下指令
>
> ```php
> php artisan list make
> ```

#### Broadcasting 目錄

`Broadcasting/`目錄包含應用程式的所有廣播通道類。這些類使用`make:channel`指令生成。此目錄預設是不存在的，但是當你創建第一個通道時它將被創建。要了解更多的關於頻道的信息，查看有關文檔[事件廣播](https://learnku.com/docs/laravel/6.x/broadcasting)。

#### Console 目錄

`Console/`目錄包含應用程式的所有自定義 Artisan 指令。這些指令可以使用`make:command`指令生成。此目錄也安置了控制台內核，在其中你可以註冊自定義的 Artisan 指令，並定義[計劃任務](https://learnku.com/docs/laravel/6.x/scheduling)。

#### Events 目錄

預設情況下這個目錄是不存在的，但你可以通過`event:generate`和`make:event` Artisan 指令去創建。`Events/`目錄安置[事件類](https://learnku.com/docs/laravel/6.x/events)。事件可用於提醒您的應用程式其它部分已發生給定操作，提供了極大的靈活性和解耦。

#### Exceptions 目錄

`Exceptions/`目錄包含應用程式的異常處理，並且也是一個放置應用程式拋出任何異常的好地方。如果你想自定義異常的記錄和渲染方式，你應該修改此目錄中的 `Handler` 類。

#### Http 目錄

`Http/`目錄包含你的控制器，中間件和表單請求。處理進入應用程式請求的所有邏輯幾乎都放置在此目錄。

#### Jobs 目錄

預設情況下這個目錄是不存在的，但如果你執行 `make:job` Artisan指令時，它將被創建出來。`Jobs/`目錄安置應用程式的[隊列任務](https://learnku.com/docs/laravel/6.x/queues)。Jobs可由應用程式排隊作業，也可以在當前請求的生命週期內同步運行。在當前請求期間同步運行的Jobs有時會稱為『指令』，因為它們是[指令模式](https://en.wikipedia.org/wiki/Command_pattern)的一個實現。

#### Listeners 目錄

預設情況下這個目錄是不存在的，但如果你執行了 `event:generate` 或者 `make:listener` Artisan 指令時，它將會被創建出來。`Listeners` 目錄包含[事件](https://learnku.com/docs/laravel/6.x/events)的處理類。事件偵聽器接收一個事件實例並執行邏輯以響應被觸發的事件。例如，一個 `UserRegistered` 事件可能被 `SendWelcomeEmail` 偵聽器處理。 

#### Mail 目錄

預設情況下這個目錄是不存在的，但如果你執行 `make:mail` Artisan 指令，它將被創建出來。 `Mail` 目錄包含應用程式發送郵件的所有類。郵件對象允許你去構建一個封裝所有邏輯的郵件類，在這個類中可以使用 `Mail::send` 方法發送郵件。

#### Notifications 目錄

預設情況下這個目錄是不存在的，但如果你執行`make:notification`Artisan 指令，它將被創建出來。`Notifications`目錄包含應用程式的發送的所有『事務』通知，比如關於應用程式中發生的事件的簡單通知。Laravel 的通知功能抽象了通過各種驅動（如：電子郵件，Slack，SMS 或者存儲在數據庫中）去發送通知。

#### Policies 目錄

預設情況下這個目錄是不存在的，但如果你執行`make:policy`Artisan 指令，它將被創建出來。`Policies`目錄包含應用程式的授權策略類。策略用於確定一個用戶是否對一個資源能否執行一個給定的操作。有關更多信息，查看[授權文檔](https://learnku.com/docs/laravel/6.x/authorization)。

#### Providers 目錄

`Providers/`目錄包含應用程式的所有 [服務提供者](https://learnku.com/docs/laravel/6.x/providers)。服務提供者透過在服務容器中綁定服務引導應用程式，註冊事件或者準備為應用程式即將到來的請求執行其它任何任務。

在一個新的Laravel 應用中，此目錄已經包含一些提供者。你可以根據需要隨意將你自己的提供者添加到此目錄中。

#### Rules 目錄

預設情況下這個目錄是不存在的，但如果你執行 `make:rule` Artisan 指令，它將被創建出來。`Rules/`目錄包含應用程式的自定義驗證規則對象。規則用於將復雜的驗證邏輯封裝在一個簡單對象中。關於更多訊息，查看 [驗證文檔](https://learnku.com/docs/laravel/6.x/validation)。
