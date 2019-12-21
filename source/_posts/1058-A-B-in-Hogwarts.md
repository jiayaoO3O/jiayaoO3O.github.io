---
title: 1058 A+B in Hogwarts
date: 2019-04-22 23:28:59
tags: PAT
categories: [算法学习,PAT]
---

## 题目

If you are a fan of Harry Potter, you would know the world of magic has its own currency system -- as Hagrid explained it to Harry, "Seventeen silver Sickles to a Galleon and twenty-nine Knuts to a Sickle, it's easy enough." Your job is to write a program to compute *A*+*B* where *A* and *B* are given in the standard form of `Galleon.Sickle.Knut` (`Galleon` is an integer in [0,10<sup>7</sup>], `Sickle` is an integer in [0, 17), and `Knut` is an integer in [0, 29)).

### Input Specification:

Each input file contains one test case which occupies a line with *A* and *B* in the standard form, separated by one space.

### Output Specification:

For each test case you should output the sum of *A* and *B* in one line, with the same format as the input.

### Sample Input:

```in
3.2.1 10.16.27
```

### Sample Output:

```out
14.1.28
```

<!-- more -->

## 题意

又是一道花里胡哨的题目,给你两个分成三个数的字符串,让你通过"霍格沃兹"进制来计算他们的和:最后一个数是29进制,中间那个数是17进制,第一个数是10进制.

## 思路

这道题没什么好说的,就是进制算就完事了,尤其是这个数只有3份,甚至都不用循环,直接写就行了.

## 代码

```c#
public static string HogwartsAddition(string A, string B)
{
    int[] currencyA = new int[3];
    int[] currencyB = new int[3];
    int[] result = new int[3];
    string[] strA = A.Split('.');
    string[] strB = B.Split('.');
    int flag = 0;
    for (int i = 0; i < strA.Length; i++)
    {
        currencyA[i] = int.Parse(strA[i]);
        currencyB[i] = int.Parse(strB[i]);
    }
    result[2] = (currencyA[2] + currencyB[2]) % 29;
    flag = currencyA[2] + currencyB[2] >= 29 ? 1 : 0;
    result[1] = (currencyA[1] + currencyB[1] + flag) % 17;
    flag = currencyA[1] + currencyB[1] + flag >= 17 ? 1 : 0;
    result[0] = currencyA[0] + currencyB[0] + flag;
    return result[0] + "." + result[1] + "." + result[2];
}
public static void Main()
{
    string[] tokens = System.Console.ReadLine().Split();
    Console.WriteLine(HogwartsAddition(tokens[0], tokens[1]));
}
```

