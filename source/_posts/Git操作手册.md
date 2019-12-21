---
title: Git操作手册
date: 2019-03-21 17:05:12
tags: git
categories: 笔记
---

## 生成ssh密钥

  ```
ssh-keygen
  ```

## 查看git配置信息

  ```
git config --list
  ```

## 配置git

  ```
git config --global : 当前用户
git config --system : 整个系统
git config --global user.name "jiayao"
git config --global user.email jiayao3.14@outlook.com
git config --global core.editor vim : 设置默认编辑器
git config --global alias.alias full_name : 设置别名
  ```

## 初始化git仓库

  ```
git init
  ```

<!-- more -->

## 添加文件到仓库/暂存已修改文件

  ```
git add <file>
  ```

## 取消暂存

```
git reset HEAD <file>
```

## 取消修改

```
git checkout -- <file>
```

## 提交更改

  ```
git commit -m "massage"
git commit -a : 跳过使用暂存区,直接将修改的文件提交
git commit --amend : 重新提交来覆盖上一次操作
  ```

## 克隆仓库

  ```
git clone <url>
  ```

## 查看仓库状态

  ```
git status
git status -s : 紧凑输出
?? : 未跟踪
A : 新添加到暂存区
左M : 修改并且加入暂存区
右M : 修改但未加入暂存区
MM : 修改后加入暂存区又被修改
  ```

## 忽略文件.gitignore

github的[忽略文件列表](https://github.com/github/gitignore),忽略文件应该在仓库创建后马上添加,因为已经提交了的文件,哪怕后来被加入了忽略列表,也并不会自动忽略.

## 比较文件差异

  ```
git diff : 比较当前已修改和已暂存文件的差异
git diff --staged : 比较已暂存和已提交的文件差异
git diff --cached
git diff --check : 查找空白错误
  ```

## 删除已跟踪文件

  ```
git rm --cached <file> : 会将文件移除暂存区但是不会删除文件本身
git rm <file> : 会将文件移出仓库并且删除
  ```

## 更改文件名称

  ```
git mv file_from file_to : 等于下面三个命令
mv file_from file_to
git rm file_from
git add file_to
  ```

## 查看提交历史

  ```
git log
git log -p -2 : 添加-p显示每次提交的差异,-2表示最近两次
git log --stat : 查看每次提交的简略统计信息
git log --since : 从什么时候开始
git log --until : 直到什么时候
  ```

## 添加远程仓库

```
git remote add <shortname> <url>
```
## 查看远程仓库

```
git remote show [remote-name]
```
## 拉取远程仓库

``` 
git fetch [remote-name] : 只是拉取,但是不会自动合并
git pull [remote-name] : 拉取并且合并到当前分支
```

## 推送到远程仓库

```
git push [remote-name] [branch-name]
```

## 移除远程仓库

```
git remote rm [remote-name]
```

## 创建分支

```
git branch <name>
```

## 删除分支

```
git branch -d <name>
```

## 切换分支

```
git checkout <name> : 可以切换分支,或者HEAD移动到某个提交
git checkout -b <name> : 创建并且切换到新建分支
```

## 合并分支

```
git merge <target_branch>
```

## 变基

```
git rebase target : 把自身分支变基到target分支,此时切换到target分支进行merge会产生快速合并
git rebase --onto master server client : 取出 client 分支，找出处于 client 分支和 server 分支的共同祖先之后的修改，然后把它们在 master 分支上重放一遍
git rebase [basebranch] [topicbranch] : 可以省略切换分支再变基的步骤
```

