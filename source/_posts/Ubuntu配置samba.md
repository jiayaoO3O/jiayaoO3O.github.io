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