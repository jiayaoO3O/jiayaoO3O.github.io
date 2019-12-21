---
title: 1092 To Buy or Not to Buy
date: 2019-04-28 12:58:25
tags: PAT
categories: [算法学习,PAT]
---

## 题目

Eva would like to make a string of beads with her favorite colors so she went to a small shop to buy some beads. There were many colorful strings of beads. However the owner of the shop would only sell the strings in whole pieces. Hence Eva must check whether a string in the shop contains all the beads she needs. She now comes to you for help: if the answer is `Yes`, please tell her the number of extra beads she has to buy; or if the answer is `No`, please tell her the number of beads missing from the string.

For the sake of simplicity, let's use the characters in the ranges [0-9], [a-z], and [A-Z] to represent the colors. For example, the 3rd string in Figure 1 is the one that Eva would like to make. Then the 1st string is okay since it contains all the necessary beads with 8 extra ones; yet the 2nd one is not since there is no black bead and one less red bead.

![Figure 1](https://images.ptausercontent.com/b7e2ffa6-8819-436d-ad79-a41263abe914.jpg)

### Input Specification:

Each input file contains one test case. Each case gives in two lines the strings of no more than 1000 beads which belong to the shop owner and Eva, respectively.

### Output Specification:

For each test case, print your answer in one line. If the answer is `Yes`, then also output the number of extra beads Eva has to buy; or if the answer is `No`, then also output the number of beads missing from the string. There must be exactly 1 space between the answer and the number.

### Sample Input 1:

```in
ppRYYGrrYBR2258
YrR8RrY
```

### Sample Output 1:

```out
Yes 8
```

### Sample Input 2:

```in
ppRYYGrrYB225
YrR8RrY
```

### Sample Output 2:

```out
No 2
```

<!-- more -->

## 题意

Eva想要买各种颜色的珠子做手链,但是商店老板只卖整条的,不肯单卖,所以现在要判断,老板提供的整条手链里面有没有我们需要的全部珠子:

- 如果有,就输出**Yes 剩下用不上的珠子的数目**.
- 如果没有,就输出**No 缺少了多少颗需要的珠子**.

## 思路

这道题很明显应该用哈希表来解决.老板有的珠子,我们就在哈希表对应的位置+1;我们需要的珠子,就在对应的哈希表位置-1.

最终哈希表内如果有负数,说明老板的珠子不能满足我们的需要,各个负数之和就是我们缺少的珠子的数目;

如果没有负数,说明老板的珠子能够满足我们的需求,哈希表内各个正数之和就是我们用不上的多余的珠子数目;

## 代码

```c#
public static void Main()
{
    string shopBeads = Console.ReadLine();
    string needBeads = Console.ReadLine();
    int missingBeads = 0;
    int extraBeads = 0;
    int[] colors = new int[128];
    foreach (var bead in shopBeads)
    {
        colors[bead]++;
    }
    foreach (var bead in needBeads)
    {
        colors[bead]--;
    }
    foreach (var bead in shopBeads + needBeads)
    {
        if (colors[bead] > 0)
        {
            extraBeads += colors[bead];
        }
        else
        {
            missingBeads += colors[bead];
        }
        colors[bead] = 0;//已经算过的哈希表位置要将它置零,避免重复计算.
    }
    if (missingBeads < 0)
    {
        Console.WriteLine("No {0}", 0 - missingBeads);
    }
    else
    {
        Console.WriteLine("Yes {0}", extraBeads);
    }
}
```

