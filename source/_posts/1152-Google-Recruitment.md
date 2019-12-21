---
title: 1152 Google Recruitment
date: 2019-05-09 17:01:09
tags: PAT
categories: [算法学习,PAT]
---

## 题目

In July 2004, Google posted on a giant billboard along Highway 101 in Silicon Valley (shown in the picture below) for recruitment. The content is super-simple, a URL consisting of the first 10-digit prime found in consecutive digits of the natural constant *e*. The person who could find this prime number could go to the next step in Google's hiring process by visiting this website.

![prime.jpg](https://images.ptausercontent.com/57148679-d574-4f49-b048-775c6c07791c.jpg)

The natural constant *e* is a well known transcendental number（超越数）. The first several digits are: *e* = 2.71828182845904523536028747135266249775724709369995957496696762772407663035354759457138217852516642**7427466391**932003059921... where the 10 digits in bold are the answer to Google's question.

Now you are asked to solve a more general problem: find the first K-digit prime in consecutive digits of any given L-digit number.

### Input Specification:

Each input file contains one test case. Each case first gives in a line two positive integers: L (≤ 1,000) and K (< 10), which are the numbers of digits of the given number and the prime to be found, respectively. Then the L-digit number N is given in the next line.

### Output Specification:

For each test case, print in a line the first K-digit prime in consecutive digits of N. If such a number does not exist, output `404` instead. Note: the leading zeroes must also be counted as part of the K digits. For example, to find the 4-digit prime in 200236, 0023 is a solution. However the first digit 2 must not be treated as a solution 0002 since the leading zeroes are not in the original number.

### Sample Input 1:

```in
20 5
23654987725541023819
```

### Sample Output 1:

```out
49877
```

### Sample Input 2:

```in
10 3
2468024680
```

### Sample Output 2:

```out
404
```

<!-- more -->

## 题意

找出给定长度的正整数L内的第一个长度为K的素数.

- 正整数内的0也算作长度,例如200236内找出第一个长度为4的素数,那0023就是答案.
- 如果没有要求的素数,输出404.

## 思路

这道题还是相对简单的,因为主要的考点还是在与判断某个数是不是素数,而判断素数的函数已经写过很多次了.

另一方面要获得指定长度的字符串,只需要用字符串的Substring()方法就可以了.

## 代码

先实现一个判断是否为素数的函数:

```c#
public static bool IsPrime(int input)
{
    if (input <= 1)
    {
        return false;
    }
    for (int i = 2; i <= Math.Sqrt(input); i++)
    {
        if (input % i == 0)
        {
            return false;
        }
    }
    return true;
}
```

```c#
public static void Main()
{
    int primeLength = int.Parse(Console.ReadLine().Split()[1]);
    string input = Console.ReadLine();
    for (int i = 0; i <= input.Length - primeLength; i++)
    {
        if (IsPrime(int.Parse(input.Substring(i, primeLength))))
        {
            Console.WriteLine(input.Substring(i, primeLength));
            return;
        }
    }
    Console.WriteLine("404");
}
```

