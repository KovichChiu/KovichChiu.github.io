---  
layout: post  
title:  "Composer安裝 - Windows篇"  
preview: "Install composer in Windows"  
permalink: "/articles/2019-10-30-Install-composer-in-windows"
date:   2019-10-30 11:50:00 +08:00  
tags:  
    - "tools"  
comments: true  
---

當初在安裝 `Composer` 真的浪費好多時間，希望可以藉此幫助需要懶人包的新手  
<!-- more -->

重點摘要:  
* abstact  
{:toc}  
----
# 安裝流程  
* 第一步：下載 `Composer` 主程式。→ [點我下載](https://getcomposer.org/Composer-Setup.exe)  
* 第二步：安裝 `composer`。直接點擊安裝，完成後便可以在 `Cmd` 下 `composer` 的指令了。
* 第三部：`Cmd` 移動到安裝位址，並運行以下腳本。

```bash
php -r "copy('https://getcomposer.org/installer','composer-setup.php');" #安裝檔下載
php -r "if (hash_file('sha384', 'composer-setup.php') === 'a5c698ffe4b8e849a443b120cd5ba38043260d5c4023dbf93e1558871f1f07f58274fc6f4c93bcfd858c6bd0775cd8d1') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" #用sha-384驗證安裝檔
php composer-setup.php #執行安裝檔
php -r "unlink('composer-setup.php');" #刪除安裝檔
```
* 第四步：自動檢查`php.ini`配置問題，若有錯誤會跳出警告。目錄中的`composer.phar`則為最新版本。
* 第五步：複製一份`composer.phar`並更改副檔名為`composer.bat`

```bash
echo @php "%~dp0composer.phar" %*>composer.bat
```
* 第六步：重新啟動Cmd嘗試輸入：

```bash
composer -v #若顯示版本資訊則為安裝成功
```

# 基本使用
在開始使用composer之前，需要有一個`composer.json`
## 安裝的依附檔案
安裝依序檔案請執行`install`
```bash
php composer.phar install
```
執行完之後有兩種可能：

 1. 不安裝`composer.lock`
若之前都沒有執行過install，並且也沒有`composer.lock`的存在，則`composer`只需要解析`composer.phar`中的所有依附檔案，然後將其文件的最新版本下載到`vendor/`下(`vendor`為所有第三方代碼常規位址)。 
<font color="red">tips.</font> 如果需要使用git作為版本控制，可能需要將`vendor/`加入`.gitignore`中，否則第三方代碼將會全數上交`git`。
 2.  安裝`composer.lock`
代表其他人對於這個專案已經執行過`composer`，`composer.lock`作用是確保所有使用該專案的人`composer`內文件版本都相同。
