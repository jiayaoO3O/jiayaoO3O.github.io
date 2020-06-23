---
title: 使用mklink命令软连接Onedrive同步任意文件夹
date: 2019-12-20 23:47:34
tags: [Windows10,Onedrive]
categories: 技术
---

一般来说,想要让Onedrive将某个目录下的文件上传到云,得将对应的文件或者文件夹拷贝到Onedrive目录下,但是有时候却又不方便将文件挪动到Onedrive,例如D盘是仓库盘,存放着大量视频,但是Onedrive却在C盘,这个时候将视频移动到Onedrive会占用大量空间,并且本来D盘作为仓库盘就是用于存放资料的,现在挪动到C盘,影响文件规划.

所以可以使用mklink命令来在存放文件的目录和Onedrive目录中创建一个连接,让Onedrive识别到.

使用管理员权限打开CMD,输入以下命令:

```shell
mklink /J OneDrive文件夹下的目录绝对路径 需要链接到Onedrive的目录绝对路径
```

即可.