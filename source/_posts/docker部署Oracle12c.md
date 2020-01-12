---
title: docker部署Oracle12c
date: 2020-01-12 22:38:00
tags: [docker,Oracle]
categories: 技术
---

## 本机安装docker

安装过程省略 , 注意docker for windows 是依赖win10的hyper-v功能的,而hyper-v的虚拟化技术目前与VMware并不兼容 , 所以使用了VMware安装虚拟机却想要使用docker for windows , 有可能需要卸载VMware.

## 卸载原本的Oracle数据库

参考下列文章 :

[Oracle 11g数据库安装和卸载教程](https://zhuanlan.zhihu.com/p/34256436)

1. 开始－>设置－>控制面板－>管理工具－>服务 停止所有Oracle服务.

2. 开始－>程序－>Oracle - OraDb11g_home1－>Oracle安装产品－> Universal Installer 卸装所有Oracle产品.

3. 运行regedit,删除下列所有注册表内容 :

   ```
    HKEY_LOCAL_MACHINE\\SOFTWARE\\ORACLE
    HKEY_LOCAL_MACHINE\\SYSTEM\\ControlSet001\\Services\\ 中所有与Oracle有关的内容
    HKEY_LOCAL_MACHINE\\SYSTEM\\ControlSet002\\Services\\ 中所有与Oracle有关的内容
    HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\ 中所有与Oracle有关的内容
    HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\Eventlog\\Application\\ 中所有与Oracle有关的内容
   ```

4. 开始－>设置－>控制面板－>系统－>高级－>环境变量 删除环境变量CLASSPATH和PATH中有关Oracle的设定.

5. 重新启动计算机.

## 拉取Oracle12c的docker镜像

从此github仓库中拉取Oracle的docker镜像 :

[MaksymBilenko/docker-oracle-12c](https://github.com/MaksymBilenko/docker-oracle-12c)

```shell
docker pull quay.io/maksymbilenko/oracle-12c
```

拉取镜像 , 此镜像大小为3.6G , 中间不可以断点续传 , 所以拉取前请确定网络通畅.

拉取结束后 , 输入以下命令确认已经拉取成功 : 

```shell
docker images
```

> quay.io/maksymbilenko/oracle-12c                       latest              bdc272e2d9c9        5 months ago        8GB

## 启动Oracle12c镜像

执行下列命令即可启动容器 :

```shell
docker run --name oracle12 --restart=always -d -p 8079:8080 -p 1521:1521 quay.io/maksymbilenko/oracle-12c
```

此命令会启动一个Oracle12c镜像 , 并且将数据库的8080端口与本机的8079映射 , 不用本机的8080端口是因为默认的8080端口需要留给tomcat.

如果本机的1521已经被占用 , 也可以选择将容器的1521端口映射到其他端口.

添加--restart=always参数可使每次重启电脑导致docker重启时 , Oracle12容器也重新启动.

如果创建时忘记加入--restart=always , 对于已经存在的容器 , 可以使用下面命令 , 使之能够自动重新启动 :

```shell
docker update --restart=always oracle12
```

如果想要保留数据库的内容到本地目录 , 可以执行下列命令 :

```shell
docker run --name oracle12 --restart=always -d -p 8079:8080 -p 1521:1521 -v /my/oracle/data:/u01/app/oracle quay.io/maksymbilenko/oracle-12c
```

将上述的/my/oracle/data更改为你想要保留数据库的本地目录.

启动数据库之后 , 输入下列命令可以查看Oracle 12的容器创建日志 , 如果没有error出现 , 即说明创建成功

```sql
docker logs -f oracle12

Database not initialized. Initializing database.
Starting tnslsnr
[WARNING] [DBT-11209] Current available physical memory is less than the required physical memory (2,048MB) for creating the database.
[WARNING] [DBT-11217] Unable to check available shared memory on specified node(s) (b68f267b42c8).
[WARNING] [DBT-06208] The 'SYS' password entered does not conform to the Oracle recommended standards.
CAUSE:
a. Oracle recommends that the password entered should be at least 8 characters in length, contain at least 1 uppercase character, 1 lower case character and 1 digit [0-9].
b.The password entered is a keyword that Oracle does not recommend to be used as password
ACTION: Specify a strong password. If required refer Oracle documentation for guidelines.
[WARNING] [DBT-06208] The 'SYSTEM' password entered does not conform to the Oracle recommended standards.
CAUSE:
a. Oracle recommends that the password entered should be at least 8 characters in length, contain at least 1 uppercase character, 1 lower case character and 1 digit [0-9].
b.The password entered is a keyword that Oracle does not recommend to be used as password
ACTION: Specify a strong password. If required refer Oracle documentation for guidelines.
Copying database files
1% complete
2% complete
18% complete
33% complete
Creating and starting Oracle instance
35% complete
40% complete
44% complete
49% complete
50% complete
53% complete
55% complete
Completing Database Creation
56% complete
57% complete
58% complete
62% complete
65% complete
66% complete
Executing Post Configuration Actions
100% complete
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/xe/xe.log" for further details.
Configuring Apex console
Database initialized. Please visit http://#containeer:8080/em http://#containeer:8080/apex for extra configuration if needed
Starting web management console
PL/SQL procedure successfully completed.
Starting import from '/docker-entrypoint-initdb.d':
Import finished
Database ready to use. Enjoy! ;)
ls: cannot access /docker-entrypoint-initdb.d/*: No such file or directory
```

启动需要一段时间 , 只要没有error , 都是正常现象 , 上述日志输出完成之后 , 键入Ctrl+c退出日志.

此时Oracle12c就已经创建完成 , 下面是默认的配置信息 :

```
hostname: localhost
port: 1521
sid: xe
service name: xe
username: system
password: oracle
```

使用任意可视化数据库管理工具 , 输入上述信息即可成功连接安装sqlplus等工具

如果本地此时已经不存在Oracle数据库 , 运行sql脚本时需要用到sqlplus等工具,需要单独下载sqlplus等.

进入页面下载并解压下列三个文件到同一目录 :

[Instant Client for Microsoft Windows (x64) 64-bit](https://www.oracle.com/database/technologies/instant-client/winx64-64-downloads.html)

![Untitled _1_.png](https://i.loli.net/2020/01/12/4L5wXhUnZ6Qy1kI.png)

![Untitled.png](https://i.loli.net/2020/01/12/UIVj2FXTCetY3k1.png)

![Untitled _2_.png](https://i.loli.net/2020/01/12/p7uDmBvqjdKP4Mi.png)

这些文件都是免安装的 , 所以只需在环境变量Path中设置该地址即可 :

![Untitled _3_.png](https://i.loli.net/2020/01/12/LYpnoesHA7URDgm.png)

## 创建tnsnames.ora文件解决ORA-12154: TNS:could not resolve the connect identifier specified

假如已经删除了本地的oracle

若执行指定ip地址和端口的连接命令能够成功 , 例如 :

```
sqlplus user/password@localhost:1521/xe
```

但是执行不指定ip地址和端口的连接命令 , 报错ORA-12154: TNS:could not resolve the connect identifier specified

```
sqlplus user/password@xe
```

在安装sqlplus的目录中创建一个名为tnsnames.ora文件 , 粘贴下面内容 :

```
LISTENER_ORCL =
  (ADDRESS = (PROTOCOL = TCP)(HOST = localhost)(PORT = 1521))

xe =
  (DESCRIPTION =
    (ADDRESS = (PROTOCOL = TCP)(HOST = localhost)(PORT = 1521))
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = xe)
    )
  )
```

注意端口要与自身设置一致.

在环境变量中添加变量TNS_ADMIN , 值为该文件所在目录 :

![Untitled _4_.png](https://i.loli.net/2020/01/12/nde2MIVhjkOQF4C.png)

此时不指定数据库ip和端口也能连接到对应数据库.