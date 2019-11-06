--- 
layout: post      
title:  "[PHP] Boolean 比較"      
preview: "[PHP] Boolean Comparison"      
permalink: "/articles/2019-09-30-php-Boolean-Comparison"    
date:   2019-09-30 12:30:00 +08:00      
tags:      
    - "php"      
comments: true      
---

各種布林值的差異
<!-- more -->   

# 判斷結果列表 

資料來源：[PHP type comparison tables](https://www.php.net/manual/en/types.comparisons.php)

重點摘要:      
* abstact      
{:toc}
 
## 比較 $x 在各函數中的結果

| Expression      | gettype() | empty() | is_null() | isset() | boolean : if($x) |
| --------------: | :-------: | :-----: | :-------: | :-----: | :--------------: |
| `$x = "";`      | string    |  TRUE   |  FALSE    |  TRUE   |  FALSE           |
| `$x = null;`    | NULL      |  TRUE   |  TRUE     |  FLASE  |  FALSE           |  
| `var $x;`       | NULL      |  TRUE   |  TRUE     |  FLASE  |  FALSE           |
|`$x is undefined`| NULL      |  TRUE   |  TRUE     |  FLASE  |  FALSE           |
| `$x = array();` | array     |  TRUE   |  FALSE    |  TRUE   |  FALSE           |
|`$x = array('a', 'b');`|array|  FALSE  |  FALSE    |  TRUE   |  TRUE            |
| `$x = false;`   | boolean   |  TRUE   |  FALSE    |  TRUE   |  FALSE           |
| `$x = true;`    | boolean   |  FALSE  |  FALSE    |  TRUE   |  TRUE            |
| `$x = 1;`       | integer   |  FALSE  |  FALSE    |  TRUE   |  TRUE            |
| `$x = 42;`      | integer   |  FALSE  |  FALSE    |  TRUE   |  TRUE            |
| `$x = 0;`       | integer   |  TRUE   |  FALSE    |  TRUE   |  FALSE           |
| `$x = -1;`      | integer   |  FALSE  |  FALSE    |  TRUE   |  TRUE            |
| `$x = "1";`     | string    |  FALSE  |  FALSE    |  TRUE   |  TRUE            |
| `$x = "0";`     | string    |  TRUE   |  FALSE    |  TRUE   |  FALSE           |
| `$x = "-1";`    | string    |  FALSE  |  FALSE    |  TRUE   |  TRUE            |
| `$x = "php";`   | string    |  FALSE  |  FALSE    |  TRUE   |  TRUE            |
| `$x = "true";`  | string    |  FALSE  |  FALSE    |  TRUE   |  TRUE            |
| `$x = "false";` | string    |  FALSE  |  FALSE    |  TRUE   |  TRUE            |

## 用 == 寬鬆的比較

|        | TRUE  | FALSE |   1   |   0   |  -1   |  "1"  |  "0"  | "-1"  | NULL  | array() | "php" | ""   
| -----: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :-----: | :---: | :---:
| TRUE   | TRUE  | FALSE | TRUE  | FALSE | TRUE  | TRUE  | FALSE | TRUE  | FALSE | FALSE   | TRUE  | FALSE 
| FALSE  | FALSE | TRUE  | FALSE | TRUE  | FALSE | FALSE | TRUE  | FALSE | TRUE  | TRUE    | FALSE | TRUE  
|   1    | TRUE  | FALSE | TRUE  | FALSE | FALSE | TRUE  | FALSE | FALSE | FALSE | FALSE   | FALSE | FALSE 
|   0    | FALSE | TRUE  | FALSE | TRUE  | FALSE | FALSE | TRUE  | FALSE | TRUE  | FALSE   | TRUE  | TRUE  
|  -1    | TRUE  | FALSE | FALSE | FALSE | TRUE  | FALSE | FALSE | TRUE  | FALSE | FALSE   | FALSE | FALSE 
|  "1"   | TRUE  | FALSE | TRUE  | FALSE | FALSE | TRUE  | FALSE | FALSE | FALSE | FALSE   | FALSE | FALSE 
|  "0"   | FALSE | TRUE  | FALSE | TRUE  | FALSE | FALSE | TRUE  | FALSE | FALSE | FALSE   | FALSE | FALSE 
| "-1"   | TRUE  | FALSE | FALSE | FALSE | TRUE  | FALSE | FALSE | TRUE  | FALSE | FALSE   | FALSE | FALSE 
| NULL   | FALSE | TRUE  | FALSE | TRUE  | FALSE | FALSE | FALSE | FALSE | TRUE  | TRUE    | FALSE | TRUE 
| array()| FALSE | TRUE  | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | TRUE  | TRUE    | FALSE | FALSE 
| "php"  | TRUE  | FALSE | FALSE | TRUE  | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE   | TRUE  | FALSE 
|   ""   | FALSE | TRUE  | FALSE | TRUE  | FALSE | FALSE | FALSE | FALSE | TRUE  | FALSE   | FALSE | TRUE 

## 用 === 嚴格的比較 

|        | TRUE  | FALSE |   1   |   0   |  -1   |  "1"  |  "0"  | "-1"  | NULL  | array() | "php" | ""   
| -----: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :-----: | :---: | :---:
| TRUE   | TRUE  | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE   | FALSE | FALSE 
| FALSE  | FALSE | TRUE  | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE   | FALSE | FALSE  
|   1    | FALSE | FALSE | TRUE  | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE   | FALSE | FALSE 
|   0    | FALSE | FALSE | FALSE | TRUE  | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE   | FALSE | FALSE
|  -1    | FALSE | FALSE | FALSE | FALSE | TRUE  | FALSE | FALSE | FALSE | FALSE | FALSE   | FALSE | FALSE
|  "1"   | FALSE | FALSE | FALSE | FALSE | FALSE | TRUE  | FALSE | FALSE | FALSE | FALSE   | FALSE | FALSE
|  "0"   | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | TRUE  | FALSE | FALSE | FALSE   | FALSE | FALSE
| "-1"   | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | TRUE  | FALSE | FALSE   | FALSE | FALSE 
| NULL   | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | TRUE  | FALSE   | FALSE | FALSE 
| array()| FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | TRUE    | FALSE | FALSE
| "php"  | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE   | TRUE  | FALSE
|   ""   | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE   | FALSE | TRUE

