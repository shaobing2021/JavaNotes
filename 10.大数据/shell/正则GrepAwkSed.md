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

## 元字符

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



```
\  用来转义元字符，是特殊字符失去作用
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

### 扩展正则表达式元字符

```
注意一点grep不支持元字符，要想支持使用egrep

```



### alias

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

