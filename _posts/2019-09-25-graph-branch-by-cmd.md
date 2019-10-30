--- 
layout: post    
title:  "[Git]美美的分支"    
preview: "[Git]美美的分支"    
permalink: "/articles/2019-09-25-graph-branch-by-cmd"  
date:   2019-09-25 16:00:00 +08:00    
tags:    
    - "git"    
comments: true    
---  


  
試著用Command Line，畫出超美的分支圖吧！   
<!-- more -->   
# 分支圖畫法

 - 平常我在用command畫出很美的分支圖，通常會用 --graph --oneline

```bash
$git log --graph --oneline
```
![normal mode](https://i.imgur.com/pfS0z1Q.png)

 - 經過排版後，可以挑選下面兩種，稍微研究後可以得出更多變化喔！
 
## 排版後第一種

```bash
$git log --graph --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --all
```
![Beauty mode 1](https://imgur.com/XBkZGOs.png)
## 排版後第二種
```bash
$ git log --graph --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold cyan)%aD%C(reset) %C(bold green)(%ar)%C(reset)%C(bold yellow)%d%C(reset)%n''%C(white)%s%C(reset) %C(dim white)- %an%C(reset)' --all
```
![Beauty mode 2](https://imgur.com/daua0Aj.png)
