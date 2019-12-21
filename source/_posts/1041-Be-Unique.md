---
title: 1041 Be Unique
date: 2019-04-19 00:26:29
tags: PAT
categories: [算法学习,PAT]
---

## 题目

Being unique is so important to people on Mars that even their lottery is designed in a unique way. The rule of winning is simple: one bets on a number chosen from [1,10<sup>4</sup>]. The first one who bets on a unique number wins. For example, if there are 7 people betting on { 5 31 5 88 67 88 17 }, then the second one who bets on 31 wins.

### Input Specification:

Each input file contains one test case. Each case contains a line which begins with a positive integer *N* (≤10<sup>5</sup>) and then followed by *N* bets The numbers are separated by a space.

### Output Specification:

For each test case, print the winning number in a line. If there is no winner, print `None` instead.

### Sample Input 1:

```in
7 5 31 5 88 67 88 17
```

### Sample Output 1:

```out
31
```

### Sample Input 2:

```in
5 888 666 666 888 888
```

### Sample Output 2:

```out
None
```

<!-- more -->

## 题意

我发现PAT的题目就不喜欢直接问你,而是搞成某个情景来体现这个算法,花里胡哨.

这道题的意思是火星上的人都喜欢追求独一无二,所以这里有个彩票游戏,就是大伙N人从1到10^4^数字范围内抽一个数字下注,下注完了之后,第一个抽到独一无二,没有和别人重复数字的人,就赢了.

给定的一串数字,第一个数是指人数N,后面的是指大家下注的数字,我们要找出第一个只出现一次的数字,例如第一个例子里的31,66,17都出现了一次,但是31是第一个出现的,所以31赢了.

如果没有独一无二的数字,就输出None.

## 思路

这道题是肯定不能直接用循环一次所有输入数字的方法来做的,因为这个人数N的上限是10^5^,循环完了肯定都超时了,所以要用到hash,直接定位到所选的数字,然后修改这个数字的次数,因为我们用的是数组,直接定位到数组的某个位置速度是很快的.

## 代码

```c#
public static void Main()
{
    string[] tokens = System.Console.ReadLine().Split();
    int count = int.Parse(tokens[0]);
    int[] numbers = new int[count];//记录输入了哪些数字.
    int[] frequency = new int[100000];//记录所输入的这些数字的频率.
    for (int i = 0; i < count; i++)
    {
        numbers[i] = int.Parse(tokens[i + 1]);
        frequency[numbers[i]]++;//如果numbers[i]是5,那frequency的第5个位置就+1.
    }
    for (int i = 0; i < count; i++)
    {
        if (frequency[numbers[i]] == 1)
        {
            Console.WriteLine(numbers[i]);
            break;
        }
        if (i == count - 1)
        {
            Console.WriteLine("None");
        }
    }
}
```