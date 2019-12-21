---
title: 1144 The Missing Number
date: 2019-05-09 13:36:39
tags: PAT
categories: [算法学习,PAT]
---

## 题目

Given N integers, you are supposed to find the smallest positive integer that is NOT in the given list.

### Input Specification:

Each input file contains one test case. For each case, the first line gives a positive integer N (≤10<sup>5</sup>). Then N integers are given in the next line, separated by spaces. All the numbers are in the range of **int**.

### Output Specification:

Print in a line the smallest positive integer that is missing from the input list.

### Sample Input:

```in
10
5 -25 9 6 1 3 4 2 5 17
```

### Sample Output:

```out
7
```

<!-- more -->

## 题意

给定N个数，要求找出不在列表内的最小正整数.

## 思路

这道题如果没有理解题目就会跑偏了,题目要求**找出不在列表内的最小正整数**,也就是说如果1不在列表里,那1就是我们要找的那个最小正整数.

不管输入的列表有多少个数，我们都只需要在循环内从1开始比较，只要发现某个数不存在,那就输出这个数.

但是因为输入的数字列表是无序的,所以我们要将这些数字放入一个集合中.

## 代码

```c#
public static void Main()
{
    Console.ReadLine();
    HashSet<string> inputsInfo = new HashSet<string>(Console.ReadLine().Split());
    //用hashset来接收输入的数据.
    for (int i = 1; ; i++)
    {
        if (!inputsInfo.Contains(i.ToString()))//从1开始,如果i不在集合内那就是要找的数.
        {
            Console.WriteLine(i);
            return;
        }
    }
}
```









