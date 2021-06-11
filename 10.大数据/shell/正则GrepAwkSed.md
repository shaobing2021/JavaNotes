## grep举例

> grep "root passwd" /etc/passwd

“”意义在于如果没有“”就会以为是grep root 文件名 文件名

```
#匹配数字
[root@k8smaster shell-05]# egrep ^[0-9]+$ test1.txt 
12
1
[root@k8smaster shell-05]# cat test1.txt 
123$
a$
12
1

#匹配邮箱
[root@k8smaster shell-05]# egrep [a-z0-9_]+@[a-z0-9]+\.[a-z]+ test3.txt 

#匹配IP
[root@k8smaster shell-05]# egrep '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}' /etc/sysconfig/network-scripts/ifcfg-ens33 
IPADDR=192.168.11.130
NETMASK=255.255.255.0
GATEWAY=192.168.11.2

[root@k8smaster shell-05]# egrep '[[:digit:]]{1,3}\.[[:digit:]]{1,3}\.[[:digit:]]{1,3}' /etc/sysconfig/network-scripts/ifcfg-ens33 
```

### 三种grep区别

grep:使用基本元字符集，^,$,.,*,【】，\\<\\>.\\(\\),\\\{\\},\+,\|

egrep:使用扩展元字符?,+，{}，｜，（）

grep也可以使用扩展集中的元字符，仅需对这些元字符前置反斜线

\w。 所有字母与数字，称为字符[a-zA-Z0-9].  'l[a-zA-Z0-9]*ve'. 'l\\w\*ve'

\W.  所有字母与数字之外的字符，称为非字符， 'love[\^a-zA-Z0-9]+'. 'love\\W\+'

\b.  词边界。   '\\<\love\>'    '\blove\b'。 词首词尾   即grep '\bro\b' passwpord. 只会过滤包含ro的

### grep学习

```
#为什么说推荐加''，如grep bash shell /etc/passwd 可能被以为过滤bash，从shell以及/etc/passwd
grep '^root' /etc/passwd /etc/shadow
#-q不输出查找到的文件
grep -q '^root' /etc/passwd
#echo $?
0表示成功找到
1表示没找到
2表示后缀文件名有问题，没有该文件
#帮助文档
man grep
#过滤目录
ll |grep '^d'
```



## 基本元字符

shell元字符也称为通配符  

正则表达式元字符，执行模式匹配操作的程序，如vi，grep，sed，awk

```
#此处*为任意一个或多个字符
rm -rf *.pdf
#abc*，此处*指的是c为一个或者任意多个
grep 'abc*' /etc/passwd
egrep 'abc*' /etc/passwd
```

### 基本元字符

```shell
^  行首定位符  
$  行尾定位符
.  匹配单个字符，必须匹配一个
*  匹配前导符0到多次   
#可以没有o
[root@k8smaster ~]# grep 'ro*t' /etc/passwd
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin
abrt:x:173:173::/etc/abrt:/sbin/nologin
rtkit:x:172:172:RealtimeKit:/proc:/sbin/nologin

.*任意多个字符
#前面一个或者多个，比如下带rt就会显示出来
grep 'r.*t' /etc/passwd

[]  匹配指定范围内的一个字符  [Rr]oot
[-] 匹配指定范围的一个字符    [a-z0-9]ove

[root@k8smaster shell-05]# grep [a-z]oot /etc/passwd
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin
setroubleshoot:x:991:987::/var/lib/setroubleshoot:/sbin/nologin

注意一点：ll l[a-z]ve   这里不会区分大小写，因为这是shell元字符
#涵盖大小写的写法
[root@k8smaster shell-05]# grep [a-Z]ot test523.txt 
root
rot
Aot
Bot
aot
zot

[^]不在组内的字符

#以rot开头的
[root@k8smaster shell-05]# grep '^[root]' test523.txt 
#以root开头的
[root@k8smaster shell-05]# grep '^root' test523.txt 
#[^root]不带root的，^[^root]不是以root开头的
[root@k8smaster shell-05]# grep '^[^root]' test523.txt 

```

