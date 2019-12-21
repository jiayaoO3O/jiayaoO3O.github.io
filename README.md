## Blog
GitHub Page博客源文件,通过GitHub Action自动发布推送

## 原理

简单来说,原理就是将所有的hexo文章文件放到当前的Blog仓库,然后在当前仓库添加一个Action,每当这个仓库接收到更新,就触发一次Action,在Github提供的免费服务器上安装node.js,再安装hexo,然后生成静态页面,推送到**jiayaoO3O.github.io**仓库.

## 步骤

### 创建一对公钥与私钥

由于需要从**Blog**库推送到**jiayaoO3O.github.io**,所以得先创建一对公钥与私钥,输入:

```shell
ssh-keygen
```

按下三次回车,创建一对公钥与私钥.

将私钥添加到存放博客源文件的**Blog**仓库的**Settings**下**Secrets**中,名字设置为**ACTION_DEPLOY_KEY**.

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/665f9e47-9f25-4e29-b63c-630a2ee6c269/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=ASIAT73L2G45KPT4UMEP%2F20191221%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20191221T070314Z&X-Amz-Expires=86400&X-Amz-Security-Token=IQoJb3JpZ2luX2VjEI%2F%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaCXVzLXdlc3QtMiJHMEUCIHItQ6UIVDDRo5IHiQL7cSwOdRfJ2ew9AbHk20RPmzlKAiEAgu9yfTa5%2BaBJC2f5eqo2LHDl5mJCOT1JN%2Fs4BfiAmzQq2wII6P%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FARAAGgwyNzQ1NjcxNDkzNzAiDAYHUP6fr%2BwSa1WnbSqvApvsntOMMyBuwIHksXKtnPcnOqzPuO7jsMc4sjLAAnoBmapBVRu5D0HLWf%2F%2F%2B20Hu2gmEp2u7PuRR6GjvoHNUyw15bisIribl66deYP1cehYaPu%2BKe05Bh82g3pKX%2Fyx50olzlRsq377sRQU8KsaLq6UtHhWImqDkdsBlbWUIpIiat89%2FSMnJSQJKHDQfeQTgkskfjvfKga3u%2FwrKdkuE8s%2BbUSPw7Sr7chB3sVHnMVi743F5UOlK%2F53tIATqb6M2dlSD5y49Sg275SD%2FbzI0xBRcbDtNdAhGXwZZA5ZOueXmBRR1TZrgIwoI0LWPUlheMegN4JTXRLOrIjZ2Pd1mhxDECltxWz%2BBEaASOJcCqF5VV6APVFkd%2BVE4j6Op9iPMuR1RlO8Zgg0vObXiJ2bWDDegvfvBTrNAi7umDf7Pd%2FVyXzPC%2B2PMRDClGHsdGgpRRIj6j4N7FDpnY4wXKcoHFzs7k3cExaAYa8q9%2FqmAaUpA3ZsGvds0SFisR9I%2F1DtT8ibNE%2BLXiCvM0Bw4bLUmWCfxT50Sff4li%2F%2BLH3idkrqGOOh2TtXLCoIVa7yS5gxjAdL5WChn1pOewHTM84WMpMlLT7XMXXr0Ncub1KGPLhVb%2BVTj4QFmp%2BqOCSl4ISG%2Fck5UmAYDaOD03tLfCFl109CjuDl%2BSNQJzBeupFVvjECWc8G0TwgkvkdQsUqsp0jKAS4b9y3kEQjlxZvGksySXNazDriI5oQQOQYacmmQ1mIxGKvwjbc9GeU2DKteEPP0upjn1NIqOsLNdwal7HrxERqgnif4LqFyhGWF0rtScmpXDK7KL8SvldMpzjfj2QteOOmni1Bv%2Btd5Q9QwTn8SKGyYK1FjQ%3D%3D&X-Amz-Signature=3d8276a97f79ae745bcf0c14a2011b3e2b24f02e9f8234f309f8e70ca8171a8c&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

将公钥添加到**jiayaoO3O.github.io**的**Settings**下的**Deploy keys**中,名字不重要.

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/1222c246-09f0-480a-96c7-cc179a99f815/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=ASIAT73L2G45OGP2JMUM%2F20191221%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20191221T070728Z&X-Amz-Expires=86400&X-Amz-Security-Token=IQoJb3JpZ2luX2VjEI%2F%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaCXVzLXdlc3QtMiJGMEQCIEE0A4mX4zEuP%2FIzZweWb185vDGzJYqU9NmKV%2FGG8M6%2FAiA5Hmf%2Bgmz0hsu6DAY9mDhdFj4mg7jkUwtOb8bg3cEijSraAgjn%2F%2F%2F%2F%2F%2F%2F%2F%2F%2F8BEAAaDDI3NDU2NzE0OTM3MCIM7CDxduwzx5PUnRgfKq4CpNCIph5En51trKq9lJRTFnrlEkeYDbTdfFA%2BSYPqQDVIon2NP3yceh%2Ff1609Khmo0x82PY3V3Y9Tjer7r5f5f5XmTaG6Db4mHDEtUZg2vp3LnHCSCyAm%2B%2BJUBDI82CNsX9e3Pp1FivYF0ug2L3W9P3uD7sz8nbB%2BUgJVkozu%2BJM%2Fst4BYChlGBWKRKpVYTM0eIIOLA5beMGMVg%2B0meouFrRnEdjoPgN%2BynQQhHDZts5WS8AUZ4810SGCGQ6ro9StsWAHUmmD6LeksjZzVRyd%2BNIzwcwK2APfELaKRuQgj9%2FRSON9aUCAqEOzXvc%2FjhKGczNulk7%2FrirXd3FlyGeXC1s4pQWF02G8vgDHmhv8sKyB33W7EhvXrN8Sm5t2V4iZdk%2BCypaMFAO0ojh0h2wwj%2FP27wU6zwLuthUDZld13noM1urk%2BRSCN%2Becg4DyYu72PD7iSStDOQeuSyYFqAC8VpZ2KLVOHE%2F8HdO7FaHhYSUACoVPtJYWQuqFPUsGdtosr%2FdXDqFs%2FPy%2BtE17KKiTP4OFdA1b7m15HlJattMWD2GakFWU0zduEd7eqwPHozugWOqBSLhropHuksADBSyP8kOEIxdtc10HeLiFQSBXnZPiluAE%2BXIcMJZFHGtpzP9h24coyl9xFkIzPYp%2BEdPvBkNxWPuhhVRDuVpIfxjyprrpbu0Gdr7GX3LxzrqwAVu2CsbIbosZiiZWqz36GgbTetonksONoDR8x7qVvINDwOiLH40A7oim21iwW9LMMKbr5H6TQEGg%2BW91LlSya6kHgt2cd5PGcot3gwiDfFqLgmr0ACyV3H1EKGRJPsBBy%2BqA28aYWBvFmQ%2FJli3869%2F86Ung6mZDpg%3D%3D&X-Amz-Signature=65807122f8430bf3ad1f458de550cc3fab74406e7b9a6542ca9359db59bacb8e&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

这时候,**Blog**仓库就有权限推送文件到**jiayaoO3O.github.io**仓库了.

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

### 对Blog仓库创建一个Workflow

点击仓库的Actions,创建一个新的Workflow,名字不限.

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/da83764a-cb19-4573-ba36-646e73486fec/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=ASIAT73L2G45CKGRQF34%2F20191221%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20191221T071014Z&X-Amz-Expires=86400&X-Amz-Security-Token=IQoJb3JpZ2luX2VjEI%2F%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaCXVzLXdlc3QtMiJHMEUCID7mNJSV%2F9Uyd4Qd%2F%2Fw239mhT9MUjQJcMWlKg8NW1J6OAiEA51We4g%2B2RhYpFVnAHcinq4IyHc0UMfra9Sk%2BmMiDpHYq3AII6P%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FARAAGgwyNzQ1NjcxNDkzNzAiDExQ0HVtfAJEYABRQSqwAv8o7HsnK2gQptzLZEg5tBRSsT7tjFwt2yoZomoOVzyK12LvsFcCLyWO%2F6OOJn5oVrPIUUCNnZo5dfjOn8l6RZQbXi3CIShKWD9cPGq9cgErwoFQy%2BNhiGu6kfW8OanuoLH74z4qJSQ76a2xFhtr0m6Z53Z%2BPs%2B%2BGJTrNKRUj1tpkwJFmOYHvBn7HiC%2Bs2WhhIryoP6F%2FAGZOPsnMWSsuOxGQeefRgGaCTsb%2Fsxbi8dJhA738TU6WI0ld7dn0u1%2Bx0DvJaueeUQaqeMrXRYw7gEM0RCJQ4%2Bw37I%2BDcRliTaE9tdWcQpSmJcAZi3VtC4i0ojfATXKdMknhnkY4ZBLp%2FeAnWCOJfx1ORaxaL8U6kipJUeZalTSgtw2HNZNWEWmNiHJU9nQii1CUTnt%2FMC%2BOz8wzv727wU6zAJ1pHzBxjXF%2F3BhV9hxW9%2BOcCyaEEOxV0us2CRT9H1zUmthviePS2abJfnGxksKNfEqIDBL%2FXejr27Lmnlf%2B5xG9489fvNOcnb90sMQEX9aQHZOMbfTsRBqLUoFu9VuqcETIpXQNvsHUbyl5YmTfFtS4uBIouDtyfySCf8jw0%2FtXjB%2F7WhmGy%2F2tYDNxhzUYMn7hqPohRk8gRh%2F7B0zF%2BUG7t8tuk72bIu95do36KsR3iQOKDK69SG0SaFNiMPjdcEBIbPtaQUidUlaq7alvoGD3fSzdYWDyN4SFSLEbBCLDumfdePeCxcHjmy2XuAiY4JgfgY2sC5cOosyXeNWLrNdXSMkiZ1ScwALXqtTuD%2BxdDkdkL5MbphkT4PZPlVK35yuz34dyrlD%2Fqr9Z281v0fzbReO2j8RdXdAhlgF35xCo1DPgQh6qL0w2xRW9A%3D%3D&X-Amz-Signature=35579fd3db10717ec70914b1c9ffc8245bc58d80753a9ece1b244ac1d4fc8c39&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

添加内容如下:

```yaml
name: Hexo Publish
on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Blog
        uses: actions/checkout@v2
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

### 希望改进

本来是想着将源文件与网页文件都放在**jiayaoO3O.github.io**这个仓库的不同分支,这样就可以一个仓库两个分支解决所有问题,但是问题在于,目前GitHub的Workflow文件似乎只能放在master分支,并且当hexo发布的时候,会强制删除master分支的所有文件然后部署新的页面,这就导致了,每次发布,Workflow文件就会被删掉.

改进方法也很简单,就是等github的Workflow能够支持放到其他分支就可以了.
