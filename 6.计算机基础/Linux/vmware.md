

### 常见问题

1.虚拟机中ens33不见了,

systemctl stop NetworkManager
#注意此时若直接重启网络，会报错，所以先stop NetworkManager
service network restart

systemctl disable NetworkManager

systemctl start network.service

## 局域网内互通

1.选择桥接模式

2.将ens33中ip和网关和自己用的网卡无线和有线网络一致

3.如果是vmnet1或vmnet8则应该将ens33修改成一致

## 桥接模式和NAT

桥接模式：虚拟机和宿主机加真实路由器形成网络

nat：虚拟机和宿主机vmnet网络和虚拟网卡组成一个网段，宿主机和真实网络形成另一个网段

## 桥接模式无法上网

通过route -n命令查看路由，发现GateWay和ens33中网关不一致

首先删除原网关route del default gw 192.168.11.2

Route -n 查看是否删除

route add default gw 192.168.0.1 修改网关和桥接模式对应真实物理节点以太网的网关，不能重启网络

## docker相关问题

* Vim /etc/selinux/config   systemctl restart docker
