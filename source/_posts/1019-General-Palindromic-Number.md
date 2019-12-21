---
title: 1019 General Palindromic Number
date: 2019-04-17 00:34:56
tags: PAT
categories: [算法学习,PAT]
---

## 题目

A number that will be the same when it is written forwards or backwards is known as a **Palindromic Number**. For example, 1234321 is a palindromic number. All single digit numbers are palindromic numbers.

Now given any two positive integers *N* (<10<sup>5</sup>) and *D* (1<*D*≤10), you are supposed to tell if *N* is a reversible prime with radix *D*.

Given any positive decimal integer *N* and a base *b*, you are supposed to tell if *N* is a palindromic number in base *b*.

### Input Specification:

Each input file contains one test case. Each case consists of two positive numbers *N* and *b*, where 0<*N*≤10<sup>9</sup> is the decimal number and 2≤*b*≤10<sup>9</sup> is the base. The numbers are separated by a space.

### Output Specification:

For each test case, first print in one line `Yes` if *N* is a palindromic number in base *b*, or `No` if not. Then in the next line, print *N* as the number in base *b* in the form "a*k* a*k−1* ... *a*0". Notice that there must be no extra space at the end of output.

### Sample Input 1:

```in
27 2
```

### Sample Output 1:

```out
Yes
1 1 0 1 1
```

### Sample Input 2:

```in
121 5
```

### Sample Output 2:

```out
No
4 4 1
```

<!-- more -->

## 题意

这一题和上一题1015有点相似,那一题是找素数,这一题是找回文,但是不是直接判断一个数是不是回文,而是要判断某个数N的b进制数是不是回文.

另一点要注意的是,如果N是0,那不管是多少进制,结果都是Yes,并且输出0.

## 思路

这道题考的点主要还是两个,1是看你会不会进制转换,2是考你会不会判断回文.

所以我们可以先写一个判断回文的函数:

```c#
public static bool IsPalindromic(int[] input, int index)
{
    for (int i = 0; i <= index / 2; i++)
    {
        if (input[i] != input[index - 1 - i])
        {
            return false;
        }
    }
    return true;
}
```

再将已经转换了进制的整型数组放进去,判断是不是回文.

这里我们选择将N转换成b进制之后的结果放入一个整型数组,因为这样比较方便操作,但是要注意的是,因为一开始创建整型数组 **int[ ] n** 的时候,是不知道接下来要放进去的数据是多少位的,这就导致我们不知道整型数组结束的最后下标在哪,所以需要一个下标变量index确定末尾.

## 代码

```c#
static void Main(string[] args)
{
    string line = "";
    while ((line = System.Console.ReadLine()) != null)
    {
        string[] tokens = line.Split();
        int N = int.Parse(tokens[0]);
        int b = int.Parse(tokens[1]);
        if (N == 0)
        {
            Console.WriteLine("Yes");
            Console.WriteLine("0");
        }
        else
        {
            int[] n = new int[40];
            int index = 0;
            while (N != 0)
            {
                n[index] = N % b;
                N /= b;
                index++;
            }
            if (IsPalindromic(n, index))
            {
                Console.WriteLine("Yes");
            }
            else
            {
                Console.WriteLine("No");
            }
            for (int i = index - 1; i >= 0; i--)
            {
                if (i > 0)
                {
                    Console.Write(n[i] + " ");
                }
                else
                {
                    Console.WriteLine(n[i]);
                }
            }
        }
    }
}
```