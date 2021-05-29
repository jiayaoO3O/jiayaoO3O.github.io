---
title: frp配置文件记录
date: 2021-05-27 09:50:00
tags: [frp]
categories: [技术]
---

## 下载frp

```shell
wget https://github.com/fatedier/frp/releases/download/v0.36.2/frp_0.36.2_linux_amd64.tar.gz
tar -zxvf ./*.tar.gz
```

## frpc.ini

```properties
[common]
server_addr = xx.xx.xx.xx
server_port = 7000
token=xxxxxxxxxx
login_fail_exit=false

[rdp]
type = tcp
local_ip = 127.0.0.1
local_port = 3389
remote_port = 3389
```

> login_fail_exit=false这个配置能够让程序即使链接失败也不会自动退出, 例如当设置frpc为开机启动, 但是刚开机电脑会没有网络这种情况, 如果不设置该配置项, 失败次数过多之后frpc会自动退出.

<!-- more -->

## frps_full.ini

```properties
dashboard_user = xxx
dashboard_pwd = xxxxx
token = xxxxx
allow_ports = 2000-50000

#[plugin.user-manager]
#addr = 127.0.0.1:9000
#path = /handler
#ops = Login

#[plugin.port-manager]
#addr = 127.0.0.1:9001
#path = /handler
#ops = NewProxy
```

下面两个插件如果用不上需要手动注释.

## 安装frp为systemd服务

编辑对应的service文件, 如果需要配置服务端, 则编辑frps.service, 如果需要配置客户端, 则编辑frpc.service

```shell
nano ./systemd/frpc.service
```

```properties
[Unit]
Description=Frp Client Service
After=network.target

[Service]
Type=simple
User=nobody
Restart=on-failure
RestartSec=5s
ExecStart=/usr/bin/frpc -c /etc/frp/frpc.ini
ExecReload=/usr/bin/frpc reload -c /etc/frp/frpc.ini

[Install]
WantedBy=multi-user.target
```

这里要将User=nobody改成对应的用户名.

移动执行文件frpc和配置文件frpc.ini到对应的目录

```shell
sudo cp ../frpc /usr/bin
sudo mkdir /etc/frp
sudo cp ../frpc.ini /etc/frp/frpc.ini
```

复制服务文件到systemd目录

```shell
sudo cp ./frpc.service /etc/systemd/system/
sudo systemctl daemon-reload
```

启动服务

```shell
sudo service frpc start
sudo service frpc status
```

如果能成功启动, 则将服务配置为开机启动

```shell
sudo systemctl enable frpc
```

