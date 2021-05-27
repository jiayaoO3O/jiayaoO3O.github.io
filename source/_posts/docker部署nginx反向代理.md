---
title: docker部署nginx反向代理
date: 2021-05-27 10:05:00
tags: [docker,nginx]
categories: [技术]
---

## 创建nginx.conf配置文件

通过nginx官方镜像创建nginx.conf模板, 然后在该模板上进行修改即可

```
user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log notice;
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

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;

    server{
        server_name 被访问的域名;
        listen 8080;
        location / {
            proxy_pass http://target-server;
        }
    }

    upstream target-server{
        server xx.xx.xx.xx:xxxx;
    }
}
```

## 运行nginx

```shell
docker run --name nginx -p 80:8080 -v /host/path/nginx.conf:/etc/nginx/nginx.conf:ro -d nginx:stable
```

注意这里最关键的是对端口的映射, 观察这个命令, 并不是直接将host的80端口和容器的80端口进行映射, 而是先将host的80和nginx的8080进行绑定, 然后nginx再对8080端口进行监听然后反向代理.

这么做的原因是默认普通权限下容器内nginx是不能够监听1000以下的端口的, 需要提升权限,  但是通过80->8080的映射转换, 就可以正常启动了.

