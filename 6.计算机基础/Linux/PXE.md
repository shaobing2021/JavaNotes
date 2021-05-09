## PXE

### 配置静态网络

```sql
1.配置静态网络
/etc/sysconfig/network-scripts/ifcfg-ens33
```

### kickstart制作

#### 搭建ftp服务器

```
[root@k8smaster network-scripts]# yum install httpd system-config-kickstart -y
完毕！
[root@k8smaster network-scripts]# systemctl start httpd
[root@k8smaster network-scripts]# systemctl enable httpd
#外面访问以下虚拟机地址看能否访问
Created symlink from /etc/systemd/system/multi-user.target.wants/httpd.service to /usr/lib/systemd/system/httpd.service.
[root@k8smaster network-scripts]# systemctl stop firewalld
#这一步要从虚拟机里面执行
[root@k8smaster network-scripts]# system-config-kickstart
```

#### ks文件制作

![image-20210508220531941](https://gitee.com/shaobing2021/typora/raw/master/img/20210510011045.png)

> 如果这里是pxe，则选择ftp

![image-20210509224244359](https://gitee.com/shaobing2021/typora/raw/master/img/20210510011046.png)

![image-20210508220449396](https://gitee.com/shaobing2021/typora/raw/master/img/20210510011047.png)

![image-20210508221038412](https://gitee.com/shaobing2021/typora/raw/master/img/20210510011048.png)

![image-20210508221026693](https://gitee.com/shaobing2021/typora/raw/master/img/20210510011049.png)

添加分区/boot 200M

![image-20210508235635441](https://gitee.com/shaobing2021/typora/raw/master/img/20210510011050.png)

![image-20210508235753610](https://gitee.com/shaobing2021/typora/raw/master/img/20210510011051.png)

使用默认

![image-20210508235827774](https://gitee.com/shaobing2021/typora/raw/master/img/20210510011052.png)



![image-20210508235858126](https://gitee.com/shaobing2021/typora/raw/master/img/20210510011053.png)



![image-20210508235936223](https://gitee.com/shaobing2021/typora/raw/master/img/20210510011054.png)

#### 检测ks脚本语法

```
ksvalidator ks.cfg
```

### 扩展虚拟磁盘

```
shutdown
vmware中 设置——->扩展
```

### 制作PXE

#### 镜像下载并挂载

[地址](http://mirrors.aliyun.com/centos/7.9.2009/isos/x86_64/)

```
#安装必要的服务
yum install httpd dhcp system-config-kickstart -y
[root@k8smaster ~]# systemctl start httpd
[root@k8smaster ~]# systemctl status httpd

#开启http
[root@k8smaster opt]# systemctl enable httpd
Created symlink from /etc/systemd/system/multi-user.target.wants/httpd.service to /usr/lib/systemd/system/httpd.service.
#关闭防火墙
[root@k8smaster opt]# systemctl stop firewalld
[root@k8smaster opt]# systemctl disable firewalld
Removed symlink /etc/systemd/system/multi-user.target.wants/firewalld.service.
Removed symlink /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.
[root@k8smaster opt]# cd /var/www/html/
[root@k8smaster html]# ls
[root@k8smaster html]# mkdir iso_mini
[root@k8smaster html]# mkdir ks
#记得上传iso文件
[root@k8smaster html]# ls /opt/
CentOS-7-x86_64-Minimal-2009.iso  cni  containerd  rh
[root@k8smaster html]# mount /opt/CentOS-7-x86_64-Minimal-2009.iso /var/www/html/iso_mini/
mount: /dev/loop0 写保护，将以只读方式挂载
[root@k8smaster html]# vim /etc/rc.d/rc.local 
[root@k8smaster html]# chmod +x /etc/rc.d/rc.local

```

#### dhcp

```
[root@k8smaster dhcp-4.2.5]# cp /usr/share/doc/dhcp*/dhcpd.conf.example /etc/dhcp/dhcpd.conf
cp：是否覆盖"/etc/dhcp/dhcpd.conf"？ y
```

> dhcpd.conf

```
# dhcpd.conf
#
# Sample configuration file for ISC dhcpd
#

# option definitions common to all supported networks...
option domain-name "dahua.org";
option domain-name-servers 114.114.114.114;

default-lease-time 600;
max-lease-time 7200;

# Use this to enble / disable dynamic dns updates globally.
#ddns-update-style none;

# If this DHCP server is the official DHCP server for the local
# network, the authoritative directive should be uncommented.
#authoritative;

# Use this to send dhcp log messages to a different log file (you also
# have to hack syslog.conf to complete the redirection).
log-facility local7;

# No service will be given on this subnet, but declaring it helps the 
# DHCP server to understand the network topology.

subnet 10.152.187.0 netmask 255.255.255.0 {
}

# This is a very basic subnet declaration.

subnet 192.168.11.0 netmask 255.255.255.0 {
  range 192.168.11.75 192.168.11.99;
  option routers 192.168.11.250;
  next-server 192.168.11.131;
  filename "pxelinux.0"

}
```

![image-20210509181450395](https://gitee.com/shaobing2021/typora/raw/master/img/20210510011055.png)

#### pxelinux

![image-20210509185036028](https://gitee.com/shaobing2021/typora/raw/master/img/20210510011056.png)



```
[root@k8smaster dhcp]# yum search pxelinux
[root@k8smaster ~]# yum install syslinux
#查看pxelinux.txt其实就和syslinux差不多
[root@k8smaster ks]# vi /usr/share/doc/syslinux-4.05/pxelinux.txt
[root@k8smaster ks]# yum search tftp
#安装tftp
[root@k8smaster ks]# yum install tftp-server.x86_64 -y
#启动tftp
[root@k8smaster ks]# systemctl start tftp
[root@k8smaster ks]# systemctl enable tftp
#查看是否正常运行
[root@k8smaster ks]# systemctl status tftp
[root@k8smaster ks]# cd /var/lib/tftpboot/
[root@k8smaster ks]# cd /var/lib/tftpboot/
[root@k8smaster tftpboot]# ls
[root@k8smaster tftpboot]# cp /usr/share/syslinux/pxelinux.0 /var/lib/tftpboot/
[root@k8smaster tftpboot]# ls
pxelinux.0
#这个文件核心作用就是读取pxelinux.cfg/default文件
[root@k8smaster tftpboot]# file pxelinux.0 
pxelinux.0: data
[root@k8smaster tftpboot]# cp /var/www/html/iso_mini/isolinux/isolinux.cfg pxelinux.cfg/default
#添加tftp地址，详情见dhcpd.conf上，得到ip地址，获取文件
[root@k8smaster tftpboot]# vim /etc/dhcp/dhcpd.conf 
[root@k8smaster tftpboot]# systemctl restart dhcpd
[root@k8smaster tftpboot]# cp -r /var/www/html/iso_mini/isolinux/* /var/lib/tftpboot/
[root@k8smaster tftpboot]# ls
```

#### 修改default

```
[root@k8smaster pxelinux.cfg]# vim /var/lib/tftpboot/pxelinux.cfg/default

label linux
  menu label ^Install Dahua CentOS 7
  kernel vmlinuz
  append initrd=initrd.img repo=http://192.168.11.131/iso_mini 	   	   ks=http://192.168.1.131/ks/ks.cfg

```

所有配置重启

```
systemctl restart tftp
[root@k8smaster ks]# systemctl restart httpd
[root@k8smaster ks]# systemctl restart tftp
[root@k8smaster ks]# systemctl restart tftp.socket
[root@k8smaster ks]# systemctl restart dhcpd

```