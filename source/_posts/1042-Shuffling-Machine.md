---
title: 1042 Shuffling Machine
date: 2019-04-19 11:05:44
tags: PAT
categories: [算法学习,PAT]
---

## 题目

Shuffling is a procedure used to randomize a deck of playing cards. Because standard shuffling techniques are seen as weak, and in order to avoid "inside jobs" where employees collaborate with gamblers by performing inadequate shuffles, many casinos employ **automatic shuffling machines**. Your task is to simulate a shuffling machine.

The machine shuffles a deck of 54 cards according to a given random order and repeats for a given number of times. It is assumed that the initial status of a card deck is in the following order:

```
S1, S2, ..., S13, 
H1, H2, ..., H13, 
C1, C2, ..., C13, 
D1, D2, ..., D13, 
J1, J2
```

where "S" stands for "Spade", "H" for "Heart", "C" for "Club", "D" for "Diamond", and "J" for "Joker". A given order is a permutation of distinct integers in [1, 54]. If the number at the *i*-th position is *j*, it means to move the card from position *i* to position *j*. For example, suppose we only have 5 cards: S3, H5, C1, D13 and J2. Given a shuffling order {4, 2, 5, 3, 1}, the result will be: J2, H5, D13, S3, C1. If we are to repeat the shuffling again, the result will be: C1, H5, S3, J2, D13.

### Input Specification:

Each input file contains one test case. For each case, the first line contains a positive integer *K* (≤20) which is the number of repeat times. Then the next line contains the given order. All the numbers in a line are separated by a space.

### Output Specification:

For each test case, print the shuffling results in one line. All the cards are separated by a space, and there must be no extra space at the end of the line.

### Sample Input:

```in
2
36 52 37 38 3 39 40 53 54 41 11 12 13 42 43 44 2 4 23 24 25 26 27 6 7 8 48 49 50 51 9 10 14 15 16 5 17 18 19 1 20 21 22 28 29 30 31 32 33 34 35 45 46 47
```

### Sample Output:

```out
S7 C11 C10 C12 S1 H7 H8 H9 D8 D9 S11 S12 S13 D10 D11 D12 S3 S4 S6 S10 H1 H2 C13 D2 D3 D4 H6 H3 D13 J1 J2 C1 C2 C3 C4 D1 S5 H5 H11 H12 C6 C7 C8 C9 S2 S8 S9 H10 D5 D6 D7 H4 H13 C5
```

<!-- more -->

## 题意

给你一副顺序完整的扑克牌,要你按照一定的规则去洗牌.

例如给你5张牌:

> S3, H5, C1, D13, J2

给你一个洗牌顺序:

> {4, 2, 5, 3, 1}

那洗牌一次的结果就是,把对应位置的牌放到对应的洗牌顺序位置:

> J2, H5, D13, S3, C1

再用这个规则洗牌一次,结果就是:

> C1, H5, S3, J2, D13

现在要求给你一个洗牌次数K,再一个洗牌顺序,让你把按照洗牌顺序的54张洗牌结果输出.

## 思路

这道题,只要创建三个数组:一个指令数组orders,一个扑克牌数组cards,一个洗牌结果数组result,然后将洗牌指令数组内的数据作为结果数组的下标,存放扑克牌就好了.即:

> result[orders[i] - 1] = cards[i];

例如现在i=0,对于上面那五张牌,order[0]-1就是4-1=3,所以result[3]=cards[0],这就可以将第一张扑克牌S3放到第4位了.

## 代码

创建一个洗牌数组,放入一个洗牌指令,放入一个扑克牌,返回洗牌结果.

```c#
public static string[] ShufflingMachines(int[] orders, string[] cards)
{
    string[] result = new string[cards.Length];
    for (int i = 0; i < orders.Length; i++)
    {
        result[orders[i] - 1] = cards[i];
    }
    return result;
}
```

```c#
public static void Main()
{
    int repeatTimes = int.Parse(System.Console.ReadLine());
    string[] tokens = System.Console.ReadLine().Split();
    int[] orders = new int[tokens.Length];
    string[] cards = new string[54];
    for (int i = 0; i < 54; i++)
    {
        orders[i] = int.Parse(tokens[i]);
        if (i < 13)
        {
            cards[i] = "S" + (i + 1).ToString();
        }
        if (i >= 13 && i < 26)
        {
            cards[i] = "H" + (i % 13 + 1).ToString();
        }
        if (i >= 26 && i < 39)
        {
            cards[i] = "C" + (i % 13 + 1).ToString();
        }
        if (i >= 39 && i < 52)
        {
            cards[i] = "D" + (i % 13 + 1).ToString();
        }
        if (i >= 52)
        {
            cards[i] = "J" + (i % 13 + 1).ToString();
        }
    }
    for (int i = 0; i < repeatTimes; i++)
    {
        cards = ShufflingMachines(orders, cards);
    }
    for (int i = 0; i < cards.Length; i++)
    {
        if (i != cards.Length - 1)
        {
            Console.Write(cards[i] + " ");
        }
        else
        {
            Console.WriteLine(cards[i]);
        }
    }
}
```

