---
layout: post
title: SQL Server SA by CMD
date: 2019-01-15 09:45:45
categories: SQL Server
tags:
 - SQL Server
 - Script
---
# 命令行方式修改sql server sa 的密码 #

在打开具有管理员权限的cmd窗口

``` shell
C:\Documents and Settings\Administrator>osql -E
1> sp_password null,'abc123','sa'
2> go
```

Password changed.  

``` shell
1> exit
```

大功告成，sa的密码修改成了abc123


关键是osql这个东东，具体查看 http://msdn.microsoft.com/zh-cn/vstudio/ms162806.aspx