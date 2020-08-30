## 静态资源服务器

### 前置准备

```java
#将nginx中的目录进行挂载/etc/nginx目录
docker run -d -P --name nginx15 -v /etc/nginx nginx
#查看容器id
docker ps
#通过容器id通过mounts查看挂载的卷目录信息
docker instpect 容器id
#在宿主机上挂载的目录下新建images目录，并且存放图片
/var/lib/docker/volumes/9c58f2692805f662c341723d6db08dfe2d64e98a447a47b14ba68b38fd7c4341/_data/images
#在nginx容器中可以查看图片信息/etc/nginx/images
root@36e9e7f80a94:/etc/nginx/images# ls
avatar.jpg  blue.jpg
```

### 修改nginx.conf文件

```conf
server {
        #监听端口，这里肯定是80
        listen 80;
        #服务器名称这里 我也不太清楚到底该填什么
       # server_name 127.0.0.1 localhost;
        #字符集
        charset utf-8;
        #access_log  logs/host.access.log  main;
        location / {
           root   html;
           index  index.html index.htm;
        }
#通过ip:端口/images/1.jpg，其实访问的是ip:端口/etc/nginx/imges/1.jpg ,所以这里别再添加其他额外信息
        #添加一个模块 访问图片
        location /images/{
           root /etc/nginx;
           autoindex on;
        }
}
```

### 完整配置

```conf

user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;
server {
        #监听端口
        listen 80;
        #服务器名称
       # server_name 127.0.0.1 localhost;
        #字符集
        charset utf-8;

        #access_log  logs/host.access.log  main;

        location / {
           root   html;
           index  index.html index.htm;
        }

        #添加一个模块 访问图片
        location /images/{
           root /etc/nginx;
           autoindex on;
        }
}
    #gzip  on;
    include /etc/nginx/conf.d/*.conf;
}

```

## 设置代理

### 监听

```cmd
 server {
        listen       8088;
        server_name  127.0.0.1 localhost;
        #charset koi8-r;
        #access_log  logs/host.access.log  main;
        location / {
			proxy_pass  http://redis;	
            #root   html;
            #index  index.html index.htm;
			
        }
        #error_page  404              /404.html;
        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root    https://172.28.2.18:8082;
        }
```

### 被代理

```cmd
upstream redis{
		server 127.0.0.1:8080;
		server 127.0.0.1:8082 weight=2;
	}
```

### 完整代码

```cmd

user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;
server {
        #监听端口
        listen 80;
        #服务器名称
       # server_name 127.0.0.1 localhost;
        #字符集
        charset utf-8;

        #access_log  logs/host.access.log  main;

        location / {
           root   html;
           index  index.html index.htm;
        }

        #添加一个模块 访问图片
        location /images/{
           root /etc/nginx;
           autoindex on;
        }
}
    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}

```



