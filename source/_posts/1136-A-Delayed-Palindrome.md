---
title: 1136 A Delayed Palindrome
date: 2019-05-06 13:13:07
tags: PAT
categories: [算法学习,PAT]
---

## 题目

Consider a positive integer *N* written in standard notation with *k*+1 digits *ai* as *ak*⋯*a1* *a0* with 0≤*ai*<10 for all *i* and *ak*>0. Then *N* is **palindromic** if and only if *ai*=*ak*−*i* for all *i*. Zero is written 0 and is also palindromic by definition.

Non-palindromic numbers can be paired with palindromic ones via a series of operations. First, the non-palindromic number is reversed and the result is added to the original number. If the result is not a palindromic number, this is repeated until it gives a palindromic number. Such number is called **a delayed palindrome**. (Quoted from <https://en.wikipedia.org/wiki/Palindromic_number> )

Given any positive integer, you are supposed to find its paired palindromic number.

### Input Specification:

Each input file contains one test case which gives a positive integer no more than 1000 digits.

### Output Specification:

For each test case, print line by line the process of finding the palindromic number. The format of each line is the following:

```
A + B = C
```

where `A` is the original number, `B` is the reversed `A`, and `C` is their sum. `A` starts being the input number, and this process ends until `C` becomes a palindromic number -- in this case we print in the last line `C is a palindromic number.`; or if a palindromic number cannot be found in 10 iterations, print `Not found in 10 iterations.` instead.

### Sample Input 1:

```in
97152
```

### Sample Output 1:

```out
97152 + 25179 = 122331
122331 + 133221 = 255552
255552 is a palindromic number.
```

### Sample Input 2:

```in
196
```

### Sample Output 2:

```out
196 + 691 = 887
887 + 788 = 1675
1675 + 5761 = 7436
7436 + 6347 = 13783
13783 + 38731 = 52514
52514 + 41525 = 94039
94039 + 93049 = 187088
187088 + 880781 = 1067869
1067869 + 9687601 = 10755470
10755470 + 07455701 = 18211171
Not found in 10 iterations.
```

<!-- more -->

## 题意

给定一个不大于1000位的正整数A,尝试寻找一个回文数:

1. 如果这个数本身就是回文数,输出`A is a palindromic number.`
2. 如果这个数不是回文数,则寻找它的延迟回文数:
   - 将这个数A与它的反转数B相加,判断它们的和C是否为回文数,如果是,则C为延迟回文数,输出`C is a palindromic number.`.
   - 如果C不是回文数,输出`A + B = C`,将C赋值给A,重复这个过程.
   - 10步之内如果没有找到延迟回文数,则输出`Not found in 10 iterations.`
3. 0也是回文数.

## 思路

这道题非常有意思,看似是考察回文数,但实际上是考察**大数相加**,因为输入的数有可能有好几百位的长度,绝对不能够用整型相加得到和,肯定会溢出的.

大数相加的操作比较简单,就是将两个数字都作为字符串来相加,因为它们的长度都是相同的,所以直接逐位相加就可以了,得到的结果仍作为字符串.

另一个比较坑的点,题目没有说如果输入是回文数那就可以直接输出了,我当时还以为还要再加一次才行,例如输入0,我还以为要输出:
> 0+0=0
> 0 is a palindromic number.

结果并不用,直接输出结果就可以了.

## 代码

先实现一个[1069 The Black Hole of Numbers](https://jiayaoo3o.github.io/2019/04/23/1069-The-Black-Hole-of-Numbers/)就已经写过的反转字符串的函数:

```c#
public static string Reverse(string input)
{
    char[] num = input.ToCharArray();
    Array.Reverse(num);
    return new string(num);
}
```

再实现一个用于将大数相加的函数:

```c#
public static string AddStrNumber(string input, string reversed)
{
    string sum = "";
    int flag = 0;//记录进位信息.
    for (int i = input.Length - 1; i >= 0; i--)
    {
        sum = (((int)input[i] - 48 + (int)reversed[i] - 48 + flag) % 10) + sum;
        flag = ((int)input[i] - 48 + (int)reversed[i] - 48 + flag) / 10;
    }
    return flag == 1 ? "1" + sum : sum;//最终结果如果有进位得在前面加1.
}
```

```c#
public static void Main()
{
    string input = Console.ReadLine();
    string sum = "";
    string reversed;
    for (int i = 0; i < 10; i++)
    {
        reversed = Reverse(input);
        if (input == reversed)
        {
            Console.WriteLine("{0} is a palindromic number.", input);
            return;
        }
        sum = AddStrNumber(input, reversed);
        Console.WriteLine("{0} + {1} = {2}", input, reversed, sum);
        input = sum;
    }
    Console.WriteLine("Not found in 10 iterations.");
}
```

