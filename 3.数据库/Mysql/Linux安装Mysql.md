

> Ubuntu安装数据库

```cmd
sudo apt-get install mysql-server          //服务端
sudo apt-get install mysql-client          //客户端
sudo netstat -tap | grep mysql //查看是否安装成功
mysql -u root -p    //登录
show databases；
use mysql;
update user set authentication_string=PASSWORD("root") where user='root';
update user set plugin="mysql_native_password";
flush privileges;
quit;
```

> 本地连接3306失败

```cmd
update user set host = '%' where user = 'root';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;
FLUSH PRIVILEGES;


sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
bind-address = 0.0.0.0
sudo service start 
```



```
一、启动方式

1、使用 service 启动：service mysqld start

2、使用 mysqld 脚本启动：/etc/inint.d/mysqld start

3、使用 safe_mysqld 启动：safe_mysqld&

二、停止

1、使用 service 启动：service mysqld stop

2、使用 mysqld 脚本启动：/etc/inint.d/mysqld stop

3、mysqladmin shutdown

三、重启

1、使用 service 启动：service mysqld restart

2、使用 mysqld 脚本启动：/etc/inint.d/mysqld restart
```

