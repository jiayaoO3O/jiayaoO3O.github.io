---
title: linux安装docker
date: 2019-06-26 20:44:13
tags: docker
categories: 技术
---
## 不要直接安装apt仓库内的docker.io

apt仓库中的docker.io是很老版本的docker,现在新版的docker是直接叫做docker-ce,不要直接`sudo apt install docker.io`

如果你已经这样安装了docker.io,请输入以下命令卸载:

```shell
sudo apt-get remove docker docker-engine docker.io containerd runc
```

此时docker.io会被卸载,但是以前下载的images, containers,volumes都会得以保留.

<!-- more -->

## 通过仓库安装

1. 升级仓库:

   ```
sudo apt update
   ```
   
2. 通过apt安装以下软件使得可以通过https使用仓库:

   ```
sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
   ```
   
3. 添加Docker的官方GPG密钥：

   ```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
   ```
   
4. 使用下面命令添加稳定版仓库:

   ```
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
   ```
   
5. 开始安装docker-ce:

   ```
sudo apt update
   sudo apt install docker-ce docker-ce-cli containerd.io
   ```

## 通过deb包安装

1. 去[`https://download.docker.com/linux/ubuntu/dists/`](https://download.docker.com/linux/ubuntu/dists/)选择你的Ubuntu版本，在`pool/stable/`里找到合适你版本的deb包.

2. 通过下面命令直接安装deb包,注意找到自己下载deb包的目录和包名.

   ```shell
sudo dpkg -i /path/to/package.deb
   ```
   
3. 如果想要升级docker-ce,要再去下载新的deb包重复安装.

## 使用脚本安装

这个方法好处是非常的简单,一个脚本全部搞完,并且树莓派只能以这种方式安装docker.

```shell
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

以后更新只要在apt内更新即可,不需要重新运行脚本.

## 添加用户到docker组

如果不想每次运行docker都要在前面加入sudo,那可以将当前用户加入docker组:

```
sudo usermod -aG docker 用户名
```

注销,并且重新登录即可生效.

## 卸载docker-ce

```
sudo apt-get purge docker-ce
```

主机上的镜像,容器都不会自动删除,想要手动删除,可以输入:

```
sudo rm -rf /var/lib/docker
```