---
title: windows10安装MySQL
date: 2019-04-24 17:18:14
tags: MySQL
categories: 技术
---

## 安装MySQL

使用[scoop](https://jiayaoo3o.github.io/2019/01/30/windows%E4%B8%8B%E7%9A%84%E8%BD%AF%E4%BB%B6%E7%AE%A1%E7%90%86%E7%A5%9E%E5%99%A8-scoop/)直接安装mysql:

```powershell
scoop install mysql
```

scoop会自动帮我们设置好环境变量.

<!-- more -->

## 配置MySQL

编辑mysql安装目录下的my.ini文件,对应位置添加如下数据:

```
[mysqld]
basedir=C:/Users/jiayao/scoop/apps/mysql/current
default-time-zone = '+8:00'
```

因为scoop已经自动配置好了mysql,所以直接使用启动命令启动mysql即可:

```
net start mysql
```

登录数据库:

```mysql
mysql -u root -p
```

不用输入密码直接回车,即可进入数据库.

```
Enter password:
```

进入数据库之后,执行以下命令修改用户密码:

```mysql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '新密码';
```

刷新用户权限:

```mysql
FLUSH PRIVILEGES;
```

关闭数据库,再重新启动:

```
net stop mysql
```

```
net start mysql
```

输入用户名和密码:

```
mysql -u root -p
```

即可连上数据库.