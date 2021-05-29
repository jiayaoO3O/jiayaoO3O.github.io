---
title: Ubuntu配置samba
date: 2021-05-29 16:35:00
tags: [ubuntu,samba]
categories: [技术]
---

## 安装samba

```shell
sudo apt install samba
```

## 配置samba

```shell
sudo nano /etc/samba/smb.conf
```

```properties
[global]
read raw = Yes
write raw = Yes
socket options = TCP_NODELAY IPTOS_LOWDELAY SO_RCVBUF=131072 SO_SNDBUF=131072
min receivefile size = 16384
use sendfile = true
aio read size = 16384
aio write size = 16384

[sambashare]
comment = Samba on Ubuntu
path = /home/username/sambashare
read only = no
browsable = yes
```

```shell
sudo service smbd restart
```

## 设置用户名密码

```shell
sudo smbpasswd -a username
```