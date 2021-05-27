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

