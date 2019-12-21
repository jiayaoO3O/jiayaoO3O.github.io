---
title: 1132 Cut Integer
date: 2019-05-06 09:54:36
tags: PAT
categories: [算法学习,PAT]
---

## 题目

Cutting an integer means to cut a K digits lone integer Z into two integers of (K/2) digits long integers A and B. For example, after cutting Z = 167334, we have A = 167 and B = 334. It is interesting to see that Z can be devided by the product of A and B, as 167334 / (167 × 334) = 3. Given an integer Z, you are supposed to test if it is such an integer.

### Input Specification:

Each input file contains one test case. For each case, the first line gives a positive integer N (≤ 20). Then N lines follow, each gives an integer Z (10 ≤ Z <2<sup>31</sup>). It is guaranteed that the number of digits of Z is an even number.

### Output Specification:

For each case, print a single line `Yes` if it is such a number, or `No` if not.

### Sample Input:

```in
3
167334
2333
12345678
```

### Sample Output:

```out
Yes
No
No
```

<!-- more -->

## 题意

给定一个偶数位的整数,平均分割成两部分,如果这两部分相乘,可以被这个偶数位整数整除,则输出Yse,否则输出No.

## 思路

这道题就体现出面向对象语言的优势了,直接字符串分割转整型就完事了,根本不需要除法逐位取数字.

判断的时候要注意一点0不能被模,所以首先要先把0排除了,而因为这个输入整数是大于等于10的,所以只有后半部分有可能是0.

## 代码

```c#
public static void Main()
{
    int count = int.Parse(Console.ReadLine());
    for (int i = 0; i < count; i++)
    {
        string input = Console.ReadLine();
        int firstPart = int.Parse(input.Substring(0, input.Length / 2));
        int secondPart = int.Parse(input.Substring(input.Length / 2, input.Length / 2));
        Console.WriteLine(secondPart != 0 && int.Parse(input) % (firstPart * secondPart) == 0 ? "Yes" : "No");
    }
}
```

