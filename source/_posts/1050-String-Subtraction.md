---
title: 1050 String Subtraction
date: 2019-04-22 11:40:02
tags: PAT
categories: [算法学习,PAT]
---

## 题目

Given two strings *S*1 and *S*2, *S*=*S*1−*S*2 is defined to be the remaining string after taking all the characters in *S*2from *S*1. Your task is simply to calculate *S*1−*S*2 for any given strings. However, it might not be that simple to do it **fast**.

### Input Specification:

Each input file contains one test case. Each case consists of two lines which gives *S*1 and *S*2, respectively. The string lengths of both strings are no more than 10<sup>4</sup>. It is guaranteed that all the characters are visible ASCII codes and white space, and a new line character signals the end of a string.

### Output Specification:

For each test case, print *S*1−*S*2 in one line.

### Sample Input:

```in
They are students.
aeiou
```

### Sample Output:

```out
Thy r stdnts.
```

<!-- more -->

## 题意

给定两个字符串S1,S2,输出S1-S2的结果.注意题目特地提到了fast,所以这道题时间是关键.

## 思路

这道题看似很简单,但是正是因为很简单,所以肯定不能用最简单的双重for循环来判定的,因为S1最大长度可能到达10<sup>4</sup>,直接循环肯定要超时的.

但是我们知道,这道题无论如何都是要循环S1一次的,因为总得要输出的嘛,所以这道题只能循环一次S1,否则就要超时了.

所以这次题目的要求是要在一次S1循环内直接验证S1的某个字符是否在S2中存在,要用哈希表来解决.

## 代码

```c#
public static void Main()
{
    string S1 = System.Console.ReadLine();
    string S2 = System.Console.ReadLine();
    bool[] signals = new bool[127];//创建一个bool数组,长度是ascii表的长度,保证每一个字符都有自己的位置.
    foreach (var signal in S2)
    {
        signals[signal] = true;//将bool数组的对应位置设置为true.
    }
    foreach (var character in S1)
    {
        if (!signals[character])//将S1字符串对逐个字符放入bool数组中进行验证,如果是true说明需要放弃,是false说明需要输出.
        {
            Console.Write(character);
        }
    }
}
```
