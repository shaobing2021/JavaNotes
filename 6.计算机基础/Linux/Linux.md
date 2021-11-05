[镜像源](https://mirrors.ustc.edu.cn/repogen/)

```
deb https://mirrors.ustc.edu.cn/ubuntu/ bionic main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-security main restricted universe multiverse

deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse

deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse

## Not recommended
# deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
# deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse


## Note, this file is written by cloud-init on first boot of an instance
## modifications made here will not survive a re-bundle.
## if you wish to make changes you can:
## a.) add 'apt_preserve_sources_list: true' to /etc/cloud/cloud.cfg
##     or do the same in user-data
## b.) add sources in /etc/apt/sources.list.d
## c.) make changes to template file /etc/cloud/templates/sources.list.tmpl

# See http://help.ubuntu.com/community/UpgradeNotes for how to upgrade to
# newer versions of the distribution.
deb http://ports.ubuntu.com/ubuntu-ports bionic main restricted
deb-src http://ports.ubuntu.com/ubuntu-ports bionic main restricted

## Major bug fix updates produced after the final release of the
## distribution.
deb http://ports.ubuntu.com/ubuntu-ports bionic-updates main restricted
deb-src http://ports.ubuntu.com/ubuntu-ports bionic-updates main restricted

## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu
## team. Also, please note that software in universe WILL NOT receive any
## review or updates from the Ubuntu security team.
deb http://ports.ubuntu.com/ubuntu-ports bionic universe
deb-src http://ports.ubuntu.com/ubuntu-ports bionic universe
deb http://ports.ubuntu.com/ubuntu-ports bionic-updates universe
deb-src http://ports.ubuntu.com/ubuntu-ports bionic-updates universe

## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu 
## team, and may not be under a free licence. Please satisfy yourself as to 
## your rights to use the software. Also, please note that software in 
## multiverse WILL NOT receive any review or updates from the Ubuntu
## security team.
deb http://ports.ubuntu.com/ubuntu-ports bionic multiverse
deb-src http://ports.ubuntu.com/ubuntu-ports bionic multiverse
deb http://ports.ubuntu.com/ubuntu-ports bionic-updates multiverse
deb-src http://ports.ubuntu.com/ubuntu-ports bionic-updates multiverse

## N.B. software from this repository may not have been tested as
## extensively as that contained in the main release, although it includes
## newer versions of some applications which may provide useful features.
## Also, please note that software in backports WILL NOT receive any review
## or updates from the Ubuntu security team.
deb http://ports.ubuntu.com/ubuntu-ports bionic-backports main restricted universe multiverse
deb-src http://ports.ubuntu.com/ubuntu-ports bionic-backports main restricted universe multiverse

deb http://ports.ubuntu.com/ubuntu-ports bionic-security main restricted
deb-src http://ports.ubuntu.com/ubuntu-ports bionic-security main restricted
deb http://ports.ubuntu.com/ubuntu-ports bionic-security universe
deb-src http://ports.ubuntu.com/ubuntu-ports bionic-security universe
deb http://ports.ubuntu.com/ubuntu-ports bionic-security multiverse
deb-src http://ports.ubuntu.com/ubuntu-ports bionic-security multiverse

## Uncomment the following two lines to add software from Canonical's
## 'partner' repository.
## This software is not part of Ubuntu, but is offered by Canonical and the
## respective vendors as a service to Ubuntu users.
# deb http://archive.canonical.com/ubuntu bionic partner
# deb-src http://archive.canonical.com/ubuntu bionic partner


deb http://mirrors.huaweicloud.com/ubuntu/ boinc main restricted universe multiverse
deb http://mirrors.huaweicloud.com/ubuntu/ boinc-security main restricted universe multiverse
deb http://mirrors.huaweicloud.com/ubuntu/ boinc-updates main restricted universe multiverse
deb http://mirrors.huaweicloud.com/ubuntu/ boinc-backports main restricted universe multiverse
##測試版源
deb http://mirrors.huaweicloud.com/ubuntu/ boinc-proposed main restricted universe multiverse
# 源碼
deb-src http://mirrors.huaweicloud.com/ubuntu/ boinc main restricted universe multiverse
deb-src http://mirrors.huaweicloud.com/ubuntu/ boinc-security main restricted universe multiverse
deb-src http://mirrors.huaweicloud.com/ubuntu/ boinc-updates main restricted universe multiverse
deb-src http://mirrors.huaweicloud.com/ubuntu/ boinc-backports main restricted universe multiverse
##測試版源
deb-src http://mirrors.huaweicloud.com/ubuntu/ boinc-proposed main restricted universe multiverse
# Canonical 合作夥伴和附加
deb http://archive.canonical.com/ubuntu/ boinc partner
deb http://extras.ubuntu.com/ubuntu/ boinc main

http://cn.archive.ubuntu.com/ubuntu/

deb http://cn.archive.ubuntu.com/ubuntu/ boinc main restricted universe multiverse
deb http://cn.archive.ubuntu.com/ubuntu/ boinc-security main restricted universe multiverse
deb http://cn.archive.ubuntu.com/ubuntu/ boinc-updates main restricted universe multiverse
deb http://cn.archive.ubuntu.com/ubuntu/ boinc-backports main restricted universe multiverse
```

### 1.前期准备

[Linux博客](https://blog.csdn.net/qq_33369905/article/details/106647311)

[Linux笔记](https://mp.weixin.qq.com/mp/homepage?__biz=Mzg2NTAzMTExNg==&hid=2&sn=1650b6338f6469ca519b080fdbbbd333&scene=18&devicetype=android-28&version=27000f51&lang=zh_CN&nettype=WIFI&ascene=7&session_us=gh_1dd456f1d44d&pass_ticket=UwjqeyOUQFFmAvZ098nAb9yIxRtcPPXdMIBp2Vx03t3FZtRzkHjEGpSYoeWBt%2Fyx&wx_header=1)

#### 1.1 云服务器搭建

#### 1.2 xshell和xftp安装

链接：https://pan.baidu.com/s/1zwjhmDJenS15D_HeA-SU6g
提取码：3xbn

#### 1.3 重要信息记得删除

远程服务器地址：124.70.176.228

密码：sb403001592

hadoop用户：hadoop

密码：hadoop

### 2.理论

#### 2.1 关机

```xml
sync # 将数据由内存同步到硬盘中。
shutdown # 关机指令，你可以man shutdown 来看一下帮助文档。例如你可以运行如下命令关机：
shutdown –h 10 # 这个命令告诉大家，计算机将在10分钟后关机
shutdown –h now # 立马关机
shutdown –h 20:25 # 系统会在今天20:25关机
shutdown –h +10 # 十分钟后关机
shutdown –r now # 系统立马重启
shutdown –r +10 # 系统十分钟后重启
reboot # 就是重启，等同于 shutdown –r now
halt # 关闭系统，等同于shutdown –h now 和 poweroff
```

#### 2.2 Linux目录

![image-20200627175716249](https://gitee.com/shaobing2021/typora/raw/master/img/20200728100306.png)

- ***/bin***：bin是Binary的缩写, 这个目录存放着最经常使用的命令。
- ***/boot：*** 这里存放的是启动Linux时使用的一些核心文件，包括一些连接文件以及镜像文件。
- ***/dev ：*** dev是Device(设备)的缩写, 存放的是Linux的外部设备，在Linux中访问设备的方式和访问文件的方式是相同的。
- ==***/etc：***== 这个目录用来存放所有的系统管理所需要的配置文件和子目录。
- ==***/home***：==用户的主目录，在Linux中，每个用户都有一个自己的目录，一般该目录名是以用户的账号命名的。
- ***/lib***：这个目录里存放着系统最基本的动态连接共享库，其作用类似于Windows里的DLL文件。
- ***/lost+found***：这个目录一般情况下是空的，当系统非法关机后，这里就存放了一些文件。
- ***/media***：linux系统会自动识别一些设备，例如U盘、光驱等等，当识别后，linux会把识别的设备挂载到这个目录下。
- ***/mnt***：系统提供该目录是为了让用户临时挂载别的文件系统的，我们可以将光驱挂载在/mnt/上，然后进入该目录就可以查看光驱里的内容了。
- ==***/opt***：==这是给主机额外安装软件所摆放的目录。比如你安装一个ORACLE数据库则就可以放到这个目录下。默认是空的。
- **/proc***：这个目录是一个虚拟的目录，它是系统内存的映射，我们可以通过直接访问这个目录来获取系统信息。
- ==***/root***：==该目录为系统管理员，也称作超级权限者的用户主目录。
- ***/sbin***：s就是Super User的意思，这里存放的是系统管理员使用的系统管理程序。
- ***/srv***：该目录存放一些服务启动之后需要提取的数据。
- ***/sys***：这是linux2.6内核的一个很大的变化。该目录下安装了2.6内核中新出现的一个文件系统 sysfs 。
- ==**/tmp**：==这个目录是用来存放一些临时文件的。
- ==***/usr***：==这是一个非常重要的目录，用户的很多应用程序和文件都放在这个目录下，类似于windows下的program files目录。
- ***/usr/bin：*** 系统用户使用的应用程序。
- ***/usr/sbin：*** 超级用户使用的比较高级的管理程序和系统守护程序。
- ***/usr/src：*** 内核源代码默认的放置目录。
- ***/var***：这个目录中存放着在不断扩充着的东西，我们习惯将那些经常被修改的目录放在这个目录下。包括各种日志文件。
- ***/run***：是一个临时文件系统，存储系统启动以来的信息。当系统重启时，这个目录下的文件应该被删掉或清除。

* **/www:** 存放服务器网站相关资源，环境，网站项目

#### 2.3绝对相对路径

> **绝对路径**

路径的写法，由根目录 / 写起，例如：/usr/share/doc 这个目录。

> **相对路径：**

由 /usr/share/doc 要到 /usr/share/man 底下时，可以写成：cd ../man 这就是相对路径的写法啦！

#### 2.4 系统相关

```
#查看系统版本
cat /etc/rehad-release
```

### 3.处理目录常用命令

```cmd
clear #清屏幕
ctrl+l #清屏幕
tab #补全
```

#### 3.1 查（ls）

> ls （列出目录）

```cmd
- -a ：全部的文件，连同隐藏文件( 开头为 . 的文件) 一起列出来(常用)
- -l ：长数据串列出，包含文件的属性与权限等等数据；(常用)
* -al: 整合使用
```

#### 3.2 进（cd）

> cd （切换目录）Change Directory

```cmd
#进入root目录
cd 
# 切换到用户目录下
[root /]# cd home  
#返回上一级目录
[root ]# cd ..
#通过相对路径进入某个文件夹
[root ]#cd ../../xx or xx/xx
# 回到根目录
[root ]# cd /
#通过绝对路径进入某个文件夹
[root ]# cd /home
# 表示回到自己的家目录，亦即是 /root 这个目录
[root]# cd ~
```

#### 3.3 查（pwd）

> pwd ( 显示目前所在的目录 ) **Print Working Directory** 

```cmd
pwd
# 如果是链接，要显示真实地址，可以使用 -P参数
pwd -P
```

#### 3.4 增（mkdir）

> mkdir 创建文件夹

```cmd
#创建文件夹
mkdir 文件夹
#递归创建文件夹  在文件夹1不存在的情况下，若存在文件夹1直接mkdir 即可
mkdir -p 文件夹1/文件夹2
```

#### 3.5 删（rmdir）

> rmdir 文件夹

```cmd
#注意点：删除文件夹必须在上一级目录下删除，否则报错
#删除文件夹
rmdir 文件夹
#删除空文件夹2，不能删除文件夹1
rmdir 文件夹1/文件夹2
#递归删除文件夹1以及底下的文件夹2
rmdir -p 文件夹1/文件夹2
```

#### 3.6 移（cp）

> cp （复制文件）

```cmd
cp 文件 文件夹  #可以输入y覆盖	
cp -rf 递归移动
```

#### 3.7 删（rm）

> rm（移除文件或者目录）

```cmd
rm -f #忽略不存在的文件，不会出现警告，强制删除
rm -r #递归删除目录，会递归询问
rm -i #互动，删除询问是否删除
rm -rf #删库跑路
```

#### 3.8 移（mv）

> mv 移动文件或者目录

```cmd
mv 文件 文件夹 #移动文件
mv -f #强制移动文件或者目录
mv -u #只替换已经更新过的文件
mv #文件重命名
```

### 4.文件常用命令

#### 基本属性

```cmd
ls -l #查看文件属性和权限
total 68
lrwxrwxrwx.  1 root root     7 Apr 27 21:52 bin -> usr/bin
dr-xr-xr-x.  5 root root  4096 Jun 10 09:46 boot
drwxr-xr-x   7 root root  4096 Apr 27 22:28 CloudResetPwdUpdateAgent
drwxr-xr-x   7 root root  4096 Apr 27 22:28 CloudrResetPwdAgent
drwxr-xr-x  19 root root  3040 Jun 10 10:55 dev
drwxr-xr-x. 76 root root  4096 Jun 10 10:55 etc
drwxr-xr-x.  3 root root  4096 Jun 28 00:36 home
lrwxrwxrwx.  1 root root     7 Apr 27 21:52 lib -> usr/lib
lrwxrwxrwx.  1 root root     9 Apr 27 21:52 lib64 -> usr/lib64
drwx------.  2 root root 16384 Apr 27 21:50 lost+found
drwxr-xr-x.  2 root root  4096 Apr 11  2018 media
drwxr-xr-x.  2 root root  4096 Apr 11  2018 mnt
drwxr-xr-x.  2 root root  4096 Apr 11  2018 opt
dr-xr-xr-x  85 root root     0 Jun 10 10:55 proc
dr-xr-x---.  4 root root  4096 Jun 10 09:46 root
drwxr-xr-x  24 root root   640 Jun 27 18:07 run
lrwxrwxrwx.  1 root root     8 Apr 27 21:52 sbin -> usr/sbin
drwxr-xr-x.  2 root root  4096 Apr 11  2018 srv
dr-xr-xr-x  13 root root     0 Jun 27 18:47 sys
drwxrwxrwt.  9 root root  4096 Jun 27 03:48 tmp
drwxr-xr-x. 13 root root  4096 Apr 27 21:52 usr
drwxr-xr-x. 19 root root  4096 Apr 27 22:11 var
```

在Linux中`第一个字符`代表这个文件是目录、文件或链接文件等等：

- 当为[ **d** ]则是目录
- 当为[ **-** ]则是文件；
- 若是[ **l** ]则表示为链接文档 ( link file )；链接到某个目录
- 若是[ **b** ]则表示为装置文件里面的可供储存的接口设备 ( 可随机存取装置 )；
- 若是[ **c** ]则表示为装置文件里面的串行端口设备，例如键盘、鼠标 ( 一次性读取装置 )。

接下来字符以三个为一组，且均为『rwx』 的三个参数的组合。

* [ r ]代表可读(read)
* [ w ]代表可写(write)
* [ x ]代表可执行(execute)
* [-]代表不可执行
* 9个字符分为三组，分别代表【root用户】、【属组权限】、【其他用户权限】

#### 链接

> 硬链接

A是B的硬链接（A和B都是文件名），则A的目录项中的inode节点号与B的目录项中的inode节点号相同，即一个inode节点对应两个不同的文件名，两个文件名指向同一个文件，A和B对文件系统来说是完全平等的。如果删除了其中一个，对另外一个没有影响。每增加一个文件名，inode节点上的链接数增加一，每删除一个对应的文件名，inode节点上的链接数减一，直到为0，inode节点和对应的数据块被回收。注：文件和文件名是不同的东西，rm A删除的只是A这个文件名，而A对应的数据块（文件）只有在inode节点链接数减少为0的时候才会被系统回收。

> 软链接

类似快捷方式，A是B的软链接（A和B都是文件名），A的目录项中的inode节点号与B的目录项中的inode节点号不相同，A和B指向的是两个不同的inode，继而指向两块不同的数据块。但是A的数据块中存放的只是B的路径名（可以根据这个找到B的目录项）。A和B之间是“主从”关系，如果B被删除了，A仍然存在（因为两个是不同的文件），但指向的是一个无效的链接。

==touch==创建文件

==echo==输入文件

```cmd
[root@hecs-x-medium-2-linux-20200610094343 home]# touch f1
#创建硬链接
[root@hecs-x-medium-2-linux-20200610094343 home]# ln f1 f2
#创建软链接
[root@hecs-x-medium-2-linux-20200610094343 home]# ln -s f1 f3
[root@hecs-x-medium-2-linux-20200610094343 home]# ls
f1  f2  f3  shaobing
[root@hecs-x-medium-2-linux-20200610094343 home]# ll
total 4
-rw-r--r-- 2 root root    0 Jun 28 09:21 f1
-rw-r--r-- 2 root root    0 Jun 28 09:21 f2
lrwxrwxrwx 1 root root    2 Jun 28 09:22 f3 -> f1
drwxrwxrwx 3 root root 4096 Jun 28 00:36 shaobing
#测试删除效果
[root@hecs-x-medium-2-linux-20200610094343 home]# rm f1
rm: remove regular file ‘f1’? y
#f2仍然存在
[root@hecs-x-medium-2-linux-20200610094343 home]# cat f2
i love u
[root@hecs-x-medium-2-linux-20200610094343 home]# cat f3
cat: f3: No such file or directory
#f3变红，不存在
[root@hecs-x-medium-2-linux-20200610094343 home]# ls
f2  f3  shaobing

```

**1、chgrp：更改文件属组**

```
chgrp [-R] 属组名 文件名
```

-R：递归更改文件属组，就是在更改某个目录文件的属组时，如果加上-R的参数，那么该目录下的所有文件的属组都会更改。

**2、chown：更改文件属主，也可以同时更改文件属组**

```
chown [–R] 属主名 文件名
chown [-R] 属主名：属组名 文件名
```

==**3、chmod：更改文件9个属性**==

```
chmod [-R] xyz 文件或目录
```

Linux文件属性有两种设置方法，一种是数字，一种是符号。

Linux文件的基本权限就有九个，分别是owner/group/others三种身份各有自己的read/write/execute权限。

先复习一下刚刚上面提到的数据：文件的权限字符为：『-rwxrwxrwx』， 这九个权限是三个三个一组的！其中，我们可以使用数字来代表各个权限，各权限的分数对照表如下：

``` cmd
r:4     w:2         x:1
可读可写不可执行  rw-  6
可读可写可执行    rwx  7
```

每种身份(owner/group/others)各自的三个权限(r/w/x)分数是需要累加的，例如当权限为：[-rwxrwx---] 分数则是：

- owner = rwx = 4+2+1 = 7
- group = rwx = 4+2+1 = 7
- others= --- = 0+0+0 = 0

```
chmod 770 filename
```

#### 内容查看

```cmd
#查看网卡配置
[root@hecs-x-medium-2-linux-20200610094343 home]# cd /etc/sysconfig/network-scripts/
#配置文件信息
[root@hecs-x-medium-2-linux-20200610094343 network-scripts]# ls
ifcfg-eth0   ifdown-ipv6      ifup-aliases  ifup-routes
ifcfg-eth1   ifdown-isdn      ifup-bnep     ifup-sit
ifcfg-eth2   ifdown-post      ifup-eth      ifup-Team
ifcfg-eth3   ifdown-ppp       ifup-ippp     ifup-TeamPort
ifcfg-eth4   ifdown-routes    ifup-ipv6     ifup-tunnel
ifcfg-lo     ifdown-sit       ifup-isdn     ifup-wireless
ifdown       ifdown-Team      ifup-plip     init.ipv6-global
ifdown-bnep  ifdown-TeamPort  ifup-plusb    network-functions
ifdown-eth   ifdown-tunnel    ifup-post     network-functions-ipv6
ifdown-ippp  ifup             ifup-ppp
[root@hecs-x-medium-2-linux-20200610094343 network-scripts]# 
#查看网卡信息
ifconfig
```

Linux系统中使用以下命令来查看文件的内容：

- cat 由第一行开始显示文件内容
- tac 从最后一行开始显示，可以看出 tac 是 cat 的倒着写！
- nl  显示的时候，顺道输出行号！
- more 一页一页的显示文件内容
- less 与 more 类似，但是比 more 更好的是，他可以往前翻页！ q实现退出，f显示行号，在页面/xx向下查找字符串，？xx向上查找，n继续搜寻下一个，N
- head 只看头几行
- tail 只看尾巴几行

```cmd
#cat 由第一行开始显示文件内容
[root@hecs-x-medium-2-linux-20200610094343 network-scripts]# cat ifcfg-eth0
DEVICE="eth0"
BOOTPROTO="dhcp"
ONBOOT="yes"
TYPE="Ethernet"
PERSISTENT_DHCLIENT="yes"
#tac 从最后一行开始显示，可以看出 tac 是 cat 的倒着写！
[root@hecs-x-medium-2-linux-20200610094343 network-scripts]# tac ifcfg-eth0
PERSISTENT_DHCLIENT="yes"
TYPE="Ethernet"
ONBOOT="yes"
BOOTPROTO="dhcp"
DEVICE="eth0"
#nl  显示的时候，顺道输出行号！
[root@hecs-x-medium-2-linux-20200610094343 network-scripts]# nl ifcfg-eth0
     1	DEVICE="eth0"
     2	BOOTPROTO="dhcp"
     3	ONBOOT="yes"
     4	TYPE="Ethernet"
     5	PERSISTENT_DHCLIENT="yes"
#more 一页一页的显示文件内容 空间键翻页，enter一行一行翻
[root@hecs-x-medium-2-linux-20200610094343 network-scripts]# more ifcfg-eth0
#head 只看头几行
[root@hecs-x-medium-2-linux-20200610094343 network-scripts]# head -2 ifcfg-eth0
DEVICE="eth0"
BOOTPROTO="dhcp"
#
```

#### 解压和压缩

> gzip用于压缩文件  gunzip用于解压

```cmd
#将文件编程.gz 文件
gzip  *.txt
#将gz压缩文解压缩为.txt
gunzip *.txt.gz
```

> zip/unzip  

```cmd
#将文件压缩成*.zip
zip -r *.zip /xx/
#解压到/xx/
unzip -d /opt/tmp
```

> tar 指令

```
tar [选项] xx.tar.gz
tar -zvxf redis.tar.gz
```

* -c 产生.tar打包文件
* -v 显示详细信息
* -f指定压缩后的文件名
* -z打包同时压缩
* -x解包.tar文件

### 5.vim

<img src="https://gitee.com/shaobing2021/typora/raw/master/img/20200728100307.png" alt="vim键盘图" style="zoom:150%;" />

#### vim模式

基本上 vi/vim 共分为三种模式，分别是**命令模式（Command mode）**，**输入模式（Insert mode）**和**底线命令模式（Last line mode）**。这三种模式的作用分别是：

**命令模式：**

用户刚刚启动 vi/vim，便进入了命令模式。

此状态下敲击键盘动作会被Vim识别为命令，而非输入字符。比如我们此时按下i，并不会输入一个字符，i被当作了一个命令。

以下是常用的几个命令：

- **i** 切换到输入模式，以输入字符。
- **x** 删除当前光标所在处的字符。
- **:** 切换到底线命令模式，以在最底一行输入命令。

若想要编辑文本：启动Vim，进入了命令模式，按下i，切换到输入模式。

命令模式只有一些最基本的命令，因此仍要依靠底线命令模式输入更多命令。

**输入模式：**

在命令模式下按下i就进入了输入模式。

在输入模式中，可以使用以下按键：

- **字符按键以及Shift组合**，输入字符
- **ENTER**，回车键，换行
- **BACK SPACE**，退格键，删除光标前一个字符
- **DEL**，删除键，删除光标后一个字符
- **方向键**，在文本中移动光标
- **HOME**/**END**，移动光标到行首/行尾
- **Page Up**/**Page Down**，上/下翻页
- **Insert**，切换光标为输入/替换模式，光标将变成竖线/下划线
- **ESC**，退出输入模式，切换到命令模式

**底线命令模式**

在命令模式下按下:（英文冒号）就进入了底线命令模式。

底线命令模式可以输入单个或多个字符的命令，可用的命令非常多。

在底线命令模式中，基本的命令有（已经省略了冒号）：

- q 退出程序
- w 保存文件
- x 保存退出
- shift+zz保存退出

按ESC键可随时退出底线命令模式。

简单的说，我们可以将这三个模式想成底下的图标来表示：

#### vim按键

注意使用数字时，应该ESC模式下，按上排数字



**第一部分：一般模式可用的光标移动、复制粘贴、搜索替换等**

| 移动光标的方法     |                                                              |
| :----------------- | ------------------------------------------------------------ |
| h 或 向左箭头键(←) | 光标向左移动一个字符                                         |
| j 或 向下箭头键(↓) | 光标向下移动一个字符                                         |
| k 或 向上箭头键(↑) | 光标向上移动一个字符                                         |
| l 或 向右箭头键(→) | 光标向右移动一个字符                                         |
| [Ctrl] + [f]       | 屏幕『向下』移动一页，相当于 [Page Down]按键 (常用)          |
| [Ctrl] + [b]       | 屏幕『向上』移动一页，相当于 [Page Up] 按键 (常用)           |
| [Ctrl] + [d]       | 屏幕『向下』移动半页                                         |
| [Ctrl] + [u]       | 屏幕『向上』移动半页                                         |
| +                  | 光标移动到非空格符的下一行                                   |
| -                  | 光标移动到非空格符的上一行                                   |
| n< space>          | 那个 n 表示『数字』，例如 20 。按下数字后再按空格键，光标会向右移动这一行的 n 个字符。 |
| 0 或功能键[Home]   | 这是数字『 0 』：移动到这一行的最前面字符处 (常用)           |
| $ 或功能键[End]    | 移动到这一行的最后面字符处(常用)                             |
| H                  | 光标移动到这个屏幕的最上方那一行的第一个字符                 |
| M                  | 光标移动到这个屏幕的中央那一行的第一个字符                   |
| L                  | 光标移动到这个屏幕的最下方那一行的第一个字符                 |
| ==G==              | 移动到这个档案的最后一行(常用)                               |
| nG                 | n 为数字。移动到这个档案的第 n 行。例如 20G 则会移动到这个档案的第 20 行(可配合 :set nu) |
| ==gg==             | 移动到这个档案的第一行，相当于 1G 啊！(常用)                 |
| n< Enter>          | n 为数字。光标向下移动 n 行(常用)                            |
| ==/*或/字符串==    | 查找某个字符串                                               |

| 搜索替换 |                                                              |
| :------- | ------------------------------------------------------------ |
| /word    | 向光标之下寻找一个名称为 word 的字符串。例如要在档案内搜寻 vbird 这个字符串，就输入 /vbird 即可！(常用) |
| ?word    | 向光标之上寻找一个字符串名称为 word 的字符串。               |
| n        | 这个 n 是英文按键。代表重复前一个搜寻的动作。举例来说， 如果刚刚我们执行 /vbird 去向下搜寻 vbird 这个字符串，则按下 n 后，会向下继续搜寻下一个名称为 vbird 的字符串。如果是执行 ?vbird 的话，那么按下 n 则会向上继续搜寻名称为 vbird 的字符串！ |
| N        | 这个 N 是英文按键。与 n 刚好相反，为『反向』进行前一个搜寻动作。例如 /vbird 后，按下 N 则表示『向上』搜寻 vbird 。 |

| 删除、复制与粘贴 |                                                              |
| :--------------- | ------------------------------------------------------------ |
| x, X             | 在一行字当中，x 为向后删除一个字符 (相当于 [del] 按键)， X 为向前删除一个字符(相当于 [backspace] 亦即是退格键) (常用) |
| nx               | n 为数字，连续向后删除 n 个字符。举例来说，我要连续删除 10 个字符， 『10x』。 |
| dd               | 删除游标所在的那一整行(常用)                                 |
| ndd              | n 为数字。删除光标所在的向下 n 行，例如 20dd 则是删除 20 行 (常用) |
| d1G              | 删除光标所在到第一行的所有数据                               |
| dG               | 删除光标所在到最后一行的所有数据                             |
| d$               | 删除游标所在处，到该行的最后一个字符                         |
| d0               | 那个是数字的 0 ，删除游标所在处，到该行的最前面一个字符      |
| yy               | 复制游标所在的那一行(常用)                                   |
| nyy              | n 为数字。复制光标所在的向下 n 行，例如 20yy 则是复制 20 行(常用) |
| y1G              | 复制游标所在行到第一行的所有数据                             |
| yG               | 复制游标所在行到最后一行的所有数据                           |
| y0               | 复制光标所在的那个字符到该行行首的所有数据                   |
| y$               | 复制光标所在的那个字符到该行行尾的所有数据                   |
| p, P             | p 为将已复制的数据在光标下一行贴上，P 则为贴在游标上一行！举例来说，我目前光标在第 20 行，且已经复制了 10 行数据。则按下 p 后， 那 10 行数据会贴在原本的 20 行之后，亦即由 21 行开始贴。但如果是按下 P 呢？那么原本的第 20 行会被推到变成 30 行。(常用) |
| J                | 将光标所在行与下一行的数据结合成同一行                       |
| c                | 重复删除多个数据，例如向下删除 10 行，[ 10cj ]               |
| u                | 复原前一个动作。(常用)                                       |
| [Ctrl]+r         | 重做上一个动作。(常用)                                       |

**第二部分：一般模式切换到编辑模式的可用的按钮说明**

| 进入输入或取代的编辑模式 |                                                              |
| :----------------------- | ------------------------------------------------------------ |
| i, I                     | 进入输入模式(Insert mode)：i 为『从目前光标所在处输入』， I 为『在目前所在行的第一个非空格符处开始输入』。(常用) |
| a, A                     | 进入输入模式(Insert mode)：a 为『从目前光标所在的下一个字符处开始输入』， A 为『从光标所在行的最后一个字符处开始输入』。(常用) |
| o, O                     | 进入输入模式(Insert mode)：这是英文字母 o 的大小写。o 为『在目前光标所在的下一行处输入新的一行』；O 为在目前光标所在处的上一行输入新的一行！(常用) |
| r, R                     | 进入取代模式(Replace mode)：r 只会取代光标所在的那一个字符一次；R会一直取代光标所在的文字，直到按下 ESC 为止；(常用) |
| [Esc]                    | 退出编辑模式，回到一般模式中(常用)                           |

**第三部分：一般模式切换到指令行模式的可用的按钮说明**

| 指令行的储存、离开等指令                                     |                                                              |
| :----------------------------------------------------------- | ------------------------------------------------------------ |
| :w                                                           | 将编辑的数据写入硬盘档案中(常用)                             |
| :w!                                                          | 若文件属性为『只读』时，强制写入该档案。不过，到底能不能写入， 还是跟你对该档案的档案权限有关啊！ |
| :q                                                           | 离开 vi (常用)                                               |
| :q!                                                          | 若曾修改过档案，又不想储存，使用 ! 为强制离开不储存档案。    |
| 注意一下啊，那个惊叹号 (!) 在 vi 当中，常常具有『强制』的意思～ |                                                              |
| :wq                                                          | 储存后离开，若为 :wq! 则为强制储存后离开 (常用)              |
| ZZ                                                           | 这是大写的 Z 喔！若档案没有更动，则不储存离开，若档案已经被更动过，则储存后离开！ |
| :w [filename]                                                | 将编辑的数据储存成另一个档案（类似另存新档）                 |
| :r [filename]                                                | 在编辑的数据中，读入另一个档案的数据。亦即将 『filename』 这个档案内容加到游标所在行后面 |
| :n1,n2 w [filename]                                          | 将 n1 到 n2 的内容储存成 filename 这个档案。                 |
| :! command                                                   | 暂时离开 vi 到指令行模式下执行 command 的显示结果！例如 『:! ls /home』即可在 vi 当中看 /home 底下以 ls 输出的档案信息！ |
| ==:set nu==                                                  | 显示行号，设定之后，会在每一行的前缀显示该行的行号           |
| :set nonu                                                    | 与 set nu 相反，为取消行号！                                 |

### 6.账号管理

#### 命令

> 添加账号 useradd 选项 用户名

参数说明：

- 选项 :

- - -c comment 指定一段注释性描述。
  - -d 目录 指定用户主目录，如果此目录不存在，则同时使用-m选项，可以创建主目录。
  - -g 用户组 指定用户所属的用户组。
  - -G 用户组，用户组 指定用户所属的附加组。
  - ==-m==　使用者目录如不存在则自动建立。
  - -s Shell文件 指定用户的登录Shell。
  - -u 用户号 指定用户的用户号，如果同时有-o选项，则可以重复使用其他用户的标识号。

- 用户名 :

- - 指定新账号的登录名。

> 删除用户 userdel -r 用户名

> 修改用户 usermod 选项 用户名

-c, -d, -m, -g, -G, -s, -u以及-o等，这些选项的意义与useradd命令中的选项一样，可以为用户指定新的资源值。

> 切换用户

> 用户管理

```
passwd 选项 用户名
```

- -l 锁定口令，即禁用账号。
- -u 口令解锁。
- -d 使账号无口令。
- -f 强迫用户下次登录时修改口令。

```cmd
#添加用户连同文件夹
useradd -m shaobing
#删除用户连同文件夹
userdel -r shaobing
#将shaobing用户绑定的路径修改为/home/test
usermod -d /home/test shaobing
#查看用户信息
[root@hecs-x-medium-2-linux-20200610094343 etc]# cat passwd
shoabing:x:1000:1000::/home/shoabing:/bin/bash
#切换用户
[root@hecs-x-medium-2-linux-20200610094343 home]# su shaobing
#退出用户
[shaobing@hecs-x-medium-2-linux-20200610094343 home]$ exit
#修改/查看用户名，双击标签直接重连
hostname 用户名
#为用户添加密码
[root@roosevelt home]# passwd shaobing
#锁定账号，-l清空密码，-d没有密码也不能登录
[root@roosevelt home]# passwd -l shaobing
Locking password for user shaobing.
passwd: Success
```

#### 用户组管理

> 修改用户组

- -g GID 为用户组指定新的组标识号。
- -o 与-g选项同时使用，用户组的新GID可以与系统已有用户组的GID相同。
- -n新用户组 将用户组的名字改为新名字

> 切换组

```cmd
[root@roosevelt ~]# groupadd g1
#查看用户组
[root@roosevelt ~]# cat /etc/group
#删除用户组
groupdel g1
#修改用户组id以及名称
[root@roosevelt ~]# groupmod -g 666 -n newg1 g1
#修稿用户所属组以及名称
[root@roosevelt ~]# usermod -g newg1 shaobing
[root@roosevelt ~]# id shaobing
uid=1000(shaobing) gid=666(newg1) groups=666(newg1)
```

> /etc/passwd

```cmd
cat /etc/passwd
用户名:口令:用户标识号:组标识号:注释性描述:主目录:登录Shell
shaobing:x:1000:666::/home/shaobing:/bin/bash
cat /etc/shadow
登录名:加密口令:最后一次修改时间:最小时间间隔:最大时间间隔:警告时间:不活动时间:失效时间:标志
cat /etc/group
组名:口令:组标识号:组内用户列表
```

#### 磁盘管理

> 查看磁盘

语法：

```
df [-ahikHTm] [目录或文件名]
```

选项与参数：

- -a ：列出所有的文件系统，包括系统特有的 /proc 等文件系统；
- -k ：以 KBytes 的容量显示各文件系统；
- -m ：以 MBytes 的容量显示各文件系统；
- -h ：以人们较易阅读的 GBytes, MBytes, KBytes 等格式自行显示；
- -H ：以 M=1000K 取代 M=1024K 的进位方式；
- -T ：显示文件系统类型, 连同该 partition 的 filesystem 名称 (例如 ext3) 也列出；
- -i ：不用硬盘容量，而以 inode 的数量来显示

> du

Linux du命令也是查看使用空间的，但是与df命令不同的是Linux du命令是对文件和目录磁盘使用的空间的查看，还是和df命令有一些区别的，这里介绍Linux du命令。

语法：

```
du [-ahskm] 文件或目录名称
```

选项与参数：

- -a ：列出·目录容量，因为默认仅统计目录底下的文件量而已。
- -h ：以人们较易读的容量格式 (G/M) 显示；
- -s ：列出总量而已，而不列出每个各别的目录占用容量；
- -S ：不包括子目录下的总计，与 -s 有点差别。
- -k ：以 KBytes 列出容量显示；
- -m ：以 MBytes 列出容量显示；

```cmd
#检查文件系统的磁盘空间占用情况。可以利用该命令来获取硬盘被占用了多少空间，目前还剩下多少空间等信息。
[root@roosevelt ~]# df
Filesystem     1K-blocks    Used Available Use% Mounted on
devtmpfs          930492       0    930492   0% /dev
tmpfs             940844       0    940844   0% /dev/shm
tmpfs             940844    8736    932108   1% /run
tmpfs             940844       0    940844   0% /sys/fs/cgroup
/dev/vda1       41152736 2110340  36928912   6% /
tmpfs             188172       0    188172   0% /run/user/0
#以人们较易阅读的 GBytes, MBytes, KBytes 等格式自行显示；
[root@roosevelt ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        909M     0  909M   0% /dev
tmpfs           919M     0  919M   0% /dev/shm
tmpfs           919M  8.6M  911M   1% /run
tmpfs           919M     0  919M   0% /sys/fs/cgroup
/dev/vda1        40G  2.1G   36G   6% /
tmpfs           184M     0  184M   0% /run/user/0
# du命令是对文件和目录磁盘使用的空间的查看
[root@roosevelt ~]# du
4	./.ssh
4	./.pki/nssdb
8	./.pki
52	.
#列出所有的文件与目录容量，因为默认仅统计目录底下的文件量而已。 查看隐藏的
[root@roosevelt ~]# du -a
4	./.viminfo
4	./.lesshst
4	./.bashrc
0	./.history
0	./.ssh/authorized_keys
4	./.ssh
4	./.pki/nssdb
8	./.pki
4	./.bash_profile
4	./.tcshrc
4	./.bash_logout
8	./.bash_history
4	./.cshrc
52	.
#检查根目录下每个目录占的容量
[root@roosevelt ~]# du -sm /*
0	/bin
132	/boot
212	/CloudResetPwdUpdateAgent
3	/CloudrResetPwdAgent
0	/dev
34	/etc
1417	/usr

```

> 挂载

```
mount [-t 文件系统] [-L Label名] [-o 额外选项] [-n] 装置文件名 挂载点
```

磁盘卸载命令 umount 语法：

```
umount [-fn] 装置文件名或挂载点
```

选项与参数：

- -f ：强制卸除！可用在类似网络文件系统 (NFS) 无法读取到的情况下；
- -n ：不升级 /etc/mtab 情况下卸除。

卸载/dev/hdc6

#### 增加磁盘实例

```
#1.vmware中右键设置新建磁盘
#2.lsblk显示没有新磁盘，reboot重新启动
#3.看到了但是
```



#### 进程管理

> ps  显示系统执行的进程

ps -aux：

* -a：显示当前终端的所有进程信息
* -u：以用户的格式显示进程信息
* -x：显示后台进程运行的参数

```
#查看某个进程
ps -aux|grep mysql
#查看父进程信息
ps -ef|grep mysql
pstreee -pu
	-p 显示父id
	-u 显示用户组
```

> 杀掉进程

```cmd
#杀掉某个进程
kill -9 进程ID
```

### 7.网络问题

```
#当发现linux没有ens33
ifconfig ens33 up
systemctl stop NetworkManager
systemctl disable NetworkManager
ifup ens33
systemctl restart network.service
```

