---
title: Mutiny入门教程
date: 2021-07-25 00:24:00
tags: [Quarkus,Mutiny]
categories: [技术]
---

## 前言

本篇教程想要教会你如何使用Quarkus的官方响应式编程库Mutiny, 并且学会如何在Quarkus下进行响应式编程.

## 什么是Mutiny?

[Mutiny](https://smallrye.io/smallrye-mutiny/)是一个事件驱动的, quarkus官方的java响应式编程库. 在quarkus框架中进行响应式编程开发, 指的就是通过Mutiny来包装, 观察, 处理事件.

## 如何学习?

我接下来会用"提问-解答"的方式去讲解怎么学习使用Mutiny, 所以只要一步步跟着我的问题去思考, 就可以学会使用Mutiny, 也能学会在quarkus下进行响应式编程.

<!-- more -->

## Uni&lt;T&gt;与Multi&lt;T&gt;?

理解Uni和Multi是什么, 是我们学习Mutiny和响应式编程中最重要的事情. 什么是Uni和Multi? 它们指的是`某个行为或动作引起的事件所产生的数据流, 泛型T表示这个数据流的数据类型`, 这一句话是理解整个响应式编程的重点.

什么是事件? 发生了某一个行为或者动作, 就可以代表为一个事件, 例如 :

- 你**点击**了一下鼠标, 点击鼠标这个行为, 就是一个事件.
- 你**拿起**一台手机这个行为, 就是一个事件.
- 你**敲击**一下键盘这个行为, 就是一个事件.

以上三点, 都可以概括成**产生了一个"你做了某事"的事件**.

那数据流又是什么呢?它是指某个事件所产生的结果, **泛型T**就是这个事件所产生的数据流的类型. 例如在你**拿起一台手机**这个事件中, 返回的结果就是一个数据流, 一个手机就可以看作是数据流里的一个数据, 所以伪代码就是 :

```java
Uni<手机> event = 你拿起一台手机;
//or
Multi<手机> event = 你拿起一台手机;
```

你去查找数据库, 这个事件得到一个数据类型为字符串的数据流 :

```java
Uni<String> event  = 查找数据库;
//or
Multi<String> event  = 查找数据库;
```

又或者, 你去查找数据库, 这个事件得到一个数据类型为Long的数据流 :

```java
Uni<Long> event = 查找数据库;
//or
Multi<Long> event = 查找数据库;
```

又或者, 你做的这个行为没有产生什么东西, 返回一个Void的数据流 :

```java
Uni<Void> event = 做了啥事情返回一个null;
//or
Multi<Void> event = 做了啥事情返回一个null;
```

这个时候, 应该就可以理解什么是Uni和Multi了, **他们就是代指某个事件所产生的数据流, 而里面的泛型就是这个数据流的类型**.

那么到了这里, 你自然而然就会问, **这个Uni和Multi有什么区别呢?**

区别就是, **Uni代表着这个事件返回的数据流内有0个或1个T元素, 而Multi代表这个事件返回的数据流内有0个或N多个T类型元素**, 根据上面的例子 :

```java
Uni<手机> event = 你拿起一台手机;
//or
Multi<手机> event = 你拿起一台手机;
```

**Uni&lt;手机&gt;**代表了这个事件的数据流内要么是一个异常元素, 要么是一个手机元素.

**Multi&lt;手机&gt;**代表了这个事件的数据流内要么是一个异常元素, 要么是N个 (1个或多个都可以)手机元素.

```java
Uni<String> event  = 查找数据库;
//or
Multi<String> event  = 查找数据库;
```

**Uni&lt;String&gt;**代表了查找数据库这个事件产生的数据流要么是一个失败 (抛出异常), 要么是一个String(注意这个Stirng可能为null, 因为数据库返回的就是null, 没查到).

**Multi&lt;String>**代表了查找数据库这个事件产生的数据流要么是一个失败 (抛出异常), 要么是N个String(注意这个Stirng可能为null, 因为数据库返回的就是null, 没查到)

那你自然而然又会问, **那是不是说Uni&lt;List&lt;String&gt;&gt;和Multi&lt;String&gt;是相同的的意思呢?**

**不是**

Uni&lt;List&lt;String&gt;&gt;代表着一个数据流里面有一个元素, 这个元素是List&lt;String&gt;, 而Multi&lt;String&gt;代表着数据流里面有N个元素, 这些元素都是String类型.

一般情况下使用简单的Uni就已经足够了.

## Uni和Multi的特性

Uni和Multi有两个至关重要的特性, 必须要记住的 :

1. Uni和Multi所代表的数据流是不可变的, **对这个数据流的任何操作都会返回一个新的数据流**.
2. 对Uni和Multi所代表的数据流的操作是惰性的, **必须要有人消费 (或者表达为监听, 订阅)这个数据流, 这个对数据流的操作才会真正的发生**.

先来解释第一点, 这个应该比较好理解, 因为大家经常接触类似的东西, 就是String.

String本身一旦创建, 就是不可变的了, 对一个字符串进行修改, 都会产生一个新的字符串, 原本的旧字符串是不会改变的 :

``` java
String apple = "APPLE";
apple.toLowerCase();
log.info(apple); //肯定还是"APPLE"
```

创建了一个字符串apple, 但是对它调用`.toLowerCase()`之后, 只会产生一个新字符串, 并不会改变apple本身.

Uni和Multi也一样, 对这个数据流进行操作之后, 就会产生一个新的Uni或者Multi :

```java
Uni<String> uni = Uni.createFrom().item("hello");//发生一个事件, 产生的数据流内的结果是"hello"字符串

uni.onItem().transform(item -> item + " mutiny");//将字符串"hello"变成"hello mutiny"
uni.onItem().transform(String::toUpperCase);//再将字符串全部改成大写

uni.subscribe().with(item -> System.out.println(item));//打印字符串
```

这个打印的结果是什么?

答案就是最开始的`"hello"`, 因为我们对最开始的事件uni做任何操作, 都只会产生一个新的流, 原本的流是不会被改变的, 如果你没有用一个变量去接着它, 那结果就不会被影响, 所以如果上面的例子想要生效, 可以有两种办法 :

1. 要么你就一直链式调用下去 :

   ```java
   Uni<String> uni = Uni.createFrom().item("hello");
   uni.onItem().transform(item -> item + " mutiny").onItem().transform(String::toUpperCase).subscribe().with(item -> System.out.println(item));
   ```

2. 要么你就每次都用变量接着这个流 :

   ```java
           Uni<String> uni = Uni.createFrom()
                   .item("hello");
           uni = uni.onItem()
                   .transform(item -> item + " mutiny");
           uni = uni.onItem()
                   .transform(String::toUpperCase);
           uni.subscribe()
                   .with(item -> System.out.println(item));
   ```

继续解释上面的第二点, **对Uni和Multi的操作是惰性的**, 这是什么意思?

**这是指, 你最终必须明确表示对这个结果感兴趣, 明确地订阅了这个数据流, 对数据流的操作才会真正地发生.**

对数据流的各种操作, 并不是真正在操作这个数据流, 而是在**铺设操作数据流的流水线** (脑海中想象工厂的流水线), 只有当流水线的最末尾有人订阅了这个数据流, 流水线才会真正地工作, 数据才会真正地在流水线中流动到最终的订阅者手里.

例如上面这个例子, `uni.subscribe().with(item -> System.out.println(item));`这一行代码就表明了用户对这个uni感兴趣, 因为你想要打印这个字符串, 所以你订阅这个数据流, **只有当你订阅了这个数据流, 对这个数据流的操作流水线的才会工作, 才会进行后面的字符串拼接, 字符串转大写等操作.**

如果没有最后的这一行`.subscribe()`, 那么虽然代码写了你要创建一个事件产生一个字符串数据流, 并且想要拼接字符串, 然后全部转大写, **但是这一切都不会发生.**

那么有哪些行为可以表达我对这个数据流感兴趣呢?

1. 要么你直接在代码里写出来你想要`.subscribe()`这个流, 这表明你**主动**要订阅这个数据流.

2. 要么你将这个数据流最终返回到系统外部, 由系统自动帮你订阅这个消息 :

   ```java
       @GET
       public Uni<ApiResponse> getString() {
           Uni<String> uni = Uni.createFrom()
                   .item("hello");
           uni = uni.onItem()
                   .transform(item -> item + " mutiny");
           uni = uni.onItem()
                   .transform(String::toUpperCase);
           return uni.chain(ApiResponse::ok);
       }
   ```

   这是一个接口, 只要程序接收到外部的**get**请求, 调用了这个接口, 就会返回一个Uni出去, 这里你会发现虽然我们没有调用`.subscribe()`这个方法, 但是因为这里已经是系统的最外层了, 我们把uni通过接口返回出去之后, 系统就会自动帮我们订阅这个数据流.

所以说, 当你发现你的代码不生效的时候, 一定要看看是不是自己忘记订阅了.

理解了这些最基本的概念, 现在我们可以开始在响应式编程的海洋里自由飞翔了.

## 1.如何创建一个数据流?

Mutiny中, 发生一个流会有两个途径 :

1. 你手动创建了一个事件产生了一个数据流.

   ```java
   Uni<String> appleUni = Uni.createFrom().item("apple");
   //or
   List<String> appleList = new ArrayList<>();
   appleList.add("apple1");
   appleList.add("apple2");
   appleList.add("apple3");
   Multi<String> appleMulti = Multi.createFrom()
           .iterable(appleList);      
   //or
   Multi<String> appleMulti = Multi.createFrom()
           .items("apple1", "apple2", "apple3");
   ```
   
   上面这行代码, 表明本来没有什么事情发生, 但是你手动创建了一个事件. 事件返回一个类型是String的数据流, 注意, 在Mutiny中, **数据流中的数据有一个专有名词, 叫做item**, 在上面的代码中, item就是一个字符串, 内容为**"apple"**
   
   ```java
   Uni<String> appleUni = Uni.createFrom().failure(new Exception("bad apple"));
   //or
   Multi<String> appleMulti = Multi.createFrom().failure(new Exception("some bad apples"));
   ```
   
   上面这行代码, 表明本来没有什么事情发生, 但是你手动创建了一个事件产生了一个数据流, 里面装着的item是一个**异常**.
   
2. 你调用某些方法, 例如查找数据库引发了一个事件.

   ```java
   Uni<Apple> appleUni = NotificationRecordEntity.findById(1);
   ```

   这行代码它根据id查找了仓库中id为1的苹果, 事件返回了这个数据流, 里面的item是一个**Apple**类.

当你拿到了一个Uni或者一个Multi, 你就算是正式开始进入响应式编程的世界了.

## 2.如何订阅一个数据流?

上面说到, 当产生一个数据流之后, 你必须表达出你对这个数据流感兴趣, 即你必须消费这个数据流, 对数据流的操作才会发生, 在代码中, 体现为`.subscribe().with()`方法 :

```java
Uni<String> appleUni = Uni.createFrom().item("apple");
appleUni.subscribe().with(log::info);
```

上面代码表明, 产生一个数据流后中间没有进行任何操作, 马上就订阅了这个数据流, 然后消费掉了, 消费的行为体现为用`log.info()`方法打印了"apple"这个item.

> 虽然我没说, 但是你应该意识到, 学习响应式编程的一个前提是先得把lambda表达式和java8的stream给学明白了, 不会写起码也得懂得怎么看lambda表达式.

## 3.如何转换一个数据流中的item?

直到现在, 我们都一直在创建一个最简单的字符串Uni, 然后什么操作都没有进行直接就消费掉了, 但是这种行为就好比学习编程的时候打印一句**"hello world"**, 对于实际开发肯定是没啥意义的, 所以接下来要讲的是, 当我得到一个**Uni&lt;String&gt;**之后, 要怎么对这个Uni的item进行变换呢?

这时候要使用`.onItem().transform()`方法 :

```java
Uni<String> checkedAppleUni = appleUni.onItem()
        .transform(apple -> {
            if(apple.equals("apple")) {
                return "good apple";
            }
            return "bad apple";
        });
```

`.onItem()`表明了你要对item进行操作, 而`.transform()`表明了你想要对item进行转换.

当你使用了transform后, 你需要传入一个`Function<? super T, ? extends R> mapper` 的lambda表达式作为入参, 这个表达式接收一个**T**范型, 并且返回一个你想要的**R**泛型,

上面的代码表明, 你想要对appleUni内的item进行转换, **如果item等于"apple", 就将它转换成"good apple", 否则就转换为"bad apple".**

```java
Uni<Integer> intUni = appleUni.onItem()
        .transform(apple -> {
            if(apple.equals("apple")) {
                return 1;
            }
            return 0;
        });
```

上面代码表明, 你不但可以将原来的item变为其他字符串, 甚至可以变为一个数字, 实际上, **你可以把item转换为你想要的任何一种类型R.**

```java
Multi<String> apples = Multi.createFrom()
        .items("apple1", "apple2", "apple3");
Multi<String> bananas = apples.onItem()
        .transform(apple -> {
            return "banana" + apple.substring(apple.length() - 1);
        });
```

当你对一个Multi内的item进行操作时, 相当于铺设了操作item的流水线, 这时候数据会一个一个通过流水线, 所以数据流中的所有元素都会进行转换, 例如上面的Multi内原本有三个字符串`"apple1", "apple2", "apple3"`, 当你调用**一次**transform之后, 里面的item就变成了`"banana1", "banana2", "banana3"`了.

## 4.如何在产生一个数据流之后做某些事情?

一般情况下我们会在触发了一个数据流之后, 继续通过这个数据流的内容去引发另一个事件从而产生其他的数据流, 典型的例子就是通过数据库查到某张表的数据, 再通过这个数据去查找数据库的另一张关联表, 或者再去调用系统的另一个服务等, 在Mutiny中, 大致可以分成三个类型 :

1. 对数据流直接调用某个方法 :

   ```java
   Uni<String> u = uni.onItem()
       .invoke(i -> System.out.println("Received item: " + i));
   Multi<String> m = multi.onItem()
       .invoke(i -> System.out.println("Received item: " + i));
   ```

   这个时候可以选择使用`.invoke()`方法, 这个方法允许你传入一个`Consumer<? super T> callback`入参, 当item准备就绪的之后就会直接调用`.invoke()`方法中的`Consumer`, 如图所示 :

   ![](https://smallrye.io/smallrye-mutiny/assets/images/event-invoke.png)

   这里需要注意的是,`.invoke()`方法是同步的! 也就是说**线程会阻塞**在这个位置, 直到内部的方法执行完毕! 按照官方文档, `.invoke()`方法一般用于打印日志, 但是我个人建议少用这个方法.

2. 对数据流的处理触发另一个事件 :

   ```java
   multi.onCompletion().call(() -> resource.close());
   ```

   当你想要在某个数据流处理完成时触发另一个事件, 例如当你在上一个事件打开了一个资源, 事件完成了想要关闭资源, 这时候就可以选择使用`.call()`方法.

   `.call()`方法可以传入一个**Supplier&lt;Uni&lt;?&gt;&gt; supplier或者Function&lt;? super T, Uni&lt;?&gt;&gt; function**作为入参, `supplier`意味着这个事件不需要上一个数据流的item作为入参, `function`意味着这个事件需要上一个数据流的item作为入参 (lambda基础内容), 如图所示 :

   ![](https://smallrye.io/smallrye-mutiny/assets/images/event-call.png)

   但是这里需要注意的时, 和`.invoke()`不一样的是, `.call()`是**异步**的, **不会阻塞线程**, 所以在调用了`.call()`里面的方法后线程可以继续处理后面的事件.

   如果你仔细观察上面这张图, 会发现一个事情, 就是在图的左边, 原本的item内容是`1, 2, 3`, 但是执行了`.call()`之后, 内容还是`1, 2, 3`, 这代表什么呢? 这代表了, `.call()`内部的事件的返回值数据流item不会替代上一个数据流item的内容! 即上游数据流根本不关心`.call()`内部的事件返回什么, 内部的事件中**只有异常能够传播到外部数据流中**, 也就是说, 如果后续还有事件, 它要么就只能接收到原来的item的`1, 2, 3`, 要么就只能接收到由`.call()`传播出来的异常, 至于`.call()`内部事件正常的返回值, 没人在乎.

   从这里就能看出看出, `.call()`适合用于哪种场景了, 就是我们调用了某个方法, 但是不在乎方法的返回值的时候, 例如官方推荐的两个用法就是**关闭资源和刷新数据**. 关闭资源的时候我们只在乎有没有关闭失败, 至于close的时候返回什么对我们来说不重要.

3. 对数据流的处理触发另一个事件, 并且需要另一个事件的数据流 :

   ```java
   Uni<String> invokeRemoteGreetingService(String name);
   
   Uni<String> result = uni
       .onItem().transformToUni(name -> invokeRemoteGreetingService(name));
   ```

   这个场景是我们日常使用最多的场景, 就是一个数据流要去触发另一个事件, 并且需要用到另一个事件产生的数据流item, 例如上面的例子, `invokeRemoteGreetingService()`方法是一个远程调用的函数, 它的item是一个Stirng, 而这个String对我们来说是很有价值的, 这个时候就不能用上面的`.call()`了, 需要用到`.onItem().transformToUni()`方法, 如图所示 :

   ![](https://smallrye.io/smallrye-mutiny/assets/images/transform-to-uni.png)

   可以看到, 和上一张图片不同, 这里的圆圈数据流经过`.onItem().transformToUni()`流水线处理之后, 就变成了方块流水线了, 也就是说, `.onItem().transformToUni()`方法中生成的内部事件数据流会取代外部事件数据流.

   官方觉得`.onItem().transformToUni()`太长了, 所以做了一个简短的方法叫做`.chain()`, 表示用于链式调用, 两者是等价的 :

   ```java
   Uni<String> result = uni
       .onItem().transformToUni(name -> invokeRemoteGreetingService(name));
   //等价于
   Uni<String> result = uni.chain(name -> invokeRemoteGreetingService(name));
   ```

4. 对Multi流的处理触发其他事件 :

   ```java
   Multi<String> merged = multi
       .onItem().transformToUniAndMerge(name -> invokeRemoteGreetingService(name));
   
   Multi<String> concat = multi
       .onItem().transformToUniAndConcatenate(name -> invokeRemoteGreetingService(name));
   ```

   当我们想要在Multi流里面调用事件时, 事情就没有这么简单了, 在前面的例子中, 都是在一个Uni流中调用其他事件, 但是由于Uni流内只有一个item, 所以并不涉及顺序问题, 但是当我们在Multi中, 就必须考虑另一个场景了, 那就是返回的结果是否要按原数据流顺序返回?

   例如有这么一个Multi, 他的最初items是["a", "b"], 现在它要调用`invokeRemoteGreetingService()`这个远程方法, 这个方法接收一个name, 返回一个item为`"Hello name"`的Uni数据流, 如下所示 :

   ```java
   Uni<String> invokeRemoteGreetingService(String name);
   ```

   但由于网络原因, 调用这个远程方法返回的时间是不一定的, **有可能后面调用的反而先返回了**, 后续事件读取到的数据流就有可能是`["Hello b", "Hello a"]`, 因此需要怎么处理数据流取决于你是否需要确保返回结果是有序的, Mutiny为此提供了两种方案 :

   - merge -- 合并, 它不保留顺序, 并且将产生的结果按照返回的顺序依次传给下游事件.

   - concatenate -- 串联, 它严格要求传给下游事件的结果按照原来的的顺序进行.

   ```java
     Multi<String> merged = multi
         .onItem().transformToUniAndMerge(name -> invokeRemoteGreetingService(name));
   //结果为可能为["Hello b", "Hello a"], 也可能是["Hello a", "Hello b"]
     
     Multi<String> concat = multi
         .onItem().transformToUniAndConcatenate(name -> invokeRemoteGreetingService(name));
   //结果为["Hello a", "Hello b"], 维持原本的顺序
   ```
   

## 5.如何转换Uni为Multi?

想要从Uni转换到Multi, 可以使用`.onItem().transformToMulti()`方法 :

```java
Multi<String> result = uni
    .onItem().transformToMulti(item -> Multi.createFrom().items(item, item));
```

该方法需要传入一个`Function<? super T, ? extends Publisher<? extends R>> mapper`入参, 用于指导程序如何用一个Uni的item去创建一个Multi, 上面的例子表明, 你选择直接用2个item作为入参, 创建一个Multi, 如图所示 :

![](https://smallrye.io/smallrye-mutiny/assets/images/uni-transform-to-multi.png)

一个圆圈的Uni通过上面的代码编程了两个圆圈的Multi.

## 6.如何处理失败

上面我们的代码都是默认没有失败的, 但是在现实世界里这是不可能呢, 想象程序中所有的代码都没有`try-catch`做保护会多么的脆弱.

在Mutiny中, **对于失败的判断必须是程序抛出异常**, 而不能够自定义, 例如不能认为远程调用http请求的时候返回404状态码叫做失败, 除非你手动判断如果返回状态码等于404, 就抛出异常.

Mutiny对于失败的处理有以下几种, 他们都是在**.onFailure()**方法上进行的 :

- 传递到下游事件 (默认的)
- 将一个异常转换成另一个异常
- 切换到另一个备用item用于覆盖失败
- 切换到另一个备用事件用于覆盖失败
- 进行重试

对于第一点, 传递到下游很容易理解, 意思就是如果你不对异常进行处理, 下游事件再用**.onItem().transform()**等方法拿到的不会是上游数据流的item, 而是一个exception.

对于第二点, 将一个异常转换成另一个异常也很好理解, 例如将一个普通的`IOException`异常转换成更加具体的`ServiceUnavailableException`异常:

```java
Uni<String> u = uni
        .onFailure().transform(failure ->
                new ServiceUnavailableException(failure));
```

对于第三点, 通常情况下, 遇到失败了你会想用另一个事件产生的数据流数据来覆盖失败, 例如 :

```java
Uni<String> u1 = uni
        .onFailure().recoverWithItem("hello");

Uni<String> u2 = uni
        .onFailure().recoverWithItem(f -> getFallback(f));
```

当uni失败的时候, 既可以直接传一个item进去替换掉失败产生的exception, 也可以传一个`Supplier`进去提供你想要的item.

需要注意的是, 如果是数据流Multi, 那么当对数据流中的数据进行操作时引起异常然后使用另一个事件产生的数据流item来覆盖失败时, 下游事件会在这个收到这个覆盖的item之后收到一个结束流信号, 而不会继续处理异常之后的item, 例如 :

```java
Multi<String> apples = Multi.createFrom()
        .items("apple1", "apple2", "apple3");
apples.onItem()
        .transformToUniAndConcatenate(apple -> {
            if(apple.equals("apple2")) {
                return Uni.createFrom()
                        .failure(new Exception("bad apple"));
            }
            return Uni.createFrom()
                    .item("hello-" + apple);
        })
        .onFailure()
        .recoverWithItem("backup apple")
        .subscribe()
        .with(log::info);
```

原本的apples内有`"apple1", "apple2", "apple3"`这三个item, 后续我对这三个item进行处理, 如果item等于`"apple2"`, 那就抛出一个异常, 否则返回`"hello-" + apple`.

在`.onFailure()`阶段, 我又对异常进行了处理, `如果发现了异常, 就将item替换为一个"backup apple",` 这是响应式编程中很常见的处理方式, 那现在问题是, 最终对于这个apples的订阅, 打印日志应该显示什么结果呢?

答案是, 打印结果为 :

> hello-apple1
> backup apple

这是因为当apple2引发一个异常时, 整个数据流就中断了, 我们恢复失败**只是将exception覆盖为指定的"backup apple", 然后下游事件就接收到了一个完成信号**, 不会继续往下传递`hello-apple3`了.

在Multi中, 你也可以在收到异常的时候直接通过`.recoverWithCompletion()`宣布数据流处理完毕, **此时下游事件不会收到异常信息, 只会接收到异常之前的item** :

```java
Multi<String> apples = Multi.createFrom()
        .items("apple1", "apple2", "apple3");
apples.onItem()
        .transformToUniAndConcatenate(apple -> {
            if(apple.equals("apple2")) {
                return Uni.createFrom()
                        .failure(new Exception("bad apple"));
            }
            return Uni.createFrom()
                    .item("hello-" + apple);
        })
        .onFailure()
        .recoverWithCompletion()
        .subscribe()
        .with(log::info);
```

这里和上面不一样的地方在于, 没有对异常进行覆盖, 而是直接宣布传递一个完成信号, 这时候打印的结果就是 :

> hello-apple1

对于第四点, 当我们遇到失败的时候, 也可以直接切换到另一个数据流来覆盖失败, 如下所示 :

```java
Uni<String> u = uni
        .onFailure().recoverWithUni(f -> getFallbackUni(f));

Multi<String> m = multi
        .onFailure().recoverWithMulti(f -> getFallbackMulti(f));
```

对于Multi来说, 使用`.recoverWithMulti()`会在遇到异常时直接切换到新的Multi, 例如 :

```java
Multi<String> apples = Multi.createFrom()
        .items("apple1", "apple2", "apple3");
apples.onItem()
        .transformToUniAndConcatenate(apple -> {
            if(apple.equals("apple2")) {
                return Uni.createFrom()
                        .failure(new Exception("bad apple"));
            }
            return Uni.createFrom()
                    .item("hello-" + apple);
        })
        .onFailure()
        .recoverWithMulti(()->Multi.createFrom().items("a","b","c"))
        .subscribe()
        .with(log::info);
```

当检检测到异常时, 我选择直接用一个新的Multi来替换原有的Multi, 所以这时候打印的日志就应该时 :

> hello-apple1
> a
> b
> c

## 7.如何进行失败重试?

有一些失败是基本没有重试的必要的, 例如文件不存在抛出一个IO异常, 那很可能重试多少次都没有意义, 但是有一些失败是有重试价值的, 最典型的例子就是网络请求, 有时候网络波动可能导致失败, 但是很可能过一会儿重试了就成功了.

Mutiny设计上有一个让我无法理解的地方, 就是重试必须在`.onFailure()`上进行, **也就是说一定得要发生失败了才能重试**, 但是有很多时候想要重试并不一定是发生了失败, 例如我上面举的例子, 想要在http请求返回状态码为非200的时候进行重试, 这是是一个很常见的场景, 但是Mutiny不支持这样做, 你必须手动抛出一个异常之后才能进行重试, 我当初请问过他们团队为什么不将重试的条件作为可判断的, 他们说这是经过长时间讨论的方案.

想要进行失败重试, 你需要使用 `.onFailure().retry()`, 对于重试策略, Mutiny提供了很多种不同方案 :

1. 马上重试 :

   ```java
   indefinitelyUni<String> u = uni
           .onFailure().retry().indefinitely();
   ```

   这个方法会在遇到失败的时候马上重试, 但是注意如果一直失败就会一直重试, 直接死循环, 所以很少使用.

2. 指定重试次数 :

   ```java
   Uni<String> u = uni
           .onFailure().retry().atMost(3);
   Multi<String> m = multi
           .onFailure().retry().atMost(3);
   ```

   上述代码表明你**最多**想要立刻重试三次.

3. 带延迟的重试 :

   ```java
   Uni<String> u = uni
           .onFailure().retry()
           .withBackOff(Duration.ofMillis(100))
           .atMost(3);
   //or
   Uni<String> u = uni
           .onFailure().retry()
           .withBackOff(Duration.ofMillis(100), Duration.ofSeconds(1))
           .atMost(3);
   ```

   一般发生错误没必要马上重试, 可以先延迟一下,  `.withBackOff()`方法可以配置延迟多久之后再重试, 可以只传一个延迟参数, 此时会延迟100ms之后再重试, **并且每次重试延迟都会翻倍**.

   也可以传两个参数, 一个是初始延迟, 另一个是最大延迟, 此时**每次重试延迟都会翻倍直到最大延迟**.

4. 带随机抖动的重试 :

   ```java
   Uni<String> uni = uni
       .onFailure().retry()
           .withBackOff(Duration.ofSeconds(1)).withJitter(0.2).atMost(10);
   ```

   每次增加的延迟都正好翻倍, 可能不够随机性, 所以可以使用`.withJitter()`方法, 传入一个0到1之间的抖动参数, 越接近1抖动范围越大.

5. 带截止时间的重试 :

   ```java
   Uni<String> uni = uni
       .onFailure().retry()
           .withBackOff(Duration.ofSeconds(1)).withJitter(0.2).expireIn(1000L);
   ```

   有时候你不一定想写重试次数, 而是写一个**持续时间**或者**截止时间**, 如上所示, `.expireIn()`接收一个long类型的持续时间, 单位是毫秒, `.expireAt()`接收一个long类型的截至时间, 表明多久之后结束重试.

6. 直到满足判断条件的重试 :

   ```java
   Uni<String> u = uni
           .onFailure().retry()
           .until(f -> shouldWeRetry(f));
   ```

   有时候你就是想要一直重试到满足条件为止, 这时候你可以使用`.until()`方法, 传入一个判断条件, 直到判断返回`true`为止.

## 学海无涯

到这里, Mutiny的入门教程就结束了, 不知道有多少人能看到这一篇教程, 但是我相信只要是认真看完了肯定能学会响应式编程入门, 那么接下来要干什么呢?

1. 如果我哪里讲得不明白, 可以直接发送邮件向我进行询问.
2. 继续去[Mutiny官方指南](https://smallrye.io/smallrye-mutiny/guides)学习高阶知识.
3. 继续去[quarkus官方指南](https://quarkus.io/guides/)学习quarkus开发知识.
4. 我的一个[爬虫项目](https://github.com/jiayaoO3O/18-comic-finder)用到了响应式编程, 可以去看一下真正的项目是怎么使用响应式进行开发的.
5. 和我一起翻译quarkus官方指南 : https://github.com/jiayaoO3O/quarkus-guides-translation

![](https://user-images.githubusercontent.com/22891632/114197582-152e7b80-9985-11eb-9e15-5aa7ea62933c.gif)
