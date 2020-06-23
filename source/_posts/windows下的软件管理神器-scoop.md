---
title: 'Windows下的软件管理神器:scoop'
date: 2019-01-30 11:40:00
tags: [Windows10,包管理器,scoop]
categories: 技术
---

# scoop是什么

[Scoop](https://scoop.sh/)是一款Windows下的命令行软件管理工具,类似于ubuntu下的apt,它存在的意义,就是为了解决Windows下没有一个统一包管理器的痛点,有了scoop,在Windows下也能一条命令直接安装,升级,卸载,切换软件的不同版本等

<!-- more -->

------

# 安装scoop

scoop的使用与安装都要依赖PowerShell,所以电脑系统最好是Windows10的.安装方法很简单,以下命令都在PowerShell中输入:

```
set-executionpolicy remotesigned -s currentuser
```

会提示是否允许执行远程脚本,输入a同意全部.

```powershell
iex (new-object net.webclient).downloadstring('https://get.scoop.sh')
```

运行上面这条命令,scoop就会安装在你的C:\Users\ 用户名\scoop目录中,这时候在PowerShell中输入

```powershell
scoop help
```

就会显示出scoop的基本操作命令

![](https://i.loli.net/2019/01/31/5c528038a674b.png)

------

# 使用scoop

scoop的用法与一般的Linux软件类似,都是

```
scoop 操作 对象软件
```

## 查找软件

```
scoop search 软件名
```

例如:

```
scoop search vim
```

如果后面不跟软件名,直接输入

```
scoop search			
```

那scoop会直接显示仓库目前有的所有软件,可以一个一个慢慢看

## 安装软件

```
scoop install 软件名
```

例如:

```
scoop install vim
scoop install python
scoop install nodejs
```

即可安装vim

## 卸载软件

```
scoop uninstall 软件名
```

例如:

```
scoop uninstall vim
```

## 升级软件

如果想要查看今天仓库更新了哪些软件,输入:

```
scoop update
```

就会显示所有今天升级更新的软件

```
scoop update 软件名
```

即可更新对应软件

如果想要直接升级所有软件,输入:

```
scoop update *
```

即可升级所有软件

## 检查软件状态

如果想要知道自己有哪些软件可以升级,输入:

```
scoop status
```

如果想要查看scoop是否出现了问题,输入:

```
scoop checkup
```

## 清理缓存

每当scoop下载了软件之后,再卸载这个软件,当初下载的这个软件包文件还会存在,所以下一次安装就不用再下载了,想要清理缓存,可以输入:

```
scoop cache rm 软件名
```

想要清除所有缓存,可以输入:

```
scoop cache rm *
```

## 删除软件旧版本

每当scoop升级了软件之后,旧版本仍会保留在电脑上,以便于切换到旧版本,想要删除软件旧版本,输入

```
scoop cleanup 软件名
```

想要删除所有软件的所有旧版本,输入

```
scoop cleanup *
```

## 查看软件主页

如果你想了解仓库中的某一个软件,输入

```
scoop home 软件名
```

即可打开软件的主页

## 切换软件版本

如果同时安装了某个软件的不同大版本,例如python(默认的python是python3)和python27,或者同时安装了java8和java11,可以通过:

```
scoop reset 想要使用的版本名称
```

进行切换

------

# 添加额外的仓库bucket

scoop中的仓库被叫做bucket,安装好的scoop默认会带有一个主仓库,名字就叫Scoop,主仓库里面的软件,有很多,但是基本都是比较适合程序员的软件,更重要的一点是,要入选主仓库的软件,都是没有GUI的,也就是说都是命令行软件,而scoop官方还有一个第三方bucket,里面有更多的常用软件,所以我们要添加额外的仓库

## 查看可添加仓库

想要查看可添加仓库,输入:

```
scoop bucket known
```

即可显示仓库列表

![](https://i.loli.net/2019/01/31/5c529f03b26cc.png)

一般来说,比较有用的仓库如下,要添加仓库,输入:

```
scoop bucket add extras
scoop bucket add java
scoop bucket add jetbrains
```

添加了java库之后,就可以安装各个版本的jdk了,而且scoop会帮你自动设置好环境变量,真正做到了开箱即用,爽翻.

如果想要添加不在上述列表的非官方仓库的话,输入:

```
scoop bucket add 仓库名 仓库的github地址
```

例如:

```
scoop bucket add Ash258 'https://github.com/Ash258/scoop-Ash258.git'
```

即可添加非官方仓库

## 查看已经添加的仓库

```
scoop bucket list
```

## 删除已经添加的仓库

```
scoop bucket rm 仓库名
```

------

# Scoop的本地文件目录

上面已经说了,scoop是安装在用户目录下的,目录格式为:

![](https://i.loli.net/2019/01/31/5c52a47d6b568.png)

这里最值得称道的是persist文件夹,因为我们知道,scoop安装的所有软件都是安装在scoop文件夹下的,那如果软件升级了的话,以前旧软件的设置和数据是不是全部清除了呢?例如vscode上的设置和安装的插件,是不是升级了之后,都没有了呢?不是的,因为scoop会将各个软件的设置数据,例如vscode软件的data文件夹(vscode的设置和插件都放在data文件夹),单独放到persist文件夹中,在用软链接链接到软件所在的文件夹,这样,每次升级软件,persist文件夹的各个软件的设置数据都会保持不变,从而保存设置.

另一个需要注意的是,所有的对scoop操作都不要在文件夹中进行,因为有可能会破坏scoop的文件结构,想要对scoop进行任何操纵,都需要在命令行中进行

------

# scoop的优缺点分析

scoop作为一款Windows下的包管理器,意义是非凡的,尤其是对于程序员而言,它极大地解决了程序员安装各种开发环境的麻烦,一键自动设置环境变量,包括:

- dotnet core
- java
- python
- nodejs
- go
- kotlin
- ruby

各种软件,包括:

- idea
- vscode
- vim
- git
- pycharm
- maven
- make
- gcc
- gdb
- mysql
- gradle
- android-sdk

等,可以说,scoop真的是每一个程序员的必备神器

## scoop的优点

1. 干净,不会污染系统,保持最高度清洁.scoop安装的所有软件都会放在用户目录的scoop文件夹下
2. 简单,一句命令即可安装软件,开箱即用,不用再去各个软件的官网下载软件
3. 省心,升级软件及时,软件数量多,解决问题快

## scoop的缺点

虽然scoop这么强大,但是它还是有缺点的

1. 网络问题,因为scoop是外国人发明的,而且scoop里面的软件有一些也是外国软件,并且scoop的每一个软件下载地址都是软件的官网,所以有时候必须要科学上网才能下载,并且下载速度也不稳定,有一些软件很快,有一些软件却很慢
2. 安装问题,scoop的安装会有一个问题,就是如果你安装某个软件失败了,再安装一次,这时候scoop会提示你软件已经安装过了,但是安装失败又用不了,所以这个时候要先执行一次scoop uninstall卸载刚刚失败的软件,再安装

------

# 总结

scoop可以说是我2018年遇到的最好用,对我帮助最大的软件,我第一次用这个软件的时候,马上拿出信用卡进行捐赠了,希望你也能喜欢这一款神器,💉 💧 🐮 🍺