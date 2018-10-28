---
layout: post
title: "Linux Shell Command"
categories: Linux
tags: 
  - Linux
  - Shell
---
# Linux Shell Command
[https://ss64.com/bash/](https://ss64.com/bash/)
sudo - super user do
su - switch user (default to root)
nano - Nano's ANOther editor
## 我的常用指令
mkdir
find . -name 'frps.ini'    $ find . -name ' comfig.xml' 
rm 
打开端口命令 nc -lp 23 & (打开23端口，即telnet)
shutdown -r now
ps 显示瞬间进程状态 ps -aux
uname 显示系统信息
kill 杀死进程
ifconfig  查看网络情况,可以查看ip
chmod 

## 常用指令
ls　　        显示文件或目录
- -l           列出文件详细信息l(list)
- -a          列出当前目录下所有文件及目录，包括隐藏的a(all)

stat              显示指定文件的详细信息，比ls更详细
mkdir         创建目录
- -p           创建目录，若无父目录，则创建p(parent)

cd               切换目录
touch          创建空文件
echo            创建带有内容的文件。
cat              查看文件内容
cp                拷贝
mv               移动或重命名
rm               删除文件
+ -r 递归删除，可删除子目录及文件
+ -f 强制删除

find 在文件系统中搜索某文件
> 使用实例：find . -name 'frps.ini'    $ find . -name ' comfig.xml'  

wc                统计文本中行数、字数、字符数
grep             在文本文件中查找某个字符串
rmdir           删除空目录
tree             树形结构显示目录，需要安装tree包
pwd              显示当前目录
ln                  创建链接文件
more、less  分页显示文本文件内容
head、tail    显示文件头、尾内容
ctrl+alt+F1  命令行全屏模式
clear              清屏
## 查看当前启动的端口 netstat -luntp
打开端口命令 nc -lp 23 &(打开23端口，即telnet)
netstat -an | grep 23 (查看是否打开23端口)

##  关机/重启指令
#### shutdown
 - -r             关机重启
 - -h             关机不重启
 -  now          立刻关机
#### halt               关机
#### reboot          重启

ifconfig  查看网络情况,可以查看ip
kill 杀死进程，可以先用ps 或 top命令查看进程的id，然后再用kill命令杀死进程。
ps 显示瞬间进程状态 ps -aux
uname 显示系统信息
netstat          显示网络状态信息

## 打包压缩相关命令
gzip：
bzip2：
tar:                打包压缩
- -c 归档文件
- -x 压缩文件
- -z gzip压缩文件
- -j bzip2压缩文件
- -v 显示压缩或解压缩过程 v(view)
- -f 使用档名

例：
tar -cvf /home/abc.tar /home/abc              只打包，不压缩
tar -zcvf /home/abc.tar.gz /home/abc        打包，并用gzip压缩
tar -jcvf /home/abc.tar.bz2 /home/abc      打包，并用bzip2压缩
当然，如果想解压缩，就直接替换上面的命令  tar -cvf  / tar -zcvf  / tar -jcvf 中的“c” 换成“x” 就可以了。

## 文件权限管理
#### 三种基本权限
R           读         数值表示为4
W          写         数值表示为2
X           可执行  数值表示为1
如某文件的权限为-rw-rw-r--
-rw-rw-r--一共十个字符，分成四段。

第一个字符“-”表示普通文件；这个位置还可能会出现“l”链接；“d”表示目录
第二三四个字符“rw-”表示当前所属用户的权限。   所以用数值表示为4+2=6
第五六七个字符“rw-”表示当前所属组的权限。      所以用数值表示为4+2=6
第八九十个字符“r--”表示其他用户权限。              所以用数值表示为4
所以操作此文件的权限用数值表示为664 
#### 更改权限
sudo chmod [u所属用户  g所属组  o其他用户  a所有用户]  [+增加权限  -减少权限]  [r  w  x]   目录名 
例如：有一个文件filename，权限为“-rw-r----x” ,将权限值改为"-rwxrw-r-x"，用数值表示为765
sudo chmod u+x g+w o+r  filename
上面的例子可以用数值表示
sudo chmod 765 filename

## 防火墙ufw
    a. sudo ufw status 检查防火墙的状态
        如果你是root，则去掉sudo，ufw status inactive(默认为不活动)
    a. 开启/禁用 sudo ufw allow|deny [service]
        sudo ufw allow smtp　允许所有的外部IP访问本机的25/tcp (smtp)端口
        sudo ufw allow 22/tcp 允许所有的外部IP访问本机的22/tcp (ssh)端口