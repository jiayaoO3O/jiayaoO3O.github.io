---
title: Sakura frp--免费的高速内网穿透工具
date: 2019-03-01 11:24:46
tags: 内网穿透
categories: 技术
---

# 什么是Sakurafrp?

[Skaura frp](https://www.natfrp.org)是一款免费的,基于 [frp](https://github.com/fatedier/frp) 封装的可用于内网穿透的高性能的反向代理应用,通过frp,没有公网ip的用户也能够远程访问自己的电脑.

<!-- more -->

# Sakurafrp的优点

相对于传统的frp,Sakura frp有以下优点:

1. 最大的优点,Sakura frp免费提供了frp服务器给广大用户,使得用户不需要自己购买,维护公网服务器便能享受frp带来的便捷.
2. Sakura frp在传统的frp程序上进行封装,不需要用户配置frp客户端,只要输入用户名和密码,选择适合自己的服务器即可,做到快捷上手,简单易用,门槛降低.

# 使用Sakurafrp进行远程桌面控制

既然frp主打的功能是内网穿透,在这里就用windows的远程桌面来展示如何使用Sakura frp.

## 1.注册并登录Sakurafrp

首先先在[Sakurafrp](https://www.natfrp.org)官网注册账号并登录

![](https://i.loli.net/2019/03/01/5c7951fdd3276.png)

## 2.创建内网穿透映射

登录之后,选择左边的映射列表,创建一个新的映射

![](https://i.loli.net/2019/03/01/5c795275e01ad.png)

这里可以看到,Sakurafrp 已经内置了几种常用的内网穿透映射,这里点击远程桌面,注意远程端口要自己填一个数字,但是这个数字不能跟其他用户已经选择的数字冲突,所以可能要多试几个端口,直到成功为止.

## 3.下载Sakurafrp客户端

创建成功之后,点击左侧的客户软件选择适合自己的客户端进行[下载](https://s1.tcotp.cn/cdn/SakurafrpClient-2.3/Sakura_frpc_windows_amd64.exe),如果没有需求不需要下载图形版本.

![](https://i.loli.net/2019/03/01/5c79537c0ba50.png)

## 4.运行Sakurafrp客户端

运行Sakurafrp客户端的方法有3种:

1. 直接双击下载的Sakurafrp,输入账号和密码,再输入服务器ID,选择合适自己的服务器

![](https://i.loli.net/2019/03/01/5c79555656732.png)

就已经内网穿透成功了,其他电脑打开微软远程桌面,或者手机安装微软远程桌面app,电脑ip地址位置输入

```
所选服务器地址:远程端口号
```

![](https://i.loli.net/2019/03/02/5c795d2a2f194.png)

例如你选了台湾百兆服务器,远程端口是10010,则输入:

```
s21.natfrp.org:10010
```

账号密码则输入运行了Sakurafrp的,被控制的电脑的账号与密码,点击连接,手机或者其他电脑就可以远程控制这台电脑了.

这个方法适合临时使用Sakurafrp进行内网穿透,关闭软件窗口即断开连接服务器.

2. 在powershell等终端软件中打开Sakurafrp,这种方法可以免去每次手动输入账户名,密码,服务器ID.只要在Sakurafrp客户端所在的文件夹中,按住Shift键,选择打开powershell,然后在powershell中输入:

   ```
   ./Sakura_frpc_windows_amd64.exe --su="你的用户名" --sp="你的密码" --sid="服务器ID"
   ```

   即可自动登录并且选择服务器.

3. 开机自启动Sakurafrp.虽然上面两种方法可以使用,但是会在任务栏上显示出来,而且软件不能关闭,只要关闭就会断掉,所以第三种方法是将Sakurafrp放在后台,自动开机启动.

   首先在Sakurafrp所在的文件夹新建一个文本文件,命名为:

   ```
   Sakura_frpc.bat
   ```

   右键该文件,选择编辑,输入:

   ```powershell
   Sakura_frpc_windows_amd64.exe --su="你的用户名" --sp="你的密码" --sid="服务器ID"
   ```

   并且保存.

   再在Sakurafrp所在的文件夹新建一个文本文件,命名为:

   ```
   Sakura_frpc.vbs
   ```
   右键该文件,选择编辑,输入:

   ```vbscript
   set ws=WScript.CreateObject("WScript.Shell") 
   ws.Run "Sakura_frpc.bat /start",0
   ```

   并且保存.

   这时,我们只要双击这个Sakura_frpc.vbs,软件就会在后台启动了,在任务栏是看不到的,但是在任务管理器中能够看到.

   此时,我们右键Sakura_frpc.vbs,选择创建快捷方式

   然后键盘Win+R键,输入

   ```
   shell:startup
   ```

   打开开机启动文件夹,把快捷方式放进去.

   Sakurafrp就已经能够开机启动了.

# 总结

Sakurafrp是我昨天晚上无意中发现的一个项目,本来我用frp做内网穿透是用自己的腾讯云1M小水管用作公网服务器的,但是没想到Sakurafrp竟然这么良心,免费提供8M的服务器给别人使用,所以我觉得得写一个教程来感恩一下,感谢Sakurafrp,💉 💧 🐮 🍺

