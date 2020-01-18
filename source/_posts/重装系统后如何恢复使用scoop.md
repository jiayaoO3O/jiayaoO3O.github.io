---
title: 重装系统后如何恢复使用scoop
date: 2019-03-19 17:30:49
tags: scoop
categories: 技术
---

重装系统之后,如果把原有的scoop文件夹粘贴回去user文件夹,然后在powershell中再次输入[windows下的软件管理神器:scoop](<https://jiayaoo3o.github.io/2019/01/30/windows%E4%B8%8B%E7%9A%84%E8%BD%AF%E4%BB%B6%E7%AE%A1%E7%90%86%E7%A5%9E%E5%99%A8-scoop/>)文章中的安装命令,会得到一个**Scoop is already installed**错误,要想正确恢复scoop,根据[官方回答](https://github.com/lukesampson/scoop/issues/2894),请按照以下步骤:

<!-- more -->

1. 重装系统之前,先完整复制用户目录下的scoop文件夹到别的地方

2. 重装系统之后,将scoop文件夹粘贴回去用户目录

3. 在环境变量设置中,新建一个用户变量,名字为SCOOP,值为当前scoop文件夹的地址,即:

   ```
   C:\Users\xxxx\scoop
   ```

4. 允许脚本执行:

   ```
   set-executionpolicy remotesigned -s currentuser
   ```

5. 双击用户变量中的path,新建一个路径,填入 :

   ```
   %SCOOP%\shims
   ```

6. 管理员权限powershell中运行:

   ```powershell
   scoop reset *
   ```

即可恢复所有软件的正常使用.



