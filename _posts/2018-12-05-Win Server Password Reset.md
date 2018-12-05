---
layout: post
title: reset windows server 2016 password
date: 2018-12-05 14:15:11
categories: Windows Administrator
tags:
 - Windows
 - Administrator
---

- Insert the Windows Server 2016 DVD
- On the Windows Setup page, click Next.
- On the Windows Setup page, select “Repair your computer”.
- On the Choose an Option page, click “Troubleshoot” option.
- On the Advanced options page, select “Command Prompt”
```bat
 CD c:\windows\System32
 ren utilman.exe utilman_bk.exe
 ```
- Restart windows.
