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

理解Uni和Multi是什么, 是我们学习Mutiny和响应式编程中最重要的事情. 什么是Uni和Multi? 它们指的是`某个行为或动作产生的事件, 里面的泛型T就是这个事件的结果`, 这一句话是理解整个响应式编程的重点.

什么是事件? 发生了某一个行为或者动作, 就可以代表为一个事件, 例如 :

- 你**点击**了一下鼠标, 点击鼠标这个行为, 就是一个事件.
- 你**拿起**一台手机这个行为, 就是一个事件.
- 你**敲击**一下键盘这个行为, 就是一个事件.

以上三点, 都可以概括成**产生了一个"你做了某事"的事件**.

那后面跟着的**泛型T**是什么意思? 它就是指你这个事件所带来的结果, 例如在你**拿起一台手机**这个事件中, 返回的结果就是一台手机, 所以伪代码就是 :

```java
Uni<手机> event = 你拿起一台手机;
//or
Multi<手机> event = 你拿起一台手机;
```

你去查找数据库, 得到一个字符串这个事件, 对应的伪代码就是 :

```java
Uni<String> event  = 查找数据库;
//or
Multi<String> event  = 查找数据库;
```

又或者, 你去查找数据库, 得到的值为Long这个事件, 对应的伪代码就是 :

```java
Uni<Long> event = 查找数据库;
//or
Multi<Long> event = 查找数据库;
```

又或者, 你做的这个行为没有产生什么东西, 返回一个null, 那么对应的伪代码就是 :

```java
Uni<Void> event = 做了啥事情返回一个null;
//or
Multi<Void> event = 做了啥事情返回一个null;
```

这个时候, 应该就可以理解什么是Uni和Multi了, **他们就是代指某个事件, 而里面的泛型就是这个事件的返回值**.

那么到了这里, 你自然而然就会问, **这个Uni和Multi有什么区别呢?**

区别就是, **Uni代表着这个事件的返回结果T有0个或1个, 而Multi代表这事件的返回结果T有0个或N多个**, 根据上面的例子 :

```java
Uni<手机> event = 你拿起一台手机;
//or
Multi<手机> event = 你拿起一台手机;
```

**Uni&lt;手机&gt;**代表了你这个事件的结果要么是失败了, 要么是一个手机类.

**Multi&lt;手机&gt;**代表了你这个事件的结果要么是失败了, 要么是N个 (1个或多个都可以)手机类.

```java
Uni<String> event  = 查找数据库;
//or
Multi<String> event  = 查找数据库;
```

**Uni&lt;String&gt;**代表了你查找数据库这个事件的结果要么是一个失败 (抛出异常), 要么是一个String(注意这个Stirng可能为null, 因为数据库返回的就是null, 没查到).

**Multi&lt;String>**代表了你查找数据库这个事件的结果要么是一个失败 (抛出异常), 要么是一个**List&lt;String>**(注意这个List可能为null, 因为数据库返回的就是null, 没查到).

那你自然而然又会问, **那是不是说Uni&lt;List&lt;String&gt;&gt;和Multi&lt;String&gt;是相同的的意思呢?**

**是, 又不是.**

就最终返回的结果来看, 大家确实都可以看作是**List&lt;String&gt;**, 但是Multi自己会有一些Uni没有的功能, 和一些更复杂的方法, 例如Multi会带有**背压**.

一般情况下使用简单的Uni就已经足够了.

## Uni和Multi的特性

Uni和Multi有两个至关重要的特性, 必须要记住的 :

1. Uni和Multi所代表的事件是不可变的, **对这个事件的任何操作都会返回一个新的事件**.
2. Uni和Multi所代表的事件是惰性的, **必须要有人消费 (或者表达为监听, 订阅)这个事件, 这个事件才会真正的发生**.

先来解释第一点, 这个应该比较好理解, 因为大家经常接触类似的东西, 就是String.

String本身一旦创建, 就是不可变的了, 对一个字符串进行修改, 都会产生一个新的字符串, 原本的旧字符串是不会改变的 :

``` java
String apple = "APPLE";
apple.toLowerCase();
log.info(apple); //肯定还是"APPLE"
```

创建了一个字符串apple, 但是对它调用`toLowerCase()`之后, 只会产生一个新字符串, 并不会改变apple本身.

Uni和Multi也一样, 对这个事件进行操作之后, 就会产生一个新的Uni或者Multi :

```java
Uni<String> uni = Uni.createFrom().item("hello");//发生一个事件, 事件的结果是"hello"字符串

uni.onItem().transform(item -> item + " mutiny");//将字符串"hello"变成"hello mutiny"
uni.onItem().transform(String::toUpperCase);//再将字符串全部改成大写

uni.subscribe().with(item -> System.out.println(item));//打印字符串
```

这个打印的结果是什么?

答案就是最开始的`"hello"`, 因为我们对最开始的事件uni做任何操作, 都只会产生一个新的事件, 原本的事件是不会被改变的, 如果你没有用一个变量去接着它, 那结果就不会被影响, 所以如果上面的例子想要生效, 可以有两种办法 :

