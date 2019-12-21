---
title: windows10专业版激活
date: 2019-05-03 22:41:46
tags: Windows10
categories: 技术
---

## 激活windows的原理

GitHub上的这个开源的[KMS模拟器项目](https://github.com/Wind4/vlmcsd)能够让你的服务器成为一台kms,所以只要你有一台服务器,在服务器上运行这个软件,即可用于激活Windows.

而你在服务器运行这个软件之后,需要激活的电脑只需要在命令行中输入几句激活的指令就可以连接到该服务器进行激活验证,**这个过程不需要安装任何不安全的软件,和网上的所谓破解激活软件完全不一样**.

如果你没有服务器,也可以用网上别人公开的服务器,理论上只要对方的服务器没有挂掉,你就可以一直激活使用,而且kms服务器只是一个激活服务器,并不能操作客户端,所以最多只是这个激活服务器不能用了,网上说的能用kms服务器控制你的电脑,那就是扯淡.

<!-- more -->

## 激活Windows10专业版

1. 以管理员身份运行powershell

2. 输入以下命令

   ```powershell
   slmgr.vbs -upk
   slmgr.vbs -ipk W269N-WFGWX-YVC9B-4J6C9-T83GX
   slmgr.vbs -skms 119.29.248.79
   slmgr.vbs -ato
   slmgr.vbs -dlv
   ```


## 激活Windows10企业版

1. 以管理员身份运行powershell

2. 输入以下命令

   ```powershell
   slmgr.vbs -upk
   slmgr.vbs -ipk NPPR9-FWDCX-D2C8J-H872K-2YT43
   slmgr.vbs -skms 119.29.248.79
   slmgr.vbs -ato
   slmgr.vbs -dlv
   ```