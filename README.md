GitHub Page博客源文件,通过GitHub Action自动发布推送

## 原理

简单来说,原理就是将所有的hexo文章文件放到当前的**Blog**分支,然后在**Blog**分支添加一个Action,每当这个分支接收到更新,就触发一次Action,在Github提供的免费服务器上安装node.js,再安装hexo,然后生成静态页面,推送到**jiayaoO3O.github.io**的**master**分支.

## 步骤

### 创建一对公钥与私钥

由于需要从**Blog**分支推送到**jiayaoO3O.github.io**的**master**分支,所以得先创建一对公钥与私钥,输入:

```shell
ssh-keygen
```

按下三次回车,创建一对公钥与私钥.

将私钥添加到**Settings**下**Secrets**中,名字设置为**ACTION_DEPLOY_KEY**.

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/665f9e47-9f25-4e29-b63c-630a2ee6c269/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=ASIAT73L2G45KPT4UMEP%2F20191221%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20191221T070314Z&X-Amz-Expires=86400&X-Amz-Security-Token=IQoJb3JpZ2luX2VjEI%2F%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaCXVzLXdlc3QtMiJHMEUCIHItQ6UIVDDRo5IHiQL7cSwOdRfJ2ew9AbHk20RPmzlKAiEAgu9yfTa5%2BaBJC2f5eqo2LHDl5mJCOT1JN%2Fs4BfiAmzQq2wII6P%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FARAAGgwyNzQ1NjcxNDkzNzAiDAYHUP6fr%2BwSa1WnbSqvApvsntOMMyBuwIHksXKtnPcnOqzPuO7jsMc4sjLAAnoBmapBVRu5D0HLWf%2F%2F%2B20Hu2gmEp2u7PuRR6GjvoHNUyw15bisIribl66deYP1cehYaPu%2BKe05Bh82g3pKX%2Fyx50olzlRsq377sRQU8KsaLq6UtHhWImqDkdsBlbWUIpIiat89%2FSMnJSQJKHDQfeQTgkskfjvfKga3u%2FwrKdkuE8s%2BbUSPw7Sr7chB3sVHnMVi743F5UOlK%2F53tIATqb6M2dlSD5y49Sg275SD%2FbzI0xBRcbDtNdAhGXwZZA5ZOueXmBRR1TZrgIwoI0LWPUlheMegN4JTXRLOrIjZ2Pd1mhxDECltxWz%2BBEaASOJcCqF5VV6APVFkd%2BVE4j6Op9iPMuR1RlO8Zgg0vObXiJ2bWDDegvfvBTrNAi7umDf7Pd%2FVyXzPC%2B2PMRDClGHsdGgpRRIj6j4N7FDpnY4wXKcoHFzs7k3cExaAYa8q9%2FqmAaUpA3ZsGvds0SFisR9I%2F1DtT8ibNE%2BLXiCvM0Bw4bLUmWCfxT50Sff4li%2F%2BLH3idkrqGOOh2TtXLCoIVa7yS5gxjAdL5WChn1pOewHTM84WMpMlLT7XMXXr0Ncub1KGPLhVb%2BVTj4QFmp%2BqOCSl4ISG%2Fck5UmAYDaOD03tLfCFl109CjuDl%2BSNQJzBeupFVvjECWc8G0TwgkvkdQsUqsp0jKAS4b9y3kEQjlxZvGksySXNazDriI5oQQOQYacmmQ1mIxGKvwjbc9GeU2DKteEPP0upjn1NIqOsLNdwal7HrxERqgnif4LqFyhGWF0rtScmpXDK7KL8SvldMpzjfj2QteOOmni1Bv%2Btd5Q9QwTn8SKGyYK1FjQ%3D%3D&X-Amz-Signature=3d8276a97f79ae745bcf0c14a2011b3e2b24f02e9f8234f309f8e70ca8171a8c&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

将公钥添加到**Settings**下的**Deploy keys**中,名字不重要.

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/1222c246-09f0-480a-96c7-cc179a99f815/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=ASIAT73L2G45OGP2JMUM%2F20191221%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20191221T070728Z&X-Amz-Expires=86400&X-Amz-Security-Token=IQoJb3JpZ2luX2VjEI%2F%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaCXVzLXdlc3QtMiJGMEQCIEE0A4mX4zEuP%2FIzZweWb185vDGzJYqU9NmKV%2FGG8M6%2FAiA5Hmf%2Bgmz0hsu6DAY9mDhdFj4mg7jkUwtOb8bg3cEijSraAgjn%2F%2F%2F%2F%2F%2F%2F%2F%2F%2F8BEAAaDDI3NDU2NzE0OTM3MCIM7CDxduwzx5PUnRgfKq4CpNCIph5En51trKq9lJRTFnrlEkeYDbTdfFA%2BSYPqQDVIon2NP3yceh%2Ff1609Khmo0x82PY3V3Y9Tjer7r5f5f5XmTaG6Db4mHDEtUZg2vp3LnHCSCyAm%2B%2BJUBDI82CNsX9e3Pp1FivYF0ug2L3W9P3uD7sz8nbB%2BUgJVkozu%2BJM%2Fst4BYChlGBWKRKpVYTM0eIIOLA5beMGMVg%2B0meouFrRnEdjoPgN%2BynQQhHDZts5WS8AUZ4810SGCGQ6ro9StsWAHUmmD6LeksjZzVRyd%2BNIzwcwK2APfELaKRuQgj9%2FRSON9aUCAqEOzXvc%2FjhKGczNulk7%2FrirXd3FlyGeXC1s4pQWF02G8vgDHmhv8sKyB33W7EhvXrN8Sm5t2V4iZdk%2BCypaMFAO0ojh0h2wwj%2FP27wU6zwLuthUDZld13noM1urk%2BRSCN%2Becg4DyYu72PD7iSStDOQeuSyYFqAC8VpZ2KLVOHE%2F8HdO7FaHhYSUACoVPtJYWQuqFPUsGdtosr%2FdXDqFs%2FPy%2BtE17KKiTP4OFdA1b7m15HlJattMWD2GakFWU0zduEd7eqwPHozugWOqBSLhropHuksADBSyP8kOEIxdtc10HeLiFQSBXnZPiluAE%2BXIcMJZFHGtpzP9h24coyl9xFkIzPYp%2BEdPvBkNxWPuhhVRDuVpIfxjyprrpbu0Gdr7GX3LxzrqwAVu2CsbIbosZiiZWqz36GgbTetonksONoDR8x7qVvINDwOiLH40A7oim21iwW9LMMKbr5H6TQEGg%2BW91LlSya6kHgt2cd5PGcot3gwiDfFqLgmr0ACyV3H1EKGRJPsBBy%2BqA28aYWBvFmQ%2FJli3869%2F86Ung6mZDpg%3D%3D&X-Amz-Signature=65807122f8430bf3ad1f458de550cc3fab74406e7b9a6542ca9359db59bacb8e&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

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

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/abb9fff7-7e62-4916-9b4c-8f4d421ea885/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=ASIAT73L2G45B3K66HFN%2F20191221%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20191221T082439Z&X-Amz-Expires=86400&X-Amz-Security-Token=IQoJb3JpZ2luX2VjEI7%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaCXVzLXdlc3QtMiJIMEYCIQC6F8Neq5w8imn0kH%2FRKE2LmyJ92Bvo5ygKVaPetb9NtAIhAJ3s6upt93h8TTrlLBM4FnSHBPXJspTiOenib9%2B44XcGKtsCCOf%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEQABoMMjc0NTY3MTQ5MzcwIgz%2B8OMdEQLVxYvbSk4qrwJBmmoaZls%2BnYH7C0DmxmJFu%2B17AnACJZwooqbI2ozW9w5D%2BIfDIQh%2FH0504fkz1U8Q6vWD8HVrEbkQJYjUkWeHKAB0%2FVSlmviceRe%2FUeSzx%2BYUH5vXZkp0GvJCGVEFgWBQVFWARk%2B8A6cus7zv611Bpsq81pga1KKOOCvYJ8jML7nYwdkBdtJDHexVGS5sKz7fGbN7FKEaISQ1UUmUvFwj6DBjyiuZmUBnnJljahPrZlETsC8z9tO0WbksJ%2FHvvE%2FcltjmF1H4gAVaxtNPlfARIAXbfRoAIMoLyaWtESYq%2BQnF1rZBQA6XI5mVr3F4yNY6KkuQjag9Hl8cF%2F6wzo1sep1x3k6wNDLVd8tzPGEucAL8sp7Fam4hKiOi3x2eoGAENb5Aj39WkYgIs6g5DUEwwO727wU6zAJM6g6FvfwVreNtMMfygOhOFjI5XAneXcvcKIYRnt6KPUqu267yzDeSIULwlihBiSu56gGBGmYkeegyJGdd%2BBx5dHF0RMmESBFuwUjJdwWt%2BG4kxKSWuZdx5MaLQlm3knE4uYoMxryC2WTb%2BCZ7adehiJTWvv9Bq%2FoDLp1HZ5N2CVIjmAKLPIpRnpCz198DckT2ghm46nstGaGFVU14%2FXDUjUXup8bd1Co9B15o2V9x0JmkXrTXuIZR2DUcnYMKT9BUZSqLbsyPfvRd0mKutyIIHfsTAh7aHXEOMuYFwU2nt%2FeMTqixQribDJyAkHTCGmJwdKOLmHTIArPuueNADeg%2BjH2PkeAtEGTQBmjHQN9wDbiSpt%2Bx6%2FxqP7pFx3%2FC%2BP1Qs5GHu0jiQBCQ8xlmakgps3oQltZv7rD7OKp1JjtkbEtPuAUh%2B7tckSaWTw%3D%3D&X-Amz-Signature=287792d8d474b8d2cb43200b0be6b3d8b0a25e6f9eaea5fdacc79456b7f85872&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

### 创建一个Workflow文件在Blog分支中

点击分支的Actions,创建一个新的Workflow,名字不限.

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/20287f4e-87bb-48db-8d73-18c75098c8fe/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=ASIAT73L2G45C5PKXXFX%2F20191221%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20191221T082616Z&X-Amz-Expires=86400&X-Amz-Security-Token=IQoJb3JpZ2luX2VjEI7%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaCXVzLXdlc3QtMiJGMEQCIH%2BgpuXMJhz8NT4edK0scmpT7IMugh0KwFp6KwSMK1QTAiAJhPqdNi2dJY08EWgiIIs74ZvWERSKDivleKMNCBcd%2ByrbAgjn%2F%2F%2F%2F%2F%2F%2F%2F%2F%2F8BEAAaDDI3NDU2NzE0OTM3MCIMqUw%2F78lwCTPOzPl0Kq8CFmft3x1dFFpV6yFT5Uli%2Fxk1hFRsvhRsrKl5bqtlmcx%2Fn%2BgIbMpUV0Mp%2B31YKxn2i1rgKMy5pUNUvff70gkr769mUdiGQxZQUPFiDIlYh5Lr4MSo0g5QBun4e0AZp%2B2ZVJhqjggxtpbOTyZbqn%2Bd0ue84Xpv2CpDc9YTv7iGOCpIdyk0nWz%2BJ83kXzSATdpXoGobfiUC%2BlRLyhUGzcb4OKkBREvwP%2BokqwYH5luQtAy2mVlYKmt8FfbuzLEVRAjzB%2FY9tzwdlMBUOorqVsiEGzL7DTR8al%2BLBshP9PkbaEK%2F1IhHwAGbHtSWCuK%2FxeLhOXGqK66C%2FLiS7NpLvuqHw9639jCIOhXG3RzFpChLotyDjfggPIyCtwPYSdJ4WRKjNPEJpVWxeVnvKA3CKUIKMPHv9u8FOs4Cs69e6qW2bOGwjH4s8Kb6OEABKmKPOtxPjAdp3%2BcfTKD5ezUIUm1bfIIWiuga8X1lOGiAeKj2S6jMgtgaEBm8f37QHHjvHNhi2OGqXR9L7I9tb7DlMW%2F7MvuM9EzkdLXlykDU%2Ffsi%2BuRmBhuqgmrgkeoaqcencjxXlS3%2Fx%2B8iAKIXpqNmhAp29MtWJEUWW4lrmetC74qE5q6L4ZfNUerPGMLgKbXdyPMTmajybkf6w7NvIUnvR3J01GsgeIFBPjO1yRbk1TVTVkg52ylT6fsK7DEGFlcM%2FaGbkAono7OLJJISswiMRMLW5HUiZZf9s%2FjPvD0AR7TpWeM35vrRaITTZ%2BapqfF%2B2v%2BPi8oppOCp4pM7KDqiX0OMvoyXwsoZ9u4cOKmpTDco1uJxH7Q7kbzEPuY6kO97%2FQPhOi71rtB%2BS5J%2FDiRXHYy7j9nK%2Bhk%2Baw%3D%3D&X-Amz-Signature=b84257eb0ba70ee7accb857a332f6b3487dd5f1055a4970c18d78c4c290504c7&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

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

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/95d1f5d4-8fa9-47e4-ac57-692658a7f97c/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=ASIAT73L2G45FTVQW7B6%2F20191221%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20191221T083150Z&X-Amz-Expires=86400&X-Amz-Security-Token=IQoJb3JpZ2luX2VjEI%2F%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaCXVzLXdlc3QtMiJGMEQCICg2Cnp%2B%2Bk5twBzsyUmdBZM8Hc%2BUtstXHG%2BQZCB%2BGFuDAiBtfHI7%2FagI4NqdRBPWXuHG9E03JFCGL8MQ5A%2B9F%2B4FaSq9Awjo%2F%2F%2F%2F%2F%2F%2F%2F%2F%2F8BEAAaDDI3NDU2NzE0OTM3MCIMxlzD7GuDf%2FTxzjQOKpEDqorFmiYAGE2G%2BJKxcHJ4WlVa51iaExSLGrJlLeytHsggBgNxtVd1gnXMHoQwxJTvzRG2Y%2BhsMPAa5r%2F9APBj2BCMGLhl766Ipl9nk3eCAmsM%2FcNyiU7hHbBhnarlT5cCJWPMUfMXj9lw5C%2Bdgtvba0JIHDIxtat%2Br02PieziVvDrtxF5vrmb82FY7JH7KeHraTq%2FARxu3Z17YrMQ1Qu24Z8JyV6yB%2Bp3aUzv0nYsTgaKtbZsxCfTK4D58xdyiBb7ImhsbqYK78JKomyI%2FulqhUQ%2B4U4X%2FmHsLhGK%2BL%2BDRirXj7omV%2FLIZPC1F%2BlMm5Lgxt3uFlpWU3lG6JQJ5p%2BNS%2FkA0FSNi3QLiKuVHwY410i0foBCEvbzjpSxPahGbFzm%2BmmfI%2FLNLSlC3%2FA1mSerntnr2Lk1esyHNPhOCNPcFmt8HEuueRDdqbsJ1dBIjDqQExwNErY381esoQFEpKXrPF5c3lxawHXC65w8%2BTyD7maKMbqjeDQ1W2SiBNvJ4r5fyv2bQdt1lc%2FsUmnjHbbIYfMwmYP37wU67AGVq3CKU1EKl%2FGBWsBexDLkGcHs%2FW2Ci8NbQkW4%2FOoLj5RFP3HjWloTWYeDNQCPQGbaDAKWv8Xvjhq5%2FTyPzDN7PgG0rFPdtaYfqHBTU3Y7fa94SiYeRMKu5vZq4msV%2BJvSFP9tOX9YVpEkhsvXc2mArE47S4iw2b4pcmq7mhd5m5k2wqvAi9UEccxeTnBWzfZvG8PebvgBdY%2FldKDE0FY6GwJiUZrsaF2RkL9q6ZoNIQHOJVhgdJa1RbiC0LwCMEBu%2FrJnsjzpB6PXg8oRbLtrGf4kDuxEDN58TJC716xfNVHV4u42z5V%2FmNyLzA%3D%3D&X-Amz-Signature=978cd3382f615e8642690f0da6dc3256f174ff6e16c6b5eb3cdc84faf7907779&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

点击**Settings**中的**Branches**,选择**Blog**作为默认分支,这样仓库的默认显示页面就成了**Blog**分支了.
