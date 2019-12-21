---
title: 1008 Elevator
date: 2019-04-16 13:13:31
tags: PAT
categories: [算法学习,PAT]
---

## 题目

The highest building in our city has only one elevator. A request list is made up with *N* positive numbers. The numbers denote at which floors the elevator will stop, in specified order. It costs 6 seconds to move the elevator up one floor, and 4 seconds to move down one floor. The elevator will stay for 5 seconds at each stop.

For a given request list, you are to compute the total time spent to fulfill the requests on the list. The elevator is on the 0th floor at the beginning and does not have to return to the ground floor when the requests are fulfilled.

### Input Specification:

Each input file contains one test case. Each case contains a positive integer *N*, followed by *N* positive numbers. All the numbers in the input are less than 100.

### Output Specification:

For each test case, print the total time on a single line.

### Sample Input:

```in
3 2 3 1
```

### Sample Output:

```out
41
```

<!-- more -->

## 题意

有一台电梯,有N层要停的楼,电梯从0层开始,上去一层要6s,下去一层要4s,每一层要停5s,到了最后的目的地之后就停在那,不用下来,求全程要花多少秒

## 思路

这一题也不难,只要判断当前的这一层比上一层高还是低就行了,但是这里有一个很坑的点,就是题目说:

> Each case contains a positive integer *N*, followed by *N* positive numbers.
>
> 每一个例子里包含一个整数N,后面跟着N个数字.

也就是说,上面的例子

> 3 2 3 1

是指一共有3层,分别是2 3 1,第一个3只是告诉你有多少层,不算进层数里面的,一开始我用手算了很久都算不出来41,还以为哪里理解错了, 原来是 : (2x6+5)+(6+5)+(2x4+5)=41

## 代码

```c#
static void Main(string[] args)
{
    string[] tokens = System.Console.ReadLine().Split();
    int result = 0;
    int before = 0;
    int now = 0;
    int[] floors = new int[int.Parse(tokens[0])];
    for (int i = 1; i < tokens.Length; i++)
    {
        floors[i - 1] = int.Parse(tokens[i]);
    }
    for (int i = 0; i < floors.Length; i++)
    {
        now = floors[i];
        if (now > before)
        {
            result += (now - before) * 6 + 5;
            before = now;
        }
        else
        {
            result += (before - now) * 4 + 5;
            before = now;
        }
    }
    Console.WriteLine(result);
}
```

