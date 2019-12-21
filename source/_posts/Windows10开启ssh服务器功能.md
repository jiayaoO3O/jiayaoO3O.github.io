---
title: Windows10开启ssh服务器功能
date: 2019-03-04 22:20:16
tags: [ssh,内网穿透]
categories: 技术
---

# 为什么要用ssh连接windows10

一般来说,想要远程连接并且使用windows10的命令行有两种方法:

1. 使用微软自带的远程桌面(RDP)或者其他第三方远程桌面控制软件(teamviewer等)远程控制windows10,然后在软件控制界面使用命令行.
2. 使用ssh连接到Windows10,然后使用命令行.

而如果电脑没有公网ip,可以参考上一篇文章 [Sakura frp--免费的高速内网穿透工具](https://jiayaoo3o.github.io/2019/03/01/Sakura-Frp-%E5%85%8D%E8%B4%B9%E7%9A%84%E9%AB%98%E9%80%9F%E5%86%85%E7%BD%91%E7%A9%BF%E9%80%8F%E5%B7%A5%E5%85%B7/)进行内网穿透,从而在外网访问本地电脑.

<!-- more -->

# 使用PowerShell安装OpenSSH

要使用PowerShell安装OpenSSH前，请首先以管理员身份启动PowerShell.

1. 在powershell中输入:
   ```powershell
   Get-WindowsCapability -Online | ? Name -like 'OpenSSH*'
   ```

   此时powershell应该会显示可返回的结果:

   ```powershell
   Name  : OpenSSH.Client~~~~0.0.1.0
   State : NotPresent
   Name  : OpenSSH.Server~~~~0.0.1.0
   State : NotPresent
   ```

2. 分别输入以下命令安装ssh客户端与ssh服务器端:

   ```powershell
   Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0
   ```

   ```powershell
   Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
   ```

   此时powershell会显示如下返回结果:

   ```powershell
   Path          :
   Online        : True
   RestartNeeded : False
   ```

# 使用powershell卸载openssh

输入以下两条命令即可卸载刚刚安装的ssh客户端与ssh服务器:

```powershell
Remove-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0
Remove-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
```

# 初始化ssh服务器

仍然以管理员身份启动powershell,输入以下命令:

```powershell
Start-Service sshd
# 以下命令可选,但是建议启用:
Set-Service -Name sshd -StartupType 'Automatic'
Get-NetFirewallRule -Name *ssh*
```

初始化完成后,电脑上就已经启用了ssh服务器了,其他电脑或者手机设备输入以下命令:

```
ssh -p 服务器端口 远程ssh服务器用户名@远程ssh服务器IP
```

就能用ssh连上该设备了,如果在外网则需要内网穿透之后才能连上.