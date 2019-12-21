---
title: VSCode使用docker搭建hexo博客
date: 2019-06-29 00:49:03
tags: [docker,hexo]
categories: 技术
---
随着vscode推出remote-container容器开发模式,在docker里面搭hexo变得简单了起来,非常方便,现在来记录一下vscode使用docker搭建hexo的步骤.

首先要知道待解决的问题有哪些:

- vscode的remote-container模式好在哪.
- 如何使用vscode调用docker生成一个镜像.
- 生成的镜像会有哪些问题.
- 安装hexo需要哪些东西.
- 要对Dockerfile进行什么修改才能让生成的容器本身就安装好hexo.

<!-- more -->

## vscode的remote-container模式好在哪

优点有3个:

1. vscode的`remote-container`模式使得新手用户几乎不需要太多的手动干涉docker,即可开箱即用.只需安装对应的插件,并且安装了docker,即可成功地让vscode帮你完成大部分事情.
2. vscode还会将你本机的git配置与容器中的git进行关联,使得几乎可以直接在容器中使用`hexo d`命令将博客部署到GitHubPage而不需要任何设置.
3. 不管你去到哪台电脑,只要这台电脑的vscode安装了这个插件和docker,就能自动识别到`.devcontainer`文件夹中的内容,然后**自动帮你生成hexo镜像**.

## 如何使用vscode调用docker生成一个hexo容器

### 前提条件:

1. 安装了**vscode 1.35**以上版本.
2. 安装了`Remote Developmentm`插件.![](https://i.loli.net/2019/07/04/5d1dce8fb3c5d35149.png)

3. 电脑已经安装了docker.

### 步骤:

1. 使用vscode打开你想要创建hexo博客的文件夹(这个文件夹既可以是新创建的文件夹,又可以是你以前就已经用hexo写过博客的文件夹),例如我这里的**hexodocker**空文件夹.![](https://i.loli.net/2019/07/04/5d1dcf7a3fe2e53998.png)

2. 按F1,输入`remote`,在选项中选择`Open Folder in Container`,选择我们现在所在的这个hexodocker文件夹![](https://i.loli.net/2019/07/04/5d1dd01a72fce45611.png)

3. 下一步vscode会让你选择你所需要的基础镜像,由于我们知道hexo是基于node.js的所以要找到node.js镜像![](https://i.loli.net/2019/07/04/5d1dd0dacffe353417.png)

4. 选择完镜像,这时候vscode就会在你的这个目录下生成一个`.devcontainer`文件夹,这个文件夹有两个文件,一个是`devcontainer.json`,另一个是`Dockerfile`文件,vscode此时会依靠这两个文件,驱使docker生成新的镜像与容器.注意这时候生成镜像与容器所需的时间完全取决于你的电脑性能和网络性能,如果感觉vscode卡了很久都没用动,可以直接关闭vscode并且重新打开.![](https://i.loli.net/2019/07/04/5d1dd1c4e3c3670595.png)

5. 一切完成之后,可以看到自动生成的Dockerfile的内容,并且docker已经按照这个文件创建了镜像与容器,而且已经自动将这个容器与我们当前的文件夹进行了一个挂载.

   但是要注意的是,这个生成的镜像却并不是最终我们所需要的,因为这些内容只是vscode帮我们自动生成的,我们要对它进行修改.![](https://i.loli.net/2019/07/04/5d1dd578e2bca71189.png)

### 生成的镜像会有哪些问题

根据我爬过的坑,生成的镜像并不能直接使用,会有一些问题:

1. 容器内的默认时区是UTC,而中国的时区是CST,如果不把时区改过来,生成的文章时间是错误的.解决的方法请看我这一篇文章[docker容器设置时区](https://jiayaoo3o.github.io/2019/06/29/docker容器设置时区/).
2. 容器默认是没有对中文进行处理的,所以直接通过镜像生成的容器是无法输入中文的,那当我们使用`hexo new "文章标题"` 包含中文的时候,会无法输入.解决方法请看我的这一篇文章[解决docker容器无法输入中文问题](https://jiayaoo3o.github.io/2019/06/29/解决docker容器无法输入中文问题/).

### 安装hexo需要哪些东西

这里不会教你使用hexo的方方面面,这些东西去看网上的博客一搜一大把,我们只需要知道,安装hexo只需要在确保node.js环境正常的情况下输入

```shell
npm install -g hexo-cli 
npm install hexo -g
```

即可安装,所以我们也要在dockerfile中配置这两句.

### 要对Dockerfile进行什么修改

接下来直接贴上Dockerfile文件,注意看注释,并且仔细对比哪些是自动生成的,哪些是人为添加的.

```dockerfile
#-------------------------------------------------------------------------------------------------------------
# Copyright (c) Microsoft Corporation. All rights reserved.
# Licensed under the MIT License. See https://go.microsoft.com/fwlink/?linkid=2090316 for license information.
#-------------------------------------------------------------------------------------------------------------

FROM node:lts

# Configure apt
ENV DEBIAN_FRONTEND=noninteractive
RUN sed -i 's/deb.debian.org/mirrors.ustc.edu.cn/g' /etc/apt/sources.list \
    && apt-get update \
    && apt-get -y install --no-install-recommends apt-utils 2>&1 

# Verify git and needed tools are installed
# 这一部分直接解决了时区问题和中文输入问题
RUN apt-get install -y git procps locales \
    && sed -i '/^#.* zh_CN.UTF-8 /s/^#//' /etc/locale.gen \
    && locale-gen \
    && ln -fs /usr/share/zoneinfo/Asia/Shanghai /etc/localtime\
    && dpkg-reconfigure -f noninteractive tzdata

# Remove outdated yarn from /opt and install via package 
# so it can be easily updated via apt-get upgrade yarn
RUN rm -rf /opt/yarn-* \
    && rm -f /usr/local/bin/yarn \
    && rm -f /usr/local/bin/yarnpkg \
    && apt-get install -y curl apt-transport-https lsb-release \
    && curl -sS https://dl.yarnpkg.com/$(lsb_release -is | tr '[:upper:]' '[:lower:]')/pubkey.gpg | apt-key add - 2>/dev/null \
    && echo "deb https://dl.yarnpkg.com/$(lsb_release -is | tr '[:upper:]' '[:lower:]')/ stable main" | tee /etc/apt/sources.list.d/yarn.list \
    && apt-get update \
    && apt-get -y install --no-install-recommends yarn

# Install eslint
# 这一部分负责安装hexo
RUN npm install -g eslint \
    && npm install -g hexo-cli \
    && npm install hexo -g

# Clean up
RUN apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/* 

ENV DEBIAN_FRONTEND=dialog

# Set the default shell to bash instead of sh
ENV SHELL /bin/bash
# 这里负责设置环境变量
ENV LANG='zh_CN.utf8'
```

修改完成之后,我们要继续按F1,然后找到`Rebuild Container`重新生成新容器,这个时候就会得到一个完全可用的hexo容器.![](https://i.loli.net/2019/07/04/5d1dda0281bac67071.png)

## 创建博客

这里与平时创建博客的方法就完全一致了,只要在终端中输入`hexo init`即可,而如果你以前就已经在这个文件夹里面使用`hexo init`初始化过,那建议删除`node_modules`文件夹,然后再在终端中输入`npm install`命令安装所有需要的模块.