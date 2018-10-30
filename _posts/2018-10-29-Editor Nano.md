---
layout: post
title: Editor Nano
date: 2018-10-29 20:35:28 
categories: Linux
tags: 
 - Linux
 - Editor
---
## install
CentOS: yum -y install nano  
Debian：apt-get install -y nano  

## line edit
Alt+6 : copy one line(复制一整行)  
Ctrl+K: cut one line(剪贴一整行)  
Ctrl+U: paste (粘贴)  
Ctrl+6: 标记，然后移动光标到 待复制／剪贴的文本末尾.若要取消，只需要再按一次Ctrl+6.  

## save
Ctrl+O: save (来保存所做的修改)  

## quit (退出)
Ctrl+X: quit  

## page up/down翻页
Ctrl+Y: page up(到上一页)  
Ctrl+V: page down(到下一页)  

## search(搜索)
Ctrl+W: search 搜索，回车确定, 将会定位到第一个匹配的文本  
Alt+W:  search next (定位到下一个匹配的文本)  

## help (帮助)
进入nano界面后，下面有两行菜单，例如，“^G Get Help”。其意义如下：
^G意味着快捷键是Ctrl+G，“Get Help”当然是功能了。
根据这些提示就可以立刻开始使用nano了，也可以Ctrl+G看看帮助。
黑底白字表示快捷键操作。其中“^”表示Ctrl键，则Ctrl+G就表示成“^G”。“M”表示 Alt键，则Alt+W表示为“M-W”。