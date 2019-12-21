---
title: 1065 A+B and C (64bit)
date: 2019-04-23 16:26:10
tags: PAT
categories: [算法学习,PAT]
---

## 题目

Given three integers *A*, *B* and *C* in [−2<sup>63</sup>,2<sup>63</sup>], you are supposed to tell whether *A*+*B*>*C*.

### Input Specification:

The first line of the input gives the positive number of test cases, *T* (≤10). Then *T* test cases follow, each consists of a single line containing three integers *A*, *B* and *C*, separated by single spaces.

### Output Specification:

For each test case, output in one line `Case #X: true` if *A*+*B*>*C*, or `Case #X: false` otherwise, where *X* is the case number (starting from 1).

### Sample Input:

```in
3
1 2 3
2 3 4
9223372036854775807 -9223372036854775808 0
```

### Sample Output:

```out
Case #1: false
Case #2: true
Case #3: false
```

<!-- more -->

## 题意

给定三个大范围的整数A,B,C,要求判断A+B是否大于C

## 思路

这道题因为范围是64位的,所以肯定不能普通整型int的,而且哪怕你定义了64位的long,当两个数都超大时,相加结果仍然会溢出.

那怎么办呢?

128位的decimal莽就完事了,毫无技术含量.

当然这是一种投机取巧的方法,正规的方法是通过溢出判断.

定义了三个long,如果A>0,B>0,这个时候如果他们两个数都相当庞大,相加导致溢出,我们知道,不管相加结果是多少,只要溢出了,那就肯定比C大.

如果A<0,B<0,这个时候如果他们两个数都非常小,导致相加变得更小而溢出了,我们知道,不管结果如何,只要溢出了,那就肯定比C小.

而正数相加溢出,结果为负数;负数相加溢出,结果为正数,我们可以通过这样来判断,结果是否大于C.

## 代码

机智版


```c#
public static void Main()
{
    int T = int.Parse(Console.ReadLine());
    for (int i = 0; i < T; i++)
    {
        decimal A, B, C;
        string[] tokens = System.Console.ReadLine().Split();
        A = decimal.Parse(tokens[0]);
        B = decimal.Parse(tokens[1]);
        C = decimal.Parse(tokens[2]);
        if (A + B > C ? true : false)
        {
            Console.WriteLine("Case #{0}: true", i + 1);
        }
        else
        {
            Console.WriteLine("Case #{0}: false", i + 1);
        }
    }
}
```

老实版

```c#
public static void Main()
{
    int T = int.Parse(Console.ReadLine());
    for (int i = 0; i < T; i++)
    {
        long A, B, C;
        string[] tokens = System.Console.ReadLine().Split();
        A = long.Parse(tokens[0]);
        B = long.Parse(tokens[1]);
        C = long.Parse(tokens[2]);
        long sum = A + B;
        if (A > 0 && B > 0 && sum < 0)
        {
            Console.WriteLine("Case #{0}: true", i + 1);
        }
        else
        {
            if (A < 0 && B < 0 && sum >= 0)
            {
                Console.WriteLine("Case #{0}: false", i + 1);
            }
            else
            {
                if (sum > C)
                {
                    Console.WriteLine("Case #{0}: true", i + 1);
                }
                else
                {
                    Console.WriteLine("Case #{0}: false", i + 1);
                }
            }
        }
    }
}
```

