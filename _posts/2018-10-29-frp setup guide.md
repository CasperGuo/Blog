---
layout: post
title: "frp Set up -- Simon"
date:   2018-10-29 10:57:36
categories: Linux FRP
tags: 
 - Linux
 - FRP
---

# 准备工作

1. frp"官网"

> [https://github.com/fatedier/frp](https://github.com/fatedier/)

> [https://github.com/fatedier/frp/releases](https://github.com/fatedier/frp/releases)

> [https://github.com/fatedier/frp/blob/master/README\_zh.md](https://github.com/fatedier/frp/blob/master/README_zh.md)

2. putty或者其他ssh工具(windows10系统中可以安装ssh)   
3. WinSCP  
4. Linux 查找命令find   
find是最常见和最强大的查找命令，你可以用它找到任何你想找的文件。find的使用实例：  
$ find . -name 'frps.ini' $ find . -name 'comfig.xml'  
搜索当前目录（含子目录，以下同）中，所有"文件名"文件，支持通配符   
5. vps（vultra.com）  
6. ufw  
a. sudo ufw status 检查防火墙的状态  
如果你是root，则去掉sudo，ufw status inactive(默认为不活动)  
b. **开启/禁用** sudo ufw allow|deny [service]  
sudo ufw allow smtp　允许所有的外部IP访问本机的25/tcp (smtp)端口  
sudo ufw allow 22/tcp 允许所有的外部IP访问本机的22/tcp (ssh)端口  
7. 如果没有请，安装nc命令  
查看当前启动的端口 netstat -luntp   
打开端口命令 nc -lp 23 & (打开[23端口，即telnet)  
nc -lp 7777 & nc -lp 7500 &  
netstat -an | grep 23 (查看是否打开[23端口]) 

# 开始  
*脚本安装会与Shadowsocks的脚本安装冲突，先装Shadowsocks后装frps会导致Shadowsocks用不了，原因不明。*  

## FRP服务器脚本安装  

a. download script.  
```bash
wget --no-check-certificate https://raw.githubusercontent.com/clangcn/onekey-install-shell/master/frps/install-frps.sh -O ./install-frps.sh
```
b. set script file mode.  
```bash
chmod 700 ./install-frps.sh
```
c. run srcipt to install
```bash
./install-frps.sh install
```

默认安装在：   
```
./usr/local/frps/frps.ini
```
使用脚本安装后可以按需重新配置frps.ini文件，这样做的好处是省去自己配置服务启动等麻烦。

[http://ip:6443](http://ip:6443)  
可以访问frps的web状态页面。//密码需要输入两三次，服务器密码和frps密码

## FRP服务器手动安装  

### 1. 下载frps上传到linux服务器  
设置目录是可执行的，文件是可执行的，winSCP可以设置，添加目录到系统PATH，按你自己的实际情况设置。 
```bash
export PATH=/sbin:/usr/sbin:/usr/local/sbin:/etc/frp:$PATH  
export PATH=/sbin:/usr/sbin:/usr/local/sbin:/usr/local/frp:$PATH  
```
shell中运行如下命令显示设置后的目录。   
```bash
echo $PATH 
```
或者是 
```bash
$PATH
```

### 2. 设置可执行
```bash
sudo chmod u+x /etc/frp   
sudo chmod u+x /etc/frp/frps  
```
### 3.运行客户端  
带参数运行frps，
```bash
./frps -c ./frps.ini
```
如果目录设置好，可以使用:  
```bash
frps -c frps.ini
```

运行后，可以使用网页访问http://ip:xxxx（xxxx是你配置的端口号），访问测试。

使用"nohup"， 即 "nohup ./frps -c ./frps.ini &"，忽略hangup信号，防止shell关闭时程序停掉。
```bash
nohup ./frps -c ./frps.ini &
/etc/frp/frps -c ./frps.ini &
```
_privilege_allow_ports__ 可以配置允许使用的某个指定端口或者是一个范围内的所有端口，以 , 分隔，指定的范围以 - 分隔。_

当使用不允许的端口注册时，就会注册失败。出现类似以下错误：  
port not allowed  
windows10下运行ssh的方法:    
ssh domain\[user@host]

## 客户端安装
和服务器类似运行frpc   
```bash
nohup ./frpc -c ./frpc.ini &
```
Linux下SVN服务启动
```bash
svnserve -d -r /shares/Public/mycode
```
如果需要增加端口参考如下方式：  
需要更改防火墙规则，开放刚刚新增的端口。centOS 6 系统开放办法如下。保存退出后，输入以下命令，注意一次输入一行，并把 \&lt;newport\&gt; 替换为刚添加的端口：
```bash
iptables -I INPUT -m state --state NEW -m tcp -p tcp --dport \&lt;newport\&gt; -j ACCEPT iptables -I INPUT -m state --state NEW -m udp -p udp --dport \&lt;newport\&gt; -j ACCEPT /etc/init.d/iptables save /etc/init.d/iptables restart

iptables -I INPUT -m state --state NEW -m tcp -p tcp --dport 1688 -j ACCEPT

iptables -I INPUT -m state --state NEW -m udp -p udp --dport 1688 -j ACCEPT

sudo nano /etc/systemd/system/frpc.service
```
如果需要设置自启动参考如下配置文件：   
```bash
/////////////////////////////////////

[Unit]

Description=FRP Client Daemon

After=network.target

Wants=network.target

[Service]

Type=simple

ExecStart=/etc/frp/frpc -c /etc/frp/frpc.ini

Restart=always

RestartSec=20s

User=nobody

[Install]

WantedBy=multi-user.target
```

```bash
ln **-**** s** /lib/systemd/system/rc.local.service /etc/systemd/system/
```

使用sudo systemctl enable frpc.service启用

## Config
```bash
systemctl enable frpc systemctl status frpc systemctl enable frps systemctl status frps

sudo systemctl enable /etc/systemd/system/frpc.service

sudo systemctl enable frps.service#允许开机自启

sudo systemctl start frps.service#启动

sudo systemctl status frps.service#查看运行状态
```

# frp使用案例：
设置好frp后，使用ssh远程唤醒某个主机
```bash
ssh -lroot host -pxxxx "./wake.sh"
```
-l后是登录的用户名  
host为主机，ip或者domain  
-p后是端口号  
"./wake.sh" 是要执行的脚本  