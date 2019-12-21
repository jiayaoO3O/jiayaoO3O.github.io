---
title: 1069 The Black Hole of Numbers
date: 2019-04-23 23:30:27
tags: PAT
categories: [算法学习,PAT]
---

## 题目

For any 4-digit integer except the ones with all the digits being the same, if we sort the digits in non-increasing order first, and then in non-decreasing order, a new number can be obtained by taking the second number from the first one. Repeat in this manner we will soon end up at the number `6174` -- the **black hole** of 4-digit numbers. This number is named Kaprekar Constant.

For example, start from `6767`, we'll get:

```
7766 - 6677 = 1089
9810 - 0189 = 9621
9621 - 1269 = 8352
8532 - 2358 = 6174
7641 - 1467 = 6174
... ...
```

Given any 4-digit number, you are supposed to illustrate the way it gets into the black hole.

### Input Specification:

Each input file contains one test case which gives a positive integer *N* in the range (0,10<sup>4</sup>).

### Output Specification:

If all the 4 digits of *N* are the same, print in one line the equation `N - N = 0000`. Else print each step of calculation in a line until `6174` comes out as the difference. All the numbers must be printed as 4-digit numbers.

### Sample Input 1:

```in
6767
```

### Sample Output 1:

```out
7766 - 6677 = 1089
9810 - 0189 = 9621
9621 - 1269 = 8352
8532 - 2358 = 6174
```

### Sample Input 2:

```in
2222
```

### Sample Output 2:

```out
2222 - 2222 = 0000
```

<!-- more -->

## 题意

6174是一个黑洞数字,所有的4位数(除了4个数字一样的),经过降序,再减去升序,得到的结果再重复这个过程,最终的数字都会得到6174,现在要求把这个过程展现出来.

注意这里有一个坑,那就是不管你输入的数是不是4位数,展现过程中都必须显示为4位,也就是说不足4位的前面要补零.

## 思路

这道题的难点也不是很高,只是要知道上面说的那个坑,把所有的数都补足4位就行了.

## 代码

创建一个给字符串降序排序的函数:

```c#
public static string decreasingSort(string input)
{
    while (input.Length != 4)//不足4位要先补0.
    {
        input += "0";
    }
    char[] num = input.ToCharArray();
    Array.Sort(num);//直接升序排序.
    Array.Reverse(num);//再反转一次,就变成降序排序了.
    return new string(num);
}
```

创建一个反转字符串的函数:

```c#
public static string Reverse(string input)
{
    char[] num = input.ToCharArray();
    Array.Reverse(num);
    return new string(num);
}
```

```c#
public static void Main()
{
    string input = System.Console.ReadLine();
    string result = "";
    string sortedInput = "";
    string reversedInput = "";
    while (result != "6174")
    {
        sortedInput = decreasingSort(input);
        reversedInput = Reverse(sortedInput);
        result = (int.Parse(sortedInput) - int.Parse(reversedInput)).ToString();
        if (sortedInput == reversedInput)
        {
            Console.WriteLine("{0} - {1} = {2}", sortedInput, reversedInput, "0000");
            break;
        }
        Console.WriteLine("{0} - {1} = {2}", sortedInput, reversedInput, result.Length == 4 ? result : "0" + result);
        input = result;
    }
}
```