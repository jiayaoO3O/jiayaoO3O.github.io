---
title: 1007 Maximum Subsequence Sum
date: 2019-05-28 16:38:08
tags: PAT
categories: [算法学习,PAT]
---
## 题目

Given a sequence of *K* integers { *N1*, *N2*, ..., *NK* }. A continuous subsequence is defined to be { *Ni*, *Ni*+*1*, ..., *Nj* } where 1≤*i*≤*j*≤*K*. The Maximum Subsequence is the continuous subsequence which has the largest sum of its elements. For example, given sequence { -2, 11, -4, 13, -5, -2 }, its maximum subsequence is { 11, -4, 13 } with the largest sum being 20.

Now you are supposed to find the largest sum, together with the first and the last numbers of the maximum subsequence.

### Input Specification:

Each input file contains one test case. Each case occupies two lines. The first line contains a positive integer *K* (≤10000). The second line contains *K*numbers, separated by a space.

### Output Specification:

For each test case, output in one line the largest sum, together with the first and the last numbers of the maximum subsequence. The numbers must be separated by one space, but there must be no extra space at the end of a line. In case that the maximum subsequence is not unique, output the one with the smallest indices *i* and *j* (as shown by the sample case). If all the *K* numbers are negative, then its maximum sum is defined to be 0, and you are supposed to output the first and the last numbers of the whole sequence.

### Sample Input:

```in
10
-10 1 2 3 4 -5 -23 3 7 -21
```

### Sample Output:

```out
10 1 4
```

<!-- more -->

## 题意

给定一个整型序列,要求出这个序列的最大子序和,最大子序和的开始和结束这三个元素.

注意,如果整个序列全部元素都为负数,那就最大子序和为0,开始和结束分别分该序列的起始和结束.

## 思路

这道题和leetcode的这道[53.最大子序和](https://jiayaoo3o.github.io/2019/05/06/53-最大子序和/)解法基本是一致的,如果添加数组的下一个数导致这个和成为了负数,说明这个序列能到达的最大的长度已经结束了,因为加上下一个数只会让和变得更小,新的序列应该从下一个数开始尝试.

但是这一道题的另一个不一样的点是还要记录最大子序和的起始和结束.

## 代码
```c#
public static void Main()
{
    int count = int.Parse(Console.ReadLine());
    string[] lines = Console.ReadLine().Split();
    int[] inputs = new int[count];
    int begin = 0, end = 0, temp = 0;//temp用来记录新的子串的起始位置.
    int sum = 0;
    int max = -1;//max的初值设置为负数.
    for (int i = 0; i < count; i++)
    {
        inputs[i] = int.Parse(lines[i]);
        sum = sum + inputs[i];
        if (sum < 0)
        {
            sum = 0;
            temp = i + 1;
        }
        else
        {
            if (sum > max)
            {
                max = sum;
                begin = temp;
                end = i;
            }
        }
    }
    if (max < 0)//用max<0来判断是否全为负数是因为只要max不小于0,那说明整个序列里面起码得有一个非负数的元素.
    {
        Console.WriteLine("{0} {1} {2}", 0, inputs[0], inputs[count - 1]);
    }
    else
    {
        Console.WriteLine("{0} {1} {2}", max, inputs[begin], inputs[end]);
    }
}
```