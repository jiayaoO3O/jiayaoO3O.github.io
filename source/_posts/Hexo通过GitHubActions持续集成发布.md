---
title: Hexo通过GitHubActions持续集成发布
date: 2019-12-21 17:06:56
tags: [GitHub Pages,Actions,Hexo]
categories: 技术
---

GitHub Page博客源文件,通过GitHub Action自动发布推送

## 原理

简单来说,原理就是将所有的hexo文章文件放到当前的**Blog**分支,然后在**Blog**分支添加一个Action,每当这个分支接收到更新,就触发一次Action,在Github提供的免费服务器上安装node.js,再安装hexo,然后生成静态页面,推送到**jiayaoO3O.github.io**的**master**分支.

<!-- more -->

## 步骤

### 创建一对公钥与私钥

由于需要从**Blog**分支推送到**jiayaoO3O.github.io**的**master**分支,所以得先创建一对公钥与私钥,输入:

```shell
ssh-keygen
```

按下三次回车,创建一对公钥与私钥.

将私钥添加到**Settings**下**Secrets**中,名字设置为**ACTION_DEPLOY_KEY**.

![](https://i.loli.net/2019/12/23/JCNwPQUGt1RdmiA.png)

将公钥添加到**Settings**下的**Deploy keys**中,名字不重要.

![](https://i.loli.net/2019/12/23/mC6ZlP3zTcw9IJ5.png)

这时候,**Blog**分支就有权限推送文件到**jiayaoO3O.github.io**的**master**分支了.

### 修改推送博客的方式

由于现在使用ssh方式对文件进行推送,所以要修改博客文件根目录的**_config.yml**文件的**deploy**方式:

```yaml
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: 'git'
  repo: git@github.com:jiayaoO3O/jiayaoO3O.github.io.git
  branch: master
```

### 创建一个新的Blog分支

从master分支中创建一个新的Blog分支,删除Blog分支的所有文件,然后添加所有博客源文件到Blog分支中.

![](https://i.loli.net/2019/12/23/wduqPtUkmf45pMg.png)

### 创建一个Workflow文件在Blog分支中

点击分支的Actions,创建一个新的Workflow,名字不限.

![](https://i.loli.net/2019/12/23/DB7OtP46osfu2ca.png)

注意这里如果是第一次创建这个文件,一定要确定选择的是Blog分支.

添加内容如下:

```yaml
name: Hexo Publish
on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Blog Branche
        uses: actions/checkout@v2
        with: 
          ref: Blog
      - name: Install Node.js 12
        uses: actions/setup-node@v1
        with:
          node-version: 12.x
      - name: Set Git Config
        env:
          ACTION_DEPLOY_KEY: ${{ secrets.ACTION_DEPLOY_KEY }}
        run: |
          mkdir -p ~/.ssh/
          echo "$ACTION_DEPLOY_KEY" | tr -d '\r' > ~/.ssh/id_rsa
          chmod 600 ~/.ssh/id_rsa
          ssh-keyscan github.com >> ~/.ssh/known_hosts
          git config --global user.name 'jiayaoO3O'
          git config --global user.email 'jiayao3.14@outlook.com'
      - name: Install Hexo
        run: |
          npm i -g hexo-cli 
          npm i
      - name: Deploy Hexo
        run: |
          hexo g -d
```

需要注意的是,如果刚刚在**Settings**下**Secrets**中添加的名字不是**ACTION_DEPLOY_KEY**,则

```yaml
ACTION_DEPLOY_KEY: ${{ secrets.ACTION_DEPLOY_KEY }}
```

这一行也要修改成对应的名字.

提交了这个Workflow文件之后,就会马上启动一个Action,如果没有报错,那么等一会儿博客就会自动更新.

### 修改Blog分支为默认分支

这一步是可选项,由于我们平时只关注Blog分支下面的源文件,对于master分支的页面内容其实并不关心,所以可以修改Blog分支为默认分支.

![](https://i.loli.net/2019/12/23/7lrVEMjIL64hQdq.png)

点击**Settings**中的**Branches**,选择**Blog**作为默认分支,这样仓库的默认显示页面就成了**Blog**分支了.
