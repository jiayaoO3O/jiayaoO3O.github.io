---
title: 1027 Colors in Mars
date: 2019-04-18 10:53:02
tags: PAT
categories: [算法学习,PAT]
---

## 题目

People in Mars represent the colors in their computers in a similar way as the Earth people. That is, a color is represented by a 6-digit number, where the first 2 digits are for `Red`, the middle 2 digits for `Green`, and the last 2 digits for `Blue`. The only difference is that they use radix 13 (0-9 and A-C) instead of 16. Now given a color in three decimal numbers (each between 0 and 168), you are supposed to output their Mars RGB values.

### Input Specification:

Each input file contains one test case which occupies a line containing the three decimal color values.

### Output Specification:

For each test case you should output the Mars RGB value in the following format: first output `#`, then followed by a 6-digit number where all the English characters must be upper-cased. If a single color is only 1-digit long, you must print a `0` to its left.

### Sample Input:

```in
15 43 71
```

### Sample Output:

```out
#123456
```

<!-- more -->

## 题意

地球人的rgb颜色是16进制的,火星的rgb是13进制的,现在给你三个10进制数,要转换成火星rgb模式,要求:

- 每种颜色的13进制数占2位.

- 三个10进制数范围是0到168.
- 如果转换成13进制数只有1位,那剩下的那一位左边要.
- 结果按照#rrggbb输出.

## 思路

这道题考察的是进制转换,只要实现一个函数,对输入的10进制数转换成13进制,然后用一个字符串将3种颜色拼接在一起.

而10进制转换到任意进制的方法都是固定的,就是10进制数%目标进制的余数的拼接结果.

## 代码

先实现一个10进制转换到任意进制的函数:

```c#
public static string ConvertDecToTarget(int dec, int target, int length)
{
    char[] result = new char[length];//length用来规定结果的长度.
    for (int i = 0; i < length; i++)
    {
        result[i] = '0';
    }
    int n = dec;
    int index = length - 1;
    while (n != 0)
    {
        if (n % target >= 10)
        {
            result[index--] = (char)(n % target + 55);//整型到char大写字母.
        }
        else
        {
            result[index--] = (char)(n % target + 48);//整型到char数字
        }
        n /= target;
    }
    return new string(result);
}
```

```c#
public static void Main()
{
    string[] tokens = System.Console.ReadLine().Split();
    int red = int.Parse(tokens[0]);
    int green = int.Parse(tokens[1]);
    int blue = int.Parse(tokens[2]);
    string result = "#";
    result += ConvertDecToTarget(red, 13, 2);
    result += ConvertDecToTarget(green, 13, 2);
    result += ConvertDecToTarget(blue, 13, 2);
    Console.WriteLine(result);
}
```