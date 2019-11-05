--- 
layout: post      
title:  "[PHP] 連結 MySQL 資料庫"      
preview: "[PHP] Connection of MySQL Database"      
permalink: "/articles/2019-11-05-php-Connection-of-MySQL-Database"    
date:   2019-11-05 17:00:00 +08:00      
tags:      
    - "php"      
comments: true      
---

連結資料庫的程式碼
<!-- more -->   

# 連結 MySQL 資料庫

重點摘要:      
* abstact      
{:toc}

```php
<?php

/*
 * 給出連結資料庫的基本資料
 */
$DBUSER = "user";        //資料庫登入帳號
$DBPASSWD = "password";  //資料庫登入密碼
$DBNAME = "db";          //資料庫名稱
$DBHOST = "localhost";   //資料庫位置

/*
 * 設定連線指令並給值
 * mysqli_connect( "位置", "ACCOUNT", "PASSWORD")
 */
$conn = mysqli_connect($DBHOST, $DBUSER, $DBPASSWD);

/*
 * 測試資料庫連結
 * 若 $conn 為 false，則 empty(false) = true;
 */
if (empty($conn)) {
    print mysqli_error($conn);
    die ("無法連結資料庫");
    exit;
}

/*
 * 判斷可否選擇資料庫
 * mysqli_select_db("連線指令", "資料庫名稱")
 */
if( !mysqli_select_db($conn, $DBNAME)) {
    die ("無法選擇資料庫");
}
mysqli_query( $conn, "SET NAMES 'utf8'"); //設定連線編碼

/*
 * 取得資料
 * "SQL語法"可用各式語法(長度不限)取代
 */
$sql ="SQL語法";
$result = mysqli_query($conn, $sql);

/*
 * 取出資料
 * 取回行數  mysqli_num_rows($conn, $result)     可得到資料有幾筆
 * 取回資料  mysqli_fetch_array("SQL語法", 模式)  可撈得依據語法所得到的資料
 * 模式：
 * MYSQLI_NUM
 * MYSQLI_ASSOC
 * MYSQLI_BOTH
 */
while ($row = mysqli_fetch_array($result, MYSQLI_NUM)) {
    print_r( $row);
}
/*
Array
(
    [0] => A123456789
    [1] => 1234
    [2] => 李小美
)
*/
while ($row = mysqli_fetch_array($result, MYSQLI_ASSOC)) {
    print_r( $row);
}
/*
Array
(
    [id] => A123456789
    [pass] => 1234
    [name] => 李小美
)
*/
while ($row = mysqli_fetch_array($result, MYSQLI_BOTH)) {
    print_r( $row);
}
/*
Array
(
    [0] => A123456789
    [id] => A123456789
    [1] => 1234
    [pass] => 1234
    [2] => 李小美
    [name] => 李小美
)
*/

/*
 * 取回物件形態資料
 * mysqli_fetch_object("SQL語法")
 */
while($obj = mysqli_fetch_object($result)){
    print ($obj->id);
}

/*
 * 取回最後一筆異動的索引
 * mysqli_insert_id($conn)
 * 要注意的情況：
 * - 一定要有一個欄位有 auto_increment 屬性，否則回傳0
 * - 一定之前要有insert或update操作，否則回傳0
 */
$last_id=mysqli_insert_id($conn);

/*
 * 關閉連線
 *
 * 釋放記憶體
 * mysqli_free_result ( $result )
 *
 * 關閉連線
 * mysqli_close($conn);
 */
mysqli_free_result ( $result );
mysqli_close($conn);
```