1. 要么你就一直链式调用下去 :

   ```java
   Uni<String> uni = Uni.createFrom().item("hello");
   uni.onItem().transform(item -> item + " mutiny").onItem().transform(String::toUpperCase).subscribe().with(item -> System.out.println(item));
   ```

2. 要么你就每次都用变量接着这个事件 :

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

继续解释上面的第二点, **Uni和Multi是惰性的**, 这是什么意思?

**这是指, 你最终必须明确表示对这个结果感兴趣, 明确地订阅了这个事件, 这个事件才会真正地发生.**

例如上面这个例子, `uni.subscribe().with(item -> System.out.println(item));`这一行代码就表明了对这个uni感兴趣, 因为你想要打印这个字符串, 所以你订阅这个事件, **只有当你订阅了这个事件, 这个事件才会发生, 并且进行后面的字符串拼接, 字符串转大写等操作.**

如果没有最后的这一行`subscribe()`, 那么虽然代码写了你要创建一个字符串事件, 并且想要拼接字符串, 然后全部转大写, **但是这一切都不会发生.**

那么有哪些行为可以表达我对这个事件感兴趣呢?

1. 要么你直接在代码里写出来你想要`subscribe()`这个方法, 这表明你**主动**要订阅这个事件.

2. 要么你将这个事件最终返回到系统外部, 由系统自动帮你订阅这个消息 :

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

   这是一个接口, 只要程序接收到外部的**get**请求, 调用了这个接口, 就会返回一个Uni出去, 这里你会发现虽然我们没有调用`subscribe()`这个方法, 但是因为这里已经是系统的最外层了, 我们把uni通过接口返回出去之后, 系统就会自动帮我们订阅这个事件.

所以说, 当你发现你的代码不生效的时候, 一定要看看是不是自己忘记订阅了.

理解了这些最基本的概念, 现在我们可以开始在响应式编程的海洋里自由飞翔了.

## 1.如何创建一个事件?

Mutiny中, 发生一个事件会有两个途径 :

1. 你手动创建了一个事件.

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
   
   上面这行代码, 表明本来没有什么事情发生, 但是你手动创建了一个事件. 事件包装着的类型是String, 注意, 在Mutiny中, **事件包装着的那个那个东西有一个名称, 叫做item**, 中文翻译成啥都很别扭, 所以就叫item吧. 在上面的代码中, item就是一个字符串, 内容为"apple"
   
   ```java
   Uni<String> appleUni = Uni.createFrom().failure(new Exception("bad apple"));
   //or
   Multi<String> appleMulti = Multi.createFrom().failure(new Exception("some bad apples"));
   ```
   
   上面这行代码, 表明本来没有什么事情发生, 但是你手动创建了一个事件, 里面装着的item是一个异常.
   
2. 你调用某些方法, 例如查找数据库引发了一个事件.

   ```java
   Uni<Apple> appleUni = NotificationRecordEntity.findById(1);
   ```

   这行代码它根据id查找了仓库中id为1的苹果, 返回了这个事件, 里面的item是一个**Apple**类.

当你拿到了一个Uni或者一个Multi, 你就算是正式开始进入响应式编程的世界了.

## 2.如何订阅一个事件?

上面说到, 当产生一个事件之后, 你必须表达出你对这个事件感兴趣, 即你必须消费这个事件, 这个事件才会真正地发生, 在代码中, 体现为`.subscribe().with()`方法 :

```java
Uni<String> appleUni = Uni.createFrom().item("apple");
appleUni.subscribe().with(log::info);
```

上面代码表明, 产生一个事件后中间没有进行任何操作, 马上就订阅了这个事件, 然后消费掉了, 消费的行为体现为用`log.info()`方法打印了"apple"这个item.

> 虽然我没说, 但是你应该意识到, 学习响应式编程的一个前提是先得把lambda表达式和java8的stream给学明白了, 不会写起码也得懂得怎么看lambda表达式.

## 3.如何转换一个事件中的item?

直到现在, 我们都一直在创建一个最简单的字符串Uni, 然后什么操作都没有进行直接就消费掉了, 但是这种行为就好比学习编程的时候打印一句"hello world", 对于实际开发肯定是没啥意义的, 所以接下来要讲的是, 当我得到一个**Uni&lt;String&gt;**之后, 要怎么对这个Uni的item进行变换呢?

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

当你使用了transform后, 你需要传入一个`Function<? super T, ? extends R> mapper` 的lambda表达式作为入参, 这个表达式接收一个T范型, 并且返回一个你想要的R泛型,

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

当你对一个Multi内的item进行转换时, 你会对item内的所有元素进行转换, 例如上面的Multi内原本有三个字符串`"apple1", "apple2", "apple3"`, 当你调用**一次**transform之后, 里面的item就变成了`"banana1", "banana2", "banana3"`了.

## 4.如何在产生一个事件之后做某些事情?

3种情况

TODO

## 5.如何在Uni和Multi之间转换?

TODO

## 6.如何处理失败

TODO

