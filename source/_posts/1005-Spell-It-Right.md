---
title: 1005 Spell It Right
date: 2019-04-16 11:35:48
tags: PAT
categories: [算法学习,PAT]
---

## 题目

Given a non-negative integer *N*, your task is to compute the sum of all the digits of *N*, and output every digit of the sum in English.

### Input Specification:

Each input file contains one test case. Each case occupies one line which contains an *N* (≤10100).

### Output Specification:

For each test case, output in one line the digits of the sum in English words. There must be one space between two consecutive words, but no extra space at the end of a line.

### Sample Input:

```in
12345
```

### Sample Output:

```out
one five
```

<!-- more -->

## 题意

给你一串数字,把各个位加起来,得到的结果,每一位用英文单词表达出来.

## 思路

这道题比较简单,就是让你每一位求和,得到的结果再每一位去和英文单词匹配一下,这里要说一下,可以用两个方法去匹配单词,一可以直接用字符串数组,二可以用字典,但是我不知道用字典会慢多少.

## 代码

```c#
static void Main(string[] args)
{
    //int n = Convert.ToInt32(System.Console.ReadLine());
    string s = Console.ReadLine();
    int sum = 0;
    foreach (var num in s)
    {
		sum += System.Convert.ToInt32(num) - '0';
		//这里要注意不能直接用char转换成int,要减去48.
    }
    //可以用字典,也可以用数组.
    // Dictionary<char, string> dict = new Dictionary<char, string>();
    // dict.Add('0', "zero");
    // dict.Add('1', "one");
    // dict.Add('2', "two");
    // dict.Add('3', "three");
    // dict.Add('4', "four");
    // dict.Add('5', "five");
    // dict.Add('6', "six");
    // dict.Add('7', "seven");
    // dict.Add('8', "eight");
    // dict.Add('9', "nine");
    string[] dict = { "zero", "one", "two", "three", "four", "five", "six", "seven", "eight", "nine" };
    s = sum.ToString();
    for (int i = 0; i < s.Length; i++)
    {
		Console.Write(dict[s[i] - '0']);//直接读取s[i]也是char,也要减去48.
        if (i != s.Length - 1)
        {
            Console.Write(" ");
        }
    }
}
```

