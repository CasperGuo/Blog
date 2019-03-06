---
layout: post
title: Date Time Format
date: 2019-03-06 16:23:54
categories: SQL Server
tags:
 - SQL Server
 - Script
---
# 常用的日期时间格式 #
格式 说明      输出格式  
d 精简日期格式 MM/dd/yyyy  
D 详细日期格式 dddd, MMMM dd, yyyy  
f  完整格式    (long date + short time) dddd, MMMM dd, yyyy HH:mm  
F 完整日期时间格式 (long date + long time) dddd, MMMM dd, yyyy HH:mm:ss  
g 一般格式 (short date + short time) MM/dd/yyyy HH:mm  
G 一般格式 (short date + long time) MM/dd/yyyy HH:mm:ss  
m,M 月日格式 MMMM dd  
s 适中日期时间格式 yyyy-MM-dd HH:mm:ss  
t 精简时间格式 HH:mm  
T 详细时间格式 HH:mm:ss  

DateTime dt = DateTime.Now; //获得当前系统时间  
Console.WriteLine(string.Format("{0:yyyyMMddHHmmssffff}", dt));    //200912281029182047