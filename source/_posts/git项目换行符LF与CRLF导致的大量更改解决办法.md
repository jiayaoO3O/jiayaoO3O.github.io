---
title: git项目换行符LF与CRLF导致的大量更改解决办法
date: 2019-06-26 20:40:27
tags: git
categories: 技术
---
## 产生该问题的原因

产生该问题的原因是由于Windows平台和linux平台的默认换行符是不一样的,linux使用的是`0x0A(LF)`而Windows使用的是`0x0D0A(CRLF)`,这就导致了当Windows下的代码放到linux下运行时,虽然代码没有错,但是linux下的git检测到项目的换行符为CRLF时会自动换成LF.

<!-- more -->

## 该问题的症状

出现这个问题的症状表现为git会提示项目的每一个文件的所有位置都发生了修改,但是查看diff的时候发现其实哪都没修改,这是因为换行符被换了但是我们是看不出来的.

## 解决该问题的方法

由于代码多是运行在linux,所以现在主流的换行符标准就是LF,所以我们的项目一开始就应该有将换行符设置为LF的意识.

项目一开始创建,还没有加入git仓库的时候就应该将换行符设置为LF,vscode等工具都提供了这个简单的功能,

![img](https://i.loli.net/2019/06/09/5cfc7e70d69e995247.png)

如果项目已经加入了git仓库,那就让git帮我们解决问题,git有一个`autocrlf`配置,可以在我们提交时自动转换换行符,它有3个选项:

- **true:** 提交时转换为 LF，检出时转换为 CRLF
- **false:** 提交检出均不转换
- **input:** 提交时转换为LF，检出时不转换

另一个设置项`safecrlf`用于检查文件是否包含着混合换行符,也有3个选项:

- **true:** 拒绝提交包含混合换行符的文件
- **false:** 允许提交包含混合换行符的文件
- **warn:** 提交包含混合换行符的文件时给出警告

所以,如果我们要将已经加入git的大量CRLF结尾文件批量转换成LF结尾的文件,可以这样设置:

```shell
git config --global core.autocrlf input
git config --global core.safecrlf warn
```

这样设置之后,先将项目提交一次,这样所有的文件就都会被改成LF结尾.

