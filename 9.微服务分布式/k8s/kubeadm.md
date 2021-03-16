[k8s官网](http://docs.kubernetes.org.cn/774.html)

## 安装Linux

[安装centos](https://www.runoob.com/w3cnote/vmware-install-centos7.html)

[k8s搭建教程](https://blog.csdn.net/qq_41813208/article/details/108124605)

[K8s搭建集群视频教程](https://ke.qq.com/course/379938?taid=2934278707334178)

[问题合集](https://www.jianshu.com/p/300ab005ce51)

### 配置安装组

### [阿里云镜像地址](https://developer.aliyun.com/mirror/centos?spm=a2c6h.13651102.0.0.3e221b11pql8r8)

### 更改镜像源

```cmd
#查看Linux版本
[root@shaobing ~]# cat /proc/version
Linux version 3.10.0-1127.8.2.el7.x86_64 (mockbuild@kbuilder.bsys.centos.org) (gcc version 4.8.5 20150623 (Red Hat 4.8.5-39) (GCC) ) #1 SMP Tue May 12 16:57:42 UTC 2020
#查看系统版本
[root@shaobing ~]#  lsb_release -a
LSB Version:	:core-4.1-amd64:core-4.1-noarch
Distributor ID:	CentOS
Description:	CentOS Linux release 7.2.1511 (Core) 
Release:	7.2.1511
Codename:	Core
#安装wget
[root@shaobing ~]# yum install -y wget
Loaded plugins: langpacks, versionlock
#备份源
[root@shaobing ~]# mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
#下载新的centos-base
[root@shaobing ~]# wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
--2020-08-22 13:28:23--  https://mirrors.aliyun.com/repo/Centos-7.repo
#生成缓存
[root@shaobing ~]# yum makecache
Loaded plugins: langpacks, versionlock
```

### 无法ping通网络

> 虚拟机中没有en33网卡

```
#解决没有ens3
sudo dhclient ens33
sudo ifconfig ens33

输入这个命令检查一下是不是disable
nmcli n
如果是的话，开启即可（输入下面这行命令）
nmcli n on

#暴力解决重启centos后没有ens33
systemctl stop NetworkManager
systemctl disable NetworkManager
systemctl start network.service
```

### 克隆虚拟机

1. 右键管理克隆

2. 克隆虚拟机当前状态

3. 位置```F:\downloads\Virutual Machines\k8s\k8s-node1

4. 更改ip-->虚拟网络编辑器-->子网IP192.168.65.0  255.255.255.0-->NAT设置  192.168.65.2-->起始ip和结束ip  192.168.65.128-254

5. ```
   vim /etc/sysconfig/network-scripts/ifcfg-ens33
   BOOTPROTO=static
   制定静态IP和网关
   service network restart
   systemctl restart network.service
   ip addr
   ```

6. ```
   TYPE="Ethernet"
   PROXY_METHOD="none"
   BROWSER_ONLY="no"
   BOOTPROTO="static"
   DEFROUTE="yes"
   IPV4_FAILURE_FATAL="no"
   IPV6INIT="yes"
   IPV6_AUTOCONF="yes"
   IPV6_DEFROUTE="yes"
   IPV6_FAILURE_FATAL="no"
   IPV6_ADDR_GEN_MODE="stable-privacy"
   NAME="ens33"
   DEVICE="ens33"
   ONBOOT="yes" 
   IPADDR=192.168.11.131
   NETMASK=255.255.255.0
   GATEWAY=192.168.11.2
   ```

   UUID="e2564c1c-70f2-4f31-8067-3887e0b594ef"

## 安装kuberadmin

```
k8s-master

192.168.11.130
192.168.11.131
```

### 0.安装前环境准备

```powershell
systemctl stop firewalld
systemctl disable firewalld
#关闭selinux
sed -i 's/enforcing/disabled/' /etc/selinux/config # 永久
setenforce 0 # 临时
swapoff -a # 临时
hostnamectl set-hostname k8smaster
```



### 1.安装docker

```
#查看系统版本
cat /etc/os-release  
#卸载旧的
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
#安装需要的安装包               
yum install -y yum-utils
#设置镜像仓库
sudo yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
    
#更新yum
yum makecache fast
#安装docker,-y自动确认
sudo yum install -y docker-ce docker-ce-cli containerd.io
#启动docker
sudo systemctl start docker
#查看版本
docker version
docker -v
#设置开机自启
sudo systemctl enable docker
#查看正在运行的容器
docker ps
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
"registry-mirrors": ["https://0814bccd658026be0fcdc005453438c0.mirror.swr.myhuaweicloud.com"]
}
EOF
sudo systemctl daemon-reload && sudo systemctl restart docker
#重启docker

```

### 2.添加阿里云

```
cat > /etc/yum.repos.d/kubernetes.repo << EOF
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
```

### 3.安装kubeadmin

```powershell
#看是否有相关源
yum list|grep kube
#安装,无法制定版本kubelet-1.17.3
yum install -y kubelet kubeadm kubectl
#设置开机自启
systemctl enable kubelet & systemctl start kubelet
#检查是否启动成功
systemctl status kubelet
#查看k8s集群版本信息
kubectl version
```

### 4.部署master

```
#注意更改apiserver和version
kubeadm init \
--apiserver-advertise-address=192.168.11.130 \
--image-repository registry.aliyuncs.com/google_containers \
--kubernetes-version v1.20.4 \
--service-cidr=10.96.0.0/12 \
--pod-network-cidr=10.244.0.0/16
```

### 5.使用kubectl工具

```
#根据输出信息
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

```

### 6.安装pod网络插件

1.查询网址真实ip地址[查询地址](https://www.ipaddress.com/ )

2.

```
sudo vim /etc/hosts
地址  raw.githubuserconten.com
```



```
kubectl apply –f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kubeflannel.yml
```



