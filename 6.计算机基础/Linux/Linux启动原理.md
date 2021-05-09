## 实战

[Centos安装](https://blog.csdn.net/qq_27754983/article/details/75913640?utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.control&dist_request_id=1619541270708_91255&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.control)

## 原理

file initramfs.iimg

发现cpio

-idv 还原 显示信息

![image-20210304004240755](https://gitee.com/shaobing2021/typora/raw/master/img/20210510011102.png)



![image-20210304004855839](https://gitee.com/shaobing2021/typora/raw/master/img/20210510011103.png)





## 制作mini Linux

```
#查看目前硬盘
lsblk 
#分区
fdisk /dev/sdb 
n
1
+500M
n
w
#查看目前硬盘sdb是否分好区
lsblk 
#格式化
mkfs.ext4 /dev/sdb1 /dev/sdb2
#挂载
mount /dev/sdb1 /mnt/boot

#修改grub.conf为当前sdb里面的uuid
```