### 转义元字符

```powershell
\  用来转义元字符，使特殊字符失去作用
[root@k8smaster shell-05]# grep "t.t" test523.txt 
tot
t.t
t$t
t\t
[root@k8smaster shell-05]# grep "t\.t" test523.txt 
t.t

\< 词首定位符
\> 词尾定位符
[root@k8smaster shell-05]# grep "\<ro\>" test523.txt 
ro.t
ro*t

\(..\)  匹配稍后使用的标签
:%s#192.168.11.130#192.168.11.131#
这里也可以\1\2\3
:%s#\(192.168.11.\)131#\1133#         
:3,9 s/\(.*\)/#\1/ 把5-9行前面加#注释

x\{m\}  字符x重复出现m次
x\{m,\}   字符x出现m次以上
x\{m,n\}  字符x重复出现m次到n次
[root@k8smaster shell-05]# grep 'ro\{1,3\}' test523.txt 
root
ro.t
ro*t
rot
[root@k8smaster shell-05]# egrep 'ro{1}' test523.txt 
root
ro.t
ro*t
rot
[root@k8smaster shell-05]# egrep 'ro{2}' test523.txt 
root

#出现两次以上
[root@k8smaster shell-05]# egrep 'ro{2,}' test523.txt 
root
[root@k8smaster shell-05]# egrep 'ro{4,8}' test523.txt 
root
```

## egrep和grep区别以及fgrep

Grep:在文件中全局查找指定的正则表达式，并打印所有包含该表达式的行

egrep：扩展的grep，支持更多的正则表达式

fgrep：固定grep，有时也被称作快速fast grep，按字面解释所有字符

```powershell
注意一点grep不支持元字符，要想支持使用egrep
实验：
(f|g)ile
this is file;
[root@k8smaster test06]# grep '(f|g)ile' 9.txt
(f|g)ile
[root@k8smaster test06]# egrep '(f|g)ile' 9.txt
this is file;
[root@k8smaster test06]# egrep '\(f\|g\)ile' 9.txt
(f|g)ile
[root@k8smaster test06]# grep '\(f\|g\)ile' 9.txt
this is file;
```

## 扩展正则表达式元字符

```powershell
#*   n*。 表示n出现0到n次

#+ 匹配一个或多个前导字符    [a-z]+ove
[root@k8smaster test06]# egrep 'ro+t' /etc/passwd
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin

#? 匹配零个或一个前导字符。    lo？ve
[root@k8smaster test06]# egrep 'ro?t' /etc/passwd
abrt:x:173:173::/etc/abrt:/sbin/nologin
rtkit:x:172:172:RealtimeKit:/proc:/sbin/nologin

#a|b. 或的意思   \>结尾
[root@k8smaster test06]# ss -an |egrep ':80|:22\>'
tcp    LISTEN     0      128       *:22                    *:*
tcp    ESTAB      0      0      192.168.0.130:22                 192.168.0.103:64952
tcp    LISTEN     0      128    [::]:80                 [::]:*
tcp    LISTEN     0      128    [::]:22                 [::]:*

#()  组字符  (ov)+   ov一起出现一次或者多次  ov+ v出现一次或者多次
#(...)(...)\1\2标签匹配字符。(love)able\1er. \1指括号出现的内容

#x{m}  x重复m次
#x{m,}  x重复至少m次
#x{m,n}  x重复m到n次。 
```

## posix字符类

```powershell
[:alnum:]   字母与数字字符。  [[:alnum:]]+，{4},*,?,{5,},
[:alpha:]		字母字符（包括大小写字母）
[:blank:]		空格与制表符
[:digit:]		数字字母
[:lower:]		小写字母
[:upper:]		大写字母
[:punct:]		标点符号
[:space:]		包括换行符，回车等在内的所有空白
```



## alias

```
[root@k8smaster ~]# alias
alias cp='cp -i'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l.='ls -d .* --color=auto'
alias ll='ls -l --color=auto'
alias ls='ls --color=auto'
alias mv='mv -i'
alias rm='rm -i'
alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'
alias yang='date'

```

