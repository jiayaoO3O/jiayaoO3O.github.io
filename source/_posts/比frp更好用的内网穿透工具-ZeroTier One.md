---
title: 比frp更好用的内网穿透工具-ZeroTier One
date: 2020-06-10 14.51.00
tags: 内网穿透
categories: 技术
---

曾经我觉得, 最好用的内网穿透工具是frp, 并且写了一篇文章来赞美免费提供8M带宽提供内网穿透的[Sakura Frp](https://jiayaoo3o.github.io/2019/03/01/Sakura-Frp-%E5%85%8D%E8%B4%B9%E7%9A%84%E9%AB%98%E9%80%9F%E5%86%85%E7%BD%91%E7%A9%BF%E9%80%8F%E5%B7%A5%E5%85%B7/), 但后来我发现, 哪怕是Sakura Frp免费提供了公网服务器, 使用起来还是有点繁琐了, 并且速度只有8M, 所以最终我找到了能够代替frp的内网穿透工具 : ZeroTier.

## ZeroTier One是什么?

ZeroTier One是一款内网穿透软件, 但是和frp的"内网客户端-远程公网服务器-内网客户端"模式不同的是, ZeroTier One是通过p2p方式在不同的设备之间进行连接的, 只要所有的设备都加入了同一个ZeroTier Network, 互相之间就可以像在局域网中那样访问对方, 并且是直接连接而不是走公网服务器, 速度上限基本取决与设备之间的网速而不是ZeroTier服务器.免费版的ZeroTier能够连接多达100各设备, 足够个人使用了.

<!-- more -->

## ZeroTier One使用步骤

### 下载并且安装ZeroTier One客户端

进入ZeroTier的[下载页面](https://www.zerotier.com/download/)点击对应系统的下载链接, 即可下载并且安装ZeroTier One客户端.

![](https://i.loli.net/2020/06/10/S2ZB1lIfvdTMHCL.png)

### 注册并且登陆ZeroTier

[点击链接](https://accounts.zerotier.com/auth/realms/zerotier/protocol/openid-connect/auth?client_id=zt-central&redirect_uri=https%3A%2F%2Fmy.zerotier.com%2Fapi%2F_auth%2Foidc%2Fcallback&response_type=code&scope=all&state=state)进入注册页面, 进行注册.

![](https://i.loli.net/2020/06/10/qPHpS3lTxMInJ6R.png)

### 创建一个Network

点击Networks标签, 再点击Create a Network按钮创建一个网络.

![](https://i.loli.net/2020/06/10/rahZWlA5Ii4bjLu.png)

### 配置Network基本信息

点击刚刚创建的网络, 可以进入到network配置页面, 配置的作用如下图.

![基本配置](https://i.loli.net/2020/06/10/9kjqKz6GnuXBwrH.png)

![网络设置](https://i.loli.net/2020/06/10/m1gksjfd4JLzTSw.png)

右键电脑上运行的ZeroTier One, 点击Join Network, 粘贴上面的Network ID.

![](https://i.loli.net/2020/06/10/XwoKkQpeIlO7aqx.png)

![成员管理](https://i.loli.net/2020/06/10/9b5kBj1ihzCRtgX.png)

当两台设备都安装ZeroTier One并且加入了同一个网络, 即可互相通信.

![](https://i.loli.net/2020/06/10/liIhXM6TAgJeyn8.png)

此时, 已经可以直接使用[远程桌面功能](https://jiayaoo3o.github.io/2019/04/09/%E5%BC%80%E5%90%AFwindows%E7%9A%84%E8%BF%9C%E7%A8%8B%E6%A1%8C%E9%9D%A2%E5%8A%9F%E8%83%BD/), 对局域网机器进行访问.

![](https://i.loli.net/2020/06/10/BSrL5VilUYnQauH.png)

## 加速ZeroTier One之间的连接(可选)

虽然两个ZeroTier One客户端之间是通过p2p连接的, 但是设备A要首先连接到ZeroTier的行星根服务器(地球 Earth), 去获取到设备B的信息, 才能在两者之间进行通信, 而ZeroTier的全球唯一行星根服务器有可能离我们很遥远, 导致在某些时候"A-根服务器-B"这一过程速度会很慢, 这个时候可以通过在自己的公网服务器中架设根服务器(月球 Moons), 来加快"A-根服务器-B"这一过程.

1. 执行下面的命令, 在公网服务器中安装ZeroTier 

   ```shell
   curl -s https://install.zerotier.com/ | sudo bash
   ```

2. 进入ZeroTier安装目录, 生成配置文件

   ```shell
   cd /var/lib/zerotier-one
   sudo zerotier-idtool initmoon identity.public > moon.json
   ```

3. 修改moon.json配置文件, 找到对应的那一行, 添加你的公网服务器的ip

   ```
   "stableEndpoints": [ "123.123.123.123/9993" ]
   ```

4. 生成文件

   ```shell
   sudo zerotier-idtool genmoon moon.json
   ```

   这个时候会在当前目录生成一个名字类似0000009b30156f58.moon的文件.

5. 创建名为moons.d的文件夹, 并且将上面的moon文件移动进去

   ```shell
   mv 0000009b30156f58.moon moons.d
   ```

6. 重启服务

   ```shell
   service zerotier-one restart
   ```

这个时候, 你的公网服务器就已经成为moon服务器了, 客户端想要加入moon, 只需要用管理员powershell执行下面命令, 即可看到设备已经添加了moon节点. 后面那段字符是moon文件生成的文件名去掉开头的6个0.

```shell
zerotier-cli orbit 9b30156f58 9b30156f58
zerotier-cli listpeers
```