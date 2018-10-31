---
layout: post
title: SVN自动备份说明
date: 2018-10-31 08:11:26
categories: Program SCM
tags:
 - Program
 - SVN
 - SCM
---
> [http://blog.51cto.com/stephenzhao/823072](http://blog.51cto.com/stephenzhao/823072)

版本控制最关键的一件事是保证数据的安全性，不能因为磁盘损坏，程序故障造成版本库无可挽回的错误，为此必须制定较完备的备份策略。在Subversion中，我们有三种备份方式：完全备份，增量备份和同步版本库。


## 1, 完全备份

最常见和简单的备份就是直接使用拷贝命令，将版本库目录拷贝到备份目录上，就可以了。但是这样不是很安全的方式， 因为如果在拷贝时版本库发生变化，将会造成备份的结果不够准确，失去备份的作用，为此Subversion提供了“svnadmin hotcopy”命令，可以防止这种问题。

还记得我们的版本库目录吗？
```
D:\SVNROOT
├─project1
│  ├─conf
│  ├─dav
│  ├─db
│  │  ├─revprops
│  │  ├─revs
│  │  └─transactions
│  ├─hooks
│  └─locks
└─project2
    ├─conf
    ├─dav
    ├─db
    │  ├─revprops
    │  ├─revs
    │  └─transactions
    ├─hooks
    └─locks
```    

如果要把project1备份到d:\svnrootbak目录下，只需要运行：
```bash
svnadmin hotcopy d:\svnroot\project1 d:\svnrootbak\project1
```
但是我们作为配置管理员，必须想办法优化这个过程，如果我们这个目录下有许多版本库，需要为每个版本库写这样一条语句备份，为此我写了下面的脚本，实现备份一个目录下的所有版本库。我们在D:\SVNROOT下创建了两个文件，simpleBackup.bat：

```
@echo 正在备份版本库%1......
@%SVN_HOME%\bin\svnadmin hotcopy %1 %BACKUP_DIRECTORY%\%2
@echo 版本库%1成功备份到了%2！
```

这个文件仅仅是对“svnadmin hotcopy”的包装，然后是backup.bat：

```bash
echo off

rem Subversion的安装目录
set SVN_HOME="D:\Subversion"

rem 所有版本库的父目录
set SVN_ROOT=D:\svnroot

rem 备份的目录
set BACKUP_SVN_ROOT=D:\svnrootbak

set BACKUP_DIRECTORY=%BACKUP_SVN_ROOT%\%date:~0,10%
if exist %BACKUP_DIRECTORY% goto checkBack
echo 建立备份目录%BACKUP_DIRECTORY%>>%SVN_ROOT%/backup.log

mkdir %BACKUP_DIRECTORY%

rem 验证目录是否为版本库，如果是则取出名称备份
for /r %SVN_ROOT% %%I in (.) do @if exist "%%I\conf\svnserve.conf" %SVN_ROOT%\simpleBackup.bat "%%~fI" %%~nI
goto end

:checkBack
echo 备份目录%BACKUP_DIRECTORY%已经存在，请清空。
goto end

:end
```
你 在使用的时候，只需要修改backup.bat开头的三个路径，将两个脚本拷贝到“SVN_ROOT”下就可以了。根据以上的配置，你只需要运行 backup.bat，就可以把“SVN_ROOT”下的版本库都备份到“BACKUP_SVN_ROOT”里，并且存放在备份所在日的目录里，例如 “D:\svnrootbak\2006-10-22”。

虽然这部分工作很简单，可是必须有人定时地去执行这个操作（例如每周一凌晨），为了避免发生遗忘的情况，我们可以将这个操作加入到系统的at任务当中去，例如还是上面的环境，为了安装at任务，我们运行：

at 1:00 /every:M D:\svnroot\backup.bat

这样在每周一凌晨1:00都会执行这个备份过程。当然备份在本机也是不安全的，你也许需要上传到别的机器，这个就要靠你自己去实现了。


## 2, 增量备份

尽管完全备份非常简单，但是也是有代价的，当版本库非常巨大时，经常进行完全备份是不现实的，也并不必要，但是一旦版本库在备份之间发生问题，该如何呢，这里我们就用到了增量备份。

增量备份通常要与完全备份结合使用，就像oracle数据库的归档日志，记录着每次Subversion提交的变化，然后在需要恢复时能够回到最新的可用状态。在我们这个例子中我们使用的是，svnadmin dump命令进行增量的备份，使用方法是：
```bash
svnadmin dump project1 --revision 15 --incremental > dumpfile2
```
上面的命令实现了对修订版本15进行增量的备份，其中的输出文件dumpfile2只保存了修订版本15更改的内容。

为了记录每次提交的结果，我们需要使用一项Subversion的特性--钩子（hook），看看我们的project1目录：
```bash
├─project1
│  ├─conf
│  ├─dav
│  ├─db
│  │  ├─revprops
│  │  ├─revs
│  │  └─transactions
│  ├─hooks
│  └─locks
```
其中的hooks目录里存放的就是钩子脚本，我们在此处只使用post-commit钩子，这个钩子会在每次提交之后执行，为了实现我们的备份功能，我们在hooks下建立一个文件post-commit.bat，内容如下：
```bash
echo off
set SVN_HOME="C:\Program Files\Subversion"
set SVN_ROOT=D:\svnroot
set UNIX_SVN_ROOT=D:/svnroot
set DELTA_BACKUP_SVN_ROOT=D:\svnrootbak\delta

set LOG_FILE=%1\backup.log
echo backup revision %2 >> %LOG_FILE%

for /r %SVN_ROOT% %%I in (.) do if D:/svnroot/%%~nI == %1 %SVN_ROOT%\%%~nI\hooks\deltaBackup.bat %%~nI %2
goto end

:end
```
通过这个脚本，可以实现D:\svnroot下的版本库提交时自动增量备份到D:\svnrootbak\delta（确定这个目录存在），其中使用的deltaBackup.bat其实可以放在任何地方，只是对脚本的svnadmin dump的包装，内容如下：

@echo 正在备份版本库%2......
%SVN_HOME%\bin\svnadmin dump %SVN_ROOT%\%1 --incremental --revision %2 >> %DELTA_BACKUP_SVN_ROOT%\%1.dump
@echo 版本库%2成功备份到了%3！

以上两个脚本可以直接拷贝到project2的hooks目录下，不需要修改就可以实现project2的自动备份。

以上的操作已经OK了，现在需要做的是将完全备份和增量备份结合起来，也就是在完全备份后清理增量备份的结果，使之只保存完全备份后的结果。

当果真出现版本库的故障，就要求我们实现版本库的恢复操作了，这是用要使用svnadmin load命令，同时也需要上次的完全备份例如要把上次完全备份backuprepo，和之后的增量备份dumpfile：

svnadmin load backuprepo < dumpfile

最后的结果，可以下载svnroot.rar，将之解压缩到d:\下，然后修改几个bat文件的SVN_HOME就可以使用了。

## 3, 版本库同步

Subversion 1.4增加了同步机制，可以实现一个版本库同另一个版本库的同步（但好像只是单向的），我们可以用来实现版本库的备份或镜像。

### 3.1. 对目标库初始化
```bash
svnsync init svn://localhost/project2 svn://localhost/project1
 ```

其中project2是目标的版本库，而project1是源版本库。其中的目标版本库必须为空，而且必须允许修订版本属性的修改，也就是在目标的版本库的hooks目录里添加一个文件pre-revprop-change.bat，内容为空即可。

### 3.2. 同步project2到project1
```bash
svnsync sync svn://localhost/project2
 ```

这 时候你update一下你的project2的一个工作拷贝，就会发现有了project1的所有内容。如果project1又有提交，这时候 project2的版本库无法看到最新的变化，还需要再运行一遍sync操作，这样才能将最新的变化同步。需要注意的是，目标版本库只能做成只读的，如果 目标版本库发生了变更，则无法继续同步了。

### 3.3. 同步历史属性的修改

因为同步不会更新对历史属性的修改，所以svnsync还有子命令copy-revprops，可以同步某个版本的属性。

### 3.4. 钩子自动同步

希望在每次提交时同步，则需要在源版本库增加post-commit脚本，内容如下：
```bash
echo off
set SVN_HOME="D:\Subversion"
%SVN_HOME%\bin\svnsync sync  --non-interactive svn://localhost/project2
``` 

把以上内容存放为post-commit.bat，然后放到版本库project1下的hooks目录下，这样project1每次提交，都会引起project2的同步。

附件：http://down.51cto.com/data/2360197