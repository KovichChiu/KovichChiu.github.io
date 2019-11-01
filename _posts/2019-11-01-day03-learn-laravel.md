--- 
layout: post    
title:  "[DailyLearning] #03 Laravel 學習之路"    
preview: "[DailyLearning] #03 Laravel Learning"    
permalink: "/articles/2019-11-01-day03-learn-laravel"  
date:   2019-11-01 09:10:00 +08:00    
tags:    
    - "laravel"    
comments: true    
--- 

Homestead的安裝與架構 
<!-- more -->  

重點摘要:    
* abstact    
{:toc}    
----

# Laravel Homestead
## 介紹
Homestead 可以在任何系統中運行，Windows、Mac、Linux等。
Laravel Homestead 包含：

* Nginx web server
* PHP 7.1 - 7.3
* MySQL
* PostgreSQL
* Redis
* Memcached
* Node 

> Tips. Windows 需要至 Bios 啟用硬體虛擬化技術(VT-x)。若你是在 Hyper-V 系統上使用 UEFI ，也必須禁用 Hyper-V 才能使用 VT-x。

### 內建軟體

-   Ubuntu 18.04
-   Git
-   PHP 7.3
-   PHP 7.2
-   PHP 7.1
-   PHP 7.0
-   PHP 5.6
-   Nginx
-   MySQL
-   lmm for MySQL or MariaDB database snapshots
-   Sqlite3
-   PostgreSQL
-   Composer
-   Node (With Yarn, Bower, Grunt, and Gulp)
-   Redis
-   Memcached
-   Beanstalkd
-   Mailhog
-   avahi
-   ngrok
-   Xdebug
-   XHProf / Tideways / XHGui
-   wp-cli

### 可選軟體

-   Apache
-   Blackfire
-   Cassandra
-   Chronograf
-   CouchDB
-   Crystal & Lucky Framework
-   Docker
-   Elasticsearch
-   Gearman
-   Go
-   Grafana
-   InfluxDB
-   MariaDB
-   MinIO
-   MongoDB
-   MySQL 8
-   Neo4j
-   Oh My Zsh
-   Open Resty
-   PM2
-   Python
-   RabbitMQ
-   Solr
-   Webdriver & Laravel Dusk Utilities

