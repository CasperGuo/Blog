---
layout: post
title: Winusb for STM32
date: 2021-03-09 16:02:00
categories: USB
tags:
 - USB
 - Winusb
 - STM32
---


** 0x00 关于如何对WINUSB进行DEBUG **
请注意，Windows仅查询OS字符串描述符一次，在开发过程中会造成麻烦。OS描述符存储在注册表中
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\UsbFlags\VVVVPPPPRRRR
(VVVV - VID; PPPP - PID; RRRR - 版本号)
请删除你USB设备对应的注册表项，然后使用USBDeview卸载设备，以便始终获得新的设备插入行为。



