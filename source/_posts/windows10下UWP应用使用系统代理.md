---
title: Windows10下UWP应用使用系统代理
date: 2019-06-28 11:08:12
tags: Windows10
categories: 技术
---
由于UWP本身运行在沙盒中,默认情况下是不能直接访问localhost的,所以也就不能直接访问本机的代理服务,但是Win10自身有一个叫CheckNetIsolation.exe 的命令行工具可以将uwp应用设置为使用代理.

## 单独指定某个UWP应用可以代理

1. 通过Win+S在搜索框输入"注册表"来打开**注册表编辑器**![](https://i.loli.net/2019/06/28/5d16280329cd127618.png)

2. 在上图的蓝色框处,输入下面的地址:

   ```
   HKEY_CURRENT_USER\Software\Classes\Local Settings\Software\Microsoft\Windows\CurrentVersion\AppContainer\Mappings
   ```

   就会定位到上图的位置,左边的的密密麻麻的都是电脑上UWP应用的SID值,而右边绿色框的DIsplayName就是你点击的某个uwp应用的名字,现在要做的就是慢慢按方向键下键,寻找你需要进行代理的uwp软件.

3. 找到需要代理的软件之后,复制该软件红色框处的SID,然后在CMD中输入以下命令:

   ```powershell
   CheckNetIsolation.exe loopbackexempt -a -p=UWP的SID
   ```

   就可以直接生效了.

4. 如果需要取消代理,恢复原样,在CMD中输入以下命令:

   ```powershell
   CheckNetIsolation.exe loopbackexempt -d -p=UWP的SID
   ```

   ## 对所有UWP应用设置可以代理

   从上面的方法可以知道,想要对某个uwp使用代理,就只要知道它的SID然后设置就可以了,现在想要对所有uwp进行设置,也就是只要知道所有uwp的SID.

   在CMD中输入以下命令即可对所有UWP进行设置:

   ```powershell
   FOR /F "tokens=11 delims=\" %p IN ('REG QUERY "HKCU\Software\Classes\Local Settings\Software\Microsoft\Windows\CurrentVersion\AppContainer\Mappings"') DO CheckNetIsolation.exe LoopbackExempt -a -p=%p
   ```

   