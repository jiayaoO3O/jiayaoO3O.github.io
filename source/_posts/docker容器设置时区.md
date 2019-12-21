---
title: docker容器设置时区
date: 2019-06-29 01:17:15
tags: docker
categories: 技术
---
docker容器中的时区一般来说都是使用[UTC](https://zh.wikipedia.org/wiki/%E5%8D%8F%E8%B0%83%E4%B8%96%E7%95%8C%E6%97%B6),但是中国的时区是使用[CST](https://zh.wikipedia.org/wiki/CST),所以当使用某些需要依赖时间的服务时时间就会对不上,这时候就要修改容器的时区.

Debian和ubuntu类似但是稍微有点不同.

## Ubuntu

ubuntu镜像本身是没有安装一个叫tzdata的软件,这个软件可以帮我们设置时区,而且在首次安装后会自动帮我们设置已经指定了的时区,所以要做的有2步,在Dockerfile文件中添加以下命令,既可在原有的RUN指令之后添加,也可另起一行RUN,但必须在**apt-get update**命令之后:

<!-- more -->

1. 设置`/etc/localtime`文件的内容为我们需要的时区.

   ```dockerfile
   RUN ln -fs /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
   ```

2. 安装tzdata软件.这时候软件首次安装会自动帮我们设置第一步已经指定的时区.

   ```dockerfile
   RUN apt-get install -y tzdata 
   ```

生成镜像后可以在容器终端输入date查看时间.

## Debian

Debian与Ubuntu不同,它本身已经安装了tzdata软件,所以并不能像Ubuntu那样在首次安装之后自动帮我们设置时区,而要手动使用命令重新配置,步骤如下,在Dockerfile文件中添加以下命令,所有以RUN开头的命令,既可在原有的RUN指令之后添加,也可另起一行RUN.

1. 首先检查Dockerfile的起始部分中是否有`ENV DEBIAN_FRONTEND=noninteractive`这一句,这一句命令是设置环境变量`DEBIAN_FRONTEND`的值为`noninteractive`,作用是让Debian前端命令设置为非交互式,这种模式下设置dpkg不需要人为手动输入选择操作而是自动配置,如果没有这一句,请先在文件中添加这一句.

   ```dockerfile
   ENV DEBIAN_FRONTEND=noninteractive
   ```

2. 与ubuntu一样,设置/etc/localtime文件的内容为我们需要的时区.

   ```dockerfile
   RUN ln -fs /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
   ```

3. 通过非交互模式重新配置tzdata.

   ```dockerfile
   RUN dpkg-reconfigure -f noninteractive tzdata
   ```

4. 这个时候就已经设置完成了,但是要将前端命令从非交互式的重新设置为交互式的,所以要在Dockerfile的末尾添加一句命令:

   ```dockerfile
   ENV DEBIAN_FRONTEND=dialog
   ```

   