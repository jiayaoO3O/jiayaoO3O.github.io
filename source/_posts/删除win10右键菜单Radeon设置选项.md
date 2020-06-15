---
title: 删除win10右键菜单Radeon设置选项
date: 2020-06-15 18:31:00
tags: windows10
categories: 技术
---
1. 打开regedit.

2. 输入 :

   ```
   计算机\HKEY_CLASSES_ROOT\Directory\Background\shellex\ContextMenuHandlers\
   ```

3. 删除ACE文件夹.