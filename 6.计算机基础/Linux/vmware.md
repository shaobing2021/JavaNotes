

### 常见问题

1.虚拟机中ens33不见了

systemctl stop NetworkManager

systemctl disable NetworkManager

systemctl start network.service

## 局域网内互通

1.选择桥接模式

2.将ens33中ip和网管和自己用的网卡无线和有线网络一致

3.如果是vmnet1或vmnet8则应该将ens33修改成一致

## 桥接模式和NAT

桥接模式：虚拟机和宿主机加真实路由器形成网络

nat：虚拟机和宿主机vmnet网络和虚拟网卡组成一个网段，宿主机和真实网络形成另一个网段