## 安裝與設定
### 第一步
首先，要有一個可以架設虛擬機的軟體，[VirtualBox 6.x](https://www.virtualbox.org/wiki/Downloads)、[VMWare](https://www.vmware.com/)、[Parallels](https://www.parallels.com/products/desktop/)、[Hyper-V](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)。當然啦，VMware 是需要花錢的，預算有限的話我很推薦 VirtualBox ，因為沒有太多的其他限制，也不需要華麗且複雜的操作。

### 安裝 Homestead Vagrant Box
VirtualBox + Vagrant 都安裝好了，就可以開始安裝 Homestead 了。我們打開終端機把`laravel/homestead box` 加進 Vagrant 進行安裝，因為下載連線穩定度有限，指令下好後可以先閱讀後續步驟當預習喔。

``` bash
vagrant box add laravel/homestead
```
若不成功，請確認 Vagrant 版本是否為最新版。

> Tips. Homestead 會定期發出「alpha」/「beta」box用於測試，這可能會影響 `vagrant box add` 命令。如果你在運行 `vagrant box add` 時遇到問題，可以運行 `vagrant up` 命令，當 Vagrant 嘗試啟動虛擬機時，將下載正確的 box。

### 安裝 Homestead
用 `git clone` 來下載 Homestead 的程式碼，建議是裝在 Homestead 下，Windows 一般應該都是安裝在`user/`底下，所以先切換到 `C:\Users\_username_\Homestead\` 再 clone 吧。

```bash
git clone https://github.com/laravel/homestead.git
```
切記，因為 Homestead `master` 並非穩定版，所以要去找分支上有標籤註記是穩定版的版本 clone 喔，或是你可以直接去找包含最新穩定版本的 `release` 分支。

```bash
git checkout release
```
clone 完成後，直接在 Homestead 目錄下指令來創建 `Homestead.yaml` 設定檔。

```bash
# Mac / Linux...
bash init.sh

# Windows...
init.bat
```
### 設定 Homestead
#### 設定VM
`Homestead.yaml` 有一個 `provide` 參數決定我們使用的 VM。 `virtualbox`、`vmware_fusion`、`vmware_workstation`、`parallels`、`hyperv`。

```yaml
provider: virtualbox
```
#### 設定共享目錄
`Homestead.yaml` 有一個 `folders` 參數，裡面列出所有與 Homestead 共享的目錄，可以多個目錄設定。

```yaml
folders:  
    - map:  ~/code/project1         # 對應主機的位置
      to:  /home/vagrant/project1   # 虛擬機的位置	 
```

> Tips. Windows路徑設定要小心，`/` 即為跟目錄，會指向 `C:\` 所以路徑應該設為 `/User/_yourpath_`。

建議對應的方式為一個project對應一個資料夾，不要整個大資料夾對應整個大資料夾，因為當專案越來越多，只會降低整體運作的效能。

```yaml
folders:  
    - map:  ~/code/project1 
      to:  /home/vagrant/project1 
	  
    - map:  ~/code/project2 
      to:  /home/vagrant/project2
```

> Tips. 使用 Homestead 時，千萬不要使用`.` (當前目錄)，這樣做會讓 Vagrant 抓不到，設定時容易噴錯。

若要打開 [NFS](https://www.vagrantup.com/docs/synced-folders/nfs.html) 我們可以加上設定。

```yaml
folders:  
    - map:  ~/code/project1 
      to:  /home/vagrant/project1 
      type:  "nfs"
```

> Tips. 使用 NFS 應額外加上 [vagrant-winnfsd](https://github.com/winnfsd/vagrant-winnfsd) Plugin，他能夠幫我們處理權限問題。

我們也可以用 `options` 來設定整體 Vagrant 的 [共享目錄](https://www.vagrantup.com/docs/synced-folders/basic_usage.html) 能支援的所有選項。

```yaml
folders:
    - map: ~/code/project1
      to: /home/vagrant/project1
      type: "rsync"
      options:
          rsync__args: ["--verbose", "--archive", "--delete", "-zz"]
          rsync__exclude: ["node_modules"]
```
#### 設定 Nginx 站
`Homestead.yaml`中的`sites` 可以直接幫我們對應一個域名，不過這只有本機端能解析，跟 `folders` 做法一樣，可以無限對應。

```yaml
sites:
    - map: homestead.test
      to: /home/vagrant/project1/public
```
如果啟動 VM 後有修改 sites，一定要 `vagrant reload --provision` 來重新設定Nginx。

> Tips. 若 provision 失敗或有問題，`vagrant destroy && vagrant up` 來重新建構虛擬機就好了。

#### 主機名稱解析
Homestead 是利用 `mDNS` 來發布域名，如果在 `Homestead.yaml` 有設定 `hostname: homestead` 那主機名稱會是 `homestead.local`，MacOS、iOS 和 Linux 系統有支援 `mDNS`，Windows 需要額外安裝 [Bonjour Print Services for Windows](https://support.apple.com/kb/DL999?viewlocale=zh_CN&locale=en_US)。

如果一個 Homestead 上有很多站點，可以將域名添加到 `hosts` 檔案中，他會幫我們將請求重新定位到虛擬機，Mac / Linux 中，檔案位在 `/etc/hosts`，Windows 則是在 `C:\Windows\System32\drivers\etc\hosts`，我們用 `homestead.test` 為例子，添加到 hosts 中。

```bash
#請確認與[Homestead.yaml]上設定的[sites]一致。
192.168.10.10  homestead.test	#http://homestead.test
```
#### 啟動 Vagrant 虛擬機

```bash
#建立VM
vagrant up

#刪除VM
vagrant destroy	#[--force]可以強制刪除

#VM預設 Account / password 為 vagrant / vagrant
```

#### 針對項目安裝

除了全域安裝，並且在每個項目中共享 `Homestead.yaml` 設定，也可以把 Homestead 裝在每個不同的項目中，只需要將 `vagrantfile` 裝在目錄中。只需要進到該目錄並執行 `vagrant up` 就可以擁有相同的開發環境。

```php
#要將 Homestead 直接安裝到目錄中，需要執行以下指令。
composer require laravel/homestead --dev
```
安裝之後需要使用 `make` 來產生 `Vagrantfile` 和 `Homestead.yaml`，它會自動配置好 `sites` 和 `folders`。

Mac / Linux

```php
php vendor/bin/homestead make
```
Windows

```php
vendor\\bin\\homestead make
```
接下來就可以在相同域名下連結到這個server。

#### 安裝可選功能
`Homestead.yaml` 中可以使用 `features` 選項來啟用各項尚未啟用的功能。

```yaml
features:
    - blackfire:
        server_id: "server_id"
        server_token: "server_value"
        client_id: "client_id"
        client_token: "client_value"
    - cassandra: true
    - chronograf: true
    - couchdb: true
    - crystal: true
    - docker: true
    - elasticsearch:
        version: 7
    - gearman: true
    - golang: true
    - grafana: true
    - influxdb: true
    - mariadb: true
    - minio: true
    - mongodb: true
    - mysql8: true
    - neo4j: true
    - ohmyzsh: true
    - openresty: true
    - pm2: true
    - python: true
    - rabbitmq: true
    - solr: true
    - webdriver: true
```

[詳細說明請參考原出處](https://laravel.com/docs/6.x/homestead)

#### Aliases 別名
想為機器添加	Bash 別名，編輯 `Homestead/aliases` 檔案。

```php
alias c='clear'
alias ..='cd ..'
```
修改後執行 `vagrant reload --provision` 使 `aliases` 檔案生效。

## 日常使用
### 隨時執行 `vagrant up`
專案開發的過程中，可能會需要多次的使用 `vagrant up`。但總不能每次打開終端機，都要先 cd 一大堆路徑才能開始工作吧。依據不同系統，有不同的方式。
#### Mac / Linux
修改 Bash( `~/.bash_profile` )

```php
function  homestead()  {  
	( cd ~/Homestead && vagrant $*  )  # ~/Homestead 的位置請依據個人的狀況填寫
}
#如此一來就可以在任何時候使用 homestead up / homestead ssh 等指令
```
#### Windows
首先建立`homestead.bat`。

```php
@echo off

set cwd=%cd%
set homesteadVagrant=C:\Homestead

cd /d %homesteadVagrant% && vagrant %*
cd /d %cwd%

set cwd=
set homesteadVagrant=
```
修正`C:\Homestead` 路徑為真實路徑，並且將檔案加入`PATH` ( [PATH在哪裡](https://www.java.com/zh_TW/download/help/path.xml) ) 。

### 用 ssh 連接
`vagrant ssh`

### DB 連接
```
127.0.0.1:33060 // MySQL

127.0.0.1:54320 // PostgreSQL
```
Account：`homestead`
Password：`secret`

> 本機連結的時後才用這些 port，Laravel 本身在運作的時候還是使用 3306 / 5432 port。

### 備份 DB
### DB 快照
### 添加站點
### 站點類型
我們可以很快地增加一個非 Laravel 的站點。`apache`、`apigility`、`expressive`、`laravel` (the default)、`proxy`、`silverstripe`、`statamic`、`symfony2`、`symfony4`、`zf`。

```yaml
sites:
    - map: symfony2.test
      to: /home/vagrant/my-symfony-project/web
      type: "symfony2"
```
### 站點參數
`params` 可以增加額外的 Nginx `fastcgi_param` 值至站點。比如：`FOO` => `BAR`

```yaml
sites:
    - map: homestead.test
      to: /home/vagrant/project1/public
      params:
          - key: FOO
            value: BAR
```
### 環境變數
`Homestead.yaml` 中新增全域變數。

```yaml
variables:
    - key: APP_ENV
      value: local
    - key: FOO
      value: bar
```

其餘項目請參考[原文](https://laravel.com/docs/6.x/homestead)

## 補充
由於上述所有事情單純只是架設 server，還記得我們剛剛在 `sites` 有指定位置嗎，在本機到該位置依照前幾天的安裝laravel的方式，裝一次在該目錄中，並重新指定位置到 `public/` 就可以囉。
