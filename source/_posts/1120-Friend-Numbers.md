---
title: 1120 Friend Numbers
date: 2019-05-03 20:03:50
tags: PAT
categories: [算法学习,PAT]
---

##  题目

Two integers are called "friend numbers" if they share the same sum of their digits, and the sum is their "friend ID". For example, 123 and 51 are friend numbers since 1+2+3 = 5+1 = 6, and 6 is their friend ID. Given some numbers, you are supposed to count the number of different frind ID's among them.

### Input Specification:

Each input file contains one test case. For each case, the first line gives a positive integer N. Then N positive integers are given in the next line, separated by spaces. All the numbers are less than 10<sup>4</sup>.

### Output Specification:

For each case, print in the first line the number of different frind ID's among the given integers. Then in the second line, output the friend ID's in increasing order. The numbers must be separated by exactly one space and there must be no extra space at the end of the line.

### Sample Input:

```in
8
123 899 51 998 27 33 36 12
```

### Sample Output:

```out
4
3 6 9 26
```

<!-- more -->

## 题意

如果两个整数的各位数字之和相同,那这两个数字就是"友谊数",他们的和叫做"友谊ID".现在给定一个数字序列,让你输出他们的各个友谊ID的总数,并且在下一行从小到大输出各个友谊ID.

## 思路

这道题很多人会直接用哈希表set来做,但是其实还有更简单的方法,因为这些输入的整数不会大于10000,也就是说最大也就是9999,而友谊id最大也就可能是4个9即36,所以我们完全可以直接用整型数组做哈希表而不用直接创建set.

## 代码

先创建一个逐位数字求和的函数:

```c#
public static int GetDigitsSum(int input)
{
    int result = 0;
    if (input == 0)
    {
        return 0;
    }
    while (input != 0)
    {
        result += input % 10;
        input /= 10;
    }
    return result;
}
```

```c#
public static void Main()
{
    Console.ReadLine();
    string[] inputs = Console.ReadLine().Split();
    int[] friendIDs = new int[40];//创建一个长度为40的整型数组做哈希表,其实只要33个位置就够了.
    int friendID = 0;
    int count = 0;
    foreach (var input in inputs)
    {
        friendID = GetDigitsSum(int.Parse(input));
        if (friendIDs[friendID] != 1)
        {
            friendIDs[friendID] = 1;//将对应的ID位置数值值为1.
            count++;
        }
    }
    Console.WriteLine(count);
    for (int i = 0; i < friendIDs.Length; i++)
    {
        if (friendIDs[i] == 1)
        {
            count--;
            Console.Write(count == 0 ? i.ToString() : i + " ");
        }
    }
}
```

