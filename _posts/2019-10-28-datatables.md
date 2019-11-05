--- 
layout: post    
title:  "[Javascript] Jquery - Datatables"    
preview: "[Javascript] Jquery - Datatables"    
permalink: "/articles/2019-10-28-Datatables"  
date:   2019-10-28 18:00:00 +08:00    
tags:    
    - "javascript"    
comments: true    
---  

Datatables 用法與詳解
<!-- more -->  

# Datatables

重點摘要:    
* abstact    
{:toc}    
----

```html
<!-- DataTables -->
<!-- MDBootstrap Datatables  -->
<link href="css/datatables.min.css" rel="stylesheet">
<!-- MDBootstrap Datatables  -->
<script src="js/datatables.min.js" type="text/javascript"></script>

<script>
    $('#ID').DataTable({
        "language": {  
            "processing": "處理中...",  
            "loadingRecords": "載入中...",
            "lengthMenu": "顯示 _MENU_ 項結果",
            "zeroRecords": "沒有符合的結果",
            "info": "顯示第 _START_ 至 _END_ 項結果，共 _TOTAL_ 項",
            "infoEmpty": "顯示第 0 至 0 項結果，共 0 項",
            "infoFiltered": "(從 _MAX_ 項結果中過濾)",
            "infoPostFix": "",
            "search": "搜尋:",
            "paginate": {
                "first": "第一頁",
                "previous": "上一頁",
                "next": "下一頁",
                "last": "最後一頁"
            },
            "aria": {
                "sortAscending": ": 升冪排列",
                "sortDescending": ": 降冪排列"
            }
        }
    });
</script>
```
