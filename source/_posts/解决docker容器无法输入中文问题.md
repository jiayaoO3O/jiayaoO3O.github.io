---
title: 解决docker容器无法输入中文问题
date: 2019-06-29 00:52:15
tags: docker
categories: 技术
---
一般情况下docker镜像产生的容器,都是无法直接在终端中输入中文的,因为docker镜像追求的就是最小化修改,没有必要的部分是不会进行添加与修改的.

虽然Debian和Ubuntu是爹和儿子,但是两者设置的方法略有不同.

总体的步骤就是:

1. 修改文件**/etc/locale.gen**,将需要的语言注释取消掉.
2. 运行**locale-gen**命令.

<!-- more -->

## Ubuntu

Ubuntu与debian不同的是Ubuntu仓库里有一个语言包,只要安装了语言包就会自动配置好`zh_CN.UTF-8`,所以我们要做的就是直接安装**language-pack-zh-hans**

在Dockerfile文件中添加以下命令,所有以RUN开头的命令,既可在原有的RUN指令之后添加,也可另起一行RUN,但必须在**apt-get update**命令之后:

```dockerfile
RUN apt-get -y install language-pack-zh-hans
```

```dockerfile
ENV LANG='zh_CN.utf8'
```

生成镜像后即可在终端输入中文.

## Debian

由于debian不仅没有语言包,连locales都没有安装,所以要先安装locales,然后通过**sed**将**locale.gen**文件的**zh_CN.UTF-8**前的注释去掉,并且运行locale-gen,安装zh_CN.UTF-8

在Dockerfile文件中添加以下命令,所有以RUN开头的命令,既可在原有的RUN指令之后添加,也可另起一行RUN,但必须在**apt-get update**命令之后:

```dockerfile
RUN apt-get install -y locales \
    && sed -i '/^#.* zh_CN.UTF-8 /s/^#//' /etc/locale.gen \
    && locale-gen 
```

```dockerfile
ENV LANG='zh_CN.utf8'
```

生成镜像后即可在终端输入中文.

