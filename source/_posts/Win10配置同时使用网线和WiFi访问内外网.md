---
title: Win10配置同时使用网线和WiFi访问内外网
date: 2020-07-13 17:25:00
tags: Win10
categories: 技术
---

## 使用场景

公司内网服务器不能连外网, 或者网速很慢, 但是平时开发又有使用外网的需求, 为了不想要频繁地切换网络, 需要对网络进行配置, 使得平时访问外网可以使用WiFi热点, 而内网的服务器或者内网网站又能通过网线直接访问.

<!-- more -->

## 设置步骤

### 配置两个网络的接口跃点数

如果同时连接了网线与WiFi, 那Win10会默认所有流量都走网线的, 因为它认为网线一般来说速度会比WiFi快. 系统默认情况下, 会为每一个网络自动分配一个[接口跃点数](https://support.microsoft.com/zh-cn/help/299540/an-explanation-of-the-automatic-metric-feature-for-ipv4-routes), 自动选择跃点数比较小的进行网络访问, 所以第一步就是要将WiFi的自动跃点数调小, 而有线网络的跃点数调大, 先实现所有网络都走WiFi外网的功能, 配置步骤如下 :

1.  右键电脑右下角的网络图标, 选择**打开网络和Internet设置**选项.
2. 点击**更改适配器选项**.
3. 找到已连接网线的**以太网**, 右键选择属性.
4. 点击**Internet协议版本4**, 选择属性.
5. 点击右下角**高级**.
6. 取消勾选**自动跃点**选项, 然后手动在**接口跃点数**中填入64.
7. 再回到更改适配器选项页面, 找到WLAN网络设备, 使用同样的步骤, 在接口跃点数中填入32.

完成上述操作之后就实现了第一步, 右下角的图标应该就会变成WiFi, 并且访问网络时流量都走WiFi.

### 在hosts中配置内网网站的ip地址(可选, 如果你内网不需要访问内网网站可以不配置)

第二步指定需要访问的内网网站的ip, 是因为接下来的第三步需要指定某个ip走某个网络接口, 而不能指定网站, 所以需要在hosts中指定网站的ip, 这样我们在浏览器中访问该网站是, 系统会自动读取hosts中的ip地址, 从而走指定的网络接口.

先在命令行中输入下方ping命令, 可以看到你需要访问的网站的ip地址 :

```
ping 你需要配置的内网网址,例如我这里输入百度,得到的163.177.151.109就是对应的ip
PS C:\Users\jiayao> ping www.baidu.com

Pinging www.a.shifen.com [163.177.151.109] with 32 bytes of data:
Reply from 163.177.151.109: bytes=32 time=36ms TTL=53
Reply from 163.177.151.109: bytes=32 time=42ms TTL=53
Reply from 163.177.151.109: bytes=32 time=26ms TTL=53
Reply from 163.177.151.109: bytes=32 time=24ms TTL=53
```

得到了对应ip之后, 进入**C:\Windows\System32\drivers\etc**目录, 打开hosts文件, 输入网址和ip :

```
# Copyright (c) 1993-2009 Microsoft Corp.
#
# This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
#
# This file contains the mappings of IP addresses to host names. Each
# entry should be kept on an individual line. The IP address should
# be placed in the first column followed by the corresponding host name.
# The IP address and the host name should be separated by at least one
# space.
#
# Additionally, comments (such as these) may be inserted on individual
# lines or following the machine name denoted by a '#' symbol.
#
# For example:
#
#      102.54.94.97     rhino.acme.com          # source server
#       38.25.63.10     x.acme.com              # x client host

# localhost name resolution is handled within DNS itself.
#	127.0.0.1       localhost
#	::1             localhost

www.baidu.com	163.177.151.109
```

然后保存即可, 如果发现无法保存, 那就另存为新的文件放到桌面, 再将新文件拖入到原始目录覆盖即可.

### 配置系统的路由转发规则

使用管理员权限打开cmd或者powershell, 输入**route print**可以看到当前系统的路由信息, 输入下列命令即可指定对应的ip使用对应的网络接口 :

```
route add 你需要访问的ip mask 有线网络子网掩码 有线网络的网关 
```

例如 :

```
route add xx.xx.xx.xx mask 255.255.255.0 192.168.101.1 
```

这样当访问xx.xx.xx.xx时系统会自动通过192.168.101.1网关进行访问, 而其他的网络访问不受影响, 这个时候可以尝试访问内网服务器是否成功, 然后尝试访问外网网站试试能否正常访问, 如果没问题那就结束了.