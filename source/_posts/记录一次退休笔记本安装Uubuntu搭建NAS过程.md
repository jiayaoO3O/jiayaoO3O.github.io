---
title: 记录一次退休笔记本安装Uubuntu搭建NAS过程
date: 2020-06-16 09:37:00
tags: [Ubuntu,docker]
categories: 技术
---

## 笔记本配置

- 型号 : 坠机堡垒
- 服役时长 : 5年
- cpu : i5-4200h
- 内存 : 8+4的ddr3内存
- 硬盘 : 240G用了4年的威刚SATA固态+1T机械硬盘用于存电影
- 屏幕 : 45%NTSC祖传普通15.6屏幕

换了新电脑, 这台老机器就决定用来当NAS用了.

<!-- more -->

## 安装Ubuntu 20.04桌面版

前往Ubuntu官网下载Ubuntu 20.04桌面版, 因为这次是安装在笔记本电脑上, 有屏幕, 如果直接装服务器版本就有点浪费了, 安装有图形界面的Linux发行版都比较简单, 而且Ubuntu的汉化工作也比较到位, 就不多介绍了.

## 更换中科大源

进入[中科大镜像站官网](http://mirrors.ustc.edu.cn/), 找到Ubuntu选项, 点击右边的help按钮就可以进入Ubuntu换源帮助页面.

首先先执行下方命令, 查看当前的源网址前面有没有country-code

```shell
cat /etc/apt/sources.list
```

如果看到了`cn.archive.ubuntu.com`则执行下方命令 :

```shell
sudo sed -i 's/cn.archive.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list
```

如果只看到了`archive.ubuntu.com`则执行下方命令(少了一个cn) :

```shell
sudo sed -i 's/archive.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list
```

然后就可以执行下方命令进行源更新 :

```shell
sudo apt update
```

更新所有软件 :

```shell
sudo apt upgrade -y
```

## 取消笔记本Ubuntu的盒盖挂起功能

默认情况下, 使用笔记本安装Ubuntu, 电脑盒盖时整个系统会被挂起, 这个功能相当的扯淡, 执行以下操作可以进行修改 :

```shell
sudo nano /etc/systemd/logind.conf
#HandleLidSwitch=suspend
#找到上面的选项, 修改成下面的内容, 注意要删掉前面的#号
HandleLidSwitch=ignore
```

保存之后执行下方命令重启服务 :

```shell
sudo service systemd-logind restart
```

## 开启远程ssh连接

Ubuntu20.04默认没有安装sshd, 这个时候只要在终端输入sshd, 系统会提示没有安装sshd, 是否需要安装, 并且连命令都已经提示出来了, 执行下方命令安装sshd :

```shell
sudo apt install openssh-server -y
```

安装到这一步, 就可以不在这台笔记本上面操作了, 可以用另一台电脑远程连接到这台电脑上面.

## 安装docker

因为笔记本电脑是x86架构的, 会比树莓派, 路由器等非x86架构的设备拥有更多的docker镜像, 搭建在NAS上面的服务都是使用docker而不是直接安装到本地.

因为docker本身对Ubuntu的兼容性就足够好, 官方脚本里面已经默认兼容Ubuntu, 只要执行下方命令即可安装docker :

```shell
sudo apt install curl
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

安装完成之后, 执行下方命令, 将你的所在用户加入到docker组, 这样运行docker命令就不用每次都使用sudo :

```shell
sudo usermod -aG docker 你的用户名
```

然后重启即可使用docker.

## 部署Portainer服务

Portainer是一个可视化的docker容器管理平台, 使用Portainer可以很方便地对docker容器进行各种操作, 部署Portainer也很简单, 两条命令即可 :

```shell
docker volume create portainer_data #创建一个portainer_data卷, 由于portainer本身存储的东西不是很重要, 哪怕没了也没关系所以就懒得本地化挂载了.
docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
```

部署完成进入设备的IP : 9000端口即可访问Portainer.

## 部署adguardhome服务

adguardhome是一款用于去除广告的开源DNS服务软件, 用户可以在自己的设备上搭建DNS服务用于去除广告和加速访问等等, 在[adguardhome](https://hub.docker.com/r/adguard/adguardhome)的docker hub主页可以看到它的详细信息.

使用下方命令就可以直接拉取adguardhome镜像 :

```shell
docker pull adguard/adguardhome
```

需要注意的是, 现在还不能直接使用adguardhome提供的容器启动, 因为目前还有2个问题没有解决 :

- DNS服务器需要使用53端口, 但是Ubuntu自身的53端口已经被systemd-resolved占用了, 使用`sudo lsof -i:53`命令可以看到systemd-resolved正在运行中.
- adguardhome也需要使用68端口, 但是Ubuntu自身的DHCP会占用68端口, 不过由于我们不使用adguardhome的DHCP功能, 所以可以不用管, 换成映射成69端口即可

要解决第一个问题, 其实adguardhome的docker hub主页就有提示要如何操作 :

![](https://i.loli.net/2020/06/16/9LDSjlIrdJhMRE8.png)

简单来说, 就是 : 

```shell
sudo mkdir /etc/systemd/resolved.conf.d #如果没有这个文件夹就创建一个
sudo touch /etc/systemd/resolved.conf.d/adguardhome.conf #创建一个文件
#写入下方内容并且保存
[Resolve]
DNS=127.0.0.1
DNSStubListener=no
#对/etc/resolv.conf进行备份
mv /etc/resolv.conf /etc/resolv.conf.backup
#创建一个链接
ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf
#重启systemd-resolved
sudo systemctl restart systemd-resolved
```

搞完这套操作, 再执行`sudo lsof -i:53`就看不到占用了.

此时执行下方命令, 即可启动adguardhome容器(注意68端口已经被换成69端口) :

```shell
docker run --name adguardhome -v /home/jiayao/Docker/adguardhome/work:/opt/adguardhome/work -v /home/jiayao/Docker/adguardhome/conf:/opt/adguardhome/conf -p 53:53/tcp -p 53:53/udp -p 67:67/udp -p 69:68/tcp -p 69:68/udp -p 80:80/tcp -p 443:443/tcp -p 853:853/tcp -p 3000:3000/tcp -d adguard/adguardhome
```

如果没有报错, 就可以打开浏览器, 输入该设备的ip : 3000即可进入配置页面, 配置完成之后输入ip : 80端口即可访问adguardhome页面.

使用adguardhome最重要的就是添加DNS过滤器, adguardhome就是根据这些过滤器来屏蔽广告和加速网页访问的, 分享一些我在使用的配置 :

> url: https://adguardteam.github.io/AdGuardSDNSFilter/Filters/filter.txt
> name: AdGuard Simplified Domain Names filter
> url: https://cdn.jsdelivr.net/gh/neoFelhz/neohosts@gh-pages/127.0.0.1/full/hosts
> name: neohosts
> url: http://git.oschina.net/halflife/list/raw/master/ad.txt
> name: My AdFilters
> url: https://easylist-downloads.adblockplus.org/easyprivacy.txt
> name: EasyPrivacy
> url: https://raw.githubusercontent.com/jdlingyu/ad-wars/master/hosts
> name: ad-wars
> url: https://raw.githubusercontent.com/googlehosts/hosts/master/hosts-files/hosts
> name: googlehosts
> url: https://filters.adtidy.org/android/filters/11_optimized.txt
> name: AdGuard Mobile Ads filter (Optimized)
> url: https://filters.adtidy.org/android/filters/3_optimized.txt
> name: AdGuard Tracking Protection filter (Optimized)

到这里adguardhome算是装完了.

## 部署aria2服务

对我来说搭建NAS最重要的功能就是aria2了, 依然使用docker搭建aria2服务.

docker hub上面关于aria2的镜像有很多, 我选择了[superng6/aria2](https://hub.docker.com/r/superng6/aria2), 输入下方命令即可拉取aria2镜像 :

```shell
docker pull superng6/aria2
```

先执行`id 你的用户名`查看一下PUID和PGID :

```
uid=1000(jiayao) gid=1000(jiayao) 组=1000(jiayao),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),120(lpadmin),131(lxd),132(sambashare),998(docker)
```

使用下方命令即可启动容器 :

```shell
docker run --name=aria2 -d -v /home/jiayao/Videos:/downloads -v /home/jiayao/Docker/aria2:/config -e TZ=Asia/Shanghai -e PUID=1000 -e PGID=1000 -e SECRET=.... -e CACHE=1024M -e UpdateTracker=true -e MOVE=true -e MOVE=dmof -e SMD=true -p 6800:6800 -p 6881:6881 -p 6881:6881/udp --restart=always superng6/aria2
```

## 部署jellyfin服务

jellyfin是一个开源的媒体系统, 在国内因为没啥在线流式服务可以使用, 所以它最常用的功能就是播放NAS上面的视频, 只要部署了jellyfin, 就可以直接在网页上面播放视频.

想要部署jellyfin很简单, 首先执行下方命令拉取镜像 :

```shell
docker pull jellyfin/jellyfin:latest
```

然后在本地创建两个文件夹config和cache

```shell
 mkdir -p jellyfin/{config,cache}
```

然后就可以开始运行启动一个jellyfin容器 :

```shell
docker run -d --name=jellyfin -v /home/jiayao/Docker/jellyfin/config:/config -v /home/jiayao/Docker/jellyfin/cache:/cache -v /home/jiayao/Videos:/media -p 4000:8096 jellyfin/jellyfin:latest
```

访问4000端口即可进入jellyfin页面.

## 安装ZeroTier One

ZeroTier One是一款免费的内网穿透软件, 安装之后就可以远程访问NAS里面的视频内容, 并且免费版可以让多达一百台设备加入同一网络, 具体部署可以参考我[这一篇文章](https://jiayaoo3o.github.io/2020/06/11/%E6%AF%94frp%E6%9B%B4%E5%A5%BD%E7%94%A8%E7%9A%84%E5%86%85%E7%BD%91%E7%A9%BF%E9%80%8F%E5%B7%A5%E5%85%B7-ZeroTier%20One/), 可惜我的腾讯云服务器的学生优惠到期了, 1元/月的服务器没得用了, 所以也没法部署moon节点了, 可惜可惜.

## 部署vlmcsd服务

vlmcsd是一个开源的kms服务, 本来是挂在腾讯云上面的, 结果上面说了腾讯云到期了, 也顺便部署到NAS上面吧.

还是使用docker, 我使用的是[mikolatero/vlmcsd](https://hub.docker.com/r/mikolatero/vlmcsd)镜像, 使用下面命令可以拉取镜像 :

```shell
docker pull mikolatero/vlmcsd
```

部署非常简单, 只要暴露1688端口即可 :

```shell
docker run -d -p 1688:1688 --restart=always --name vlmcsd mikolatero/vlmcsd
```

## 部署人人影视rrshareweb服务

人人影视官方有一个linux版本的客户端, 虽然感觉不怎么更新了, 但是无广告, 功能还是非常好用的, 有人做成了docker版本, 我也选择部署到NAS上.

使用[liubing/rrshareweb](https://hub.docker.com/r/liubing/rrshareweb)的版本, 其实docker hub上面的rrhsareweb镜像都大同小异, 都可以.

```shell
docker pull liubing/rrshareweb
```

```shell
docker run --name rrshare -d -p 3001:3001 -v /home/jiayao/Videos:/opt/work/store liubing/rrshareweb
```

> 搭建完发现官方已经废弃这个客户端了, 登陆上去啥都看不到, 也搜索不出来数据, 可惜了.

## 配置硬盘开机自动挂载

因为系统是安装在240G的固态上的, 而电影都是放在1T固态硬盘上的, 所以需要让硬盘开机自动挂载, 但是惊喜的是, Ubuntu 20.04桌面版附带的"磁盘"软件是内置挂载磁盘管理功能的, 只要鼠标点击设置即可配置硬盘开机挂载, 操作如下图所示 :



## 部署samba服务

作为NAS, 肯定是要让我的另一台笔记本电脑能够访问地访问到, 所以, 所以而win下的共享就是用smb协议的, 所以需要在Ubuntu上安装samba服务, 另一个惊喜的是Ubuntu 20.04桌面版的文件管理器也能够通过右键文件夹设置共享, 第一次打开共享功能会提示让你安装samba, 后面只需要输入以下命令添加samba用户即可 :

```shell
smbpasswd -a jiayao # 这里的用户得是linux里面有的用户 
New SMB password:  
Retype new SMB password:
sudo service smbd restart # 重启smbd服务
```

配置完用户之后, 剩下的就不用配置了, 让文件管理器帮你配置即可, 只需要找到你想要共享的文件夹, 右键共享即可.