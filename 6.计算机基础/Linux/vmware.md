

### 常见问题

1.虚拟机中ens33不见了

systemctl stop NetworkManager

systemctl disable NetworkManager

systemctl start network.service

## 局域网内互通

1.选择桥接模式

2.将ens33中ip和网管和自己用的网卡无线和有线网络一致

3.如果是vmnet1或vmnet8则应该将ens33修改成一致

