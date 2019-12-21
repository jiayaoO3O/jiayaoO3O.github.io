---
title: 1116 Come on! Let's C
date: 2019-05-03 16:44:03
tags: PAT
categories: [算法学习,PAT]
---

## 题目

"Let's C" is a popular and fun programming contest hosted by the College of Computer Science and Technology, Zhejiang University. Since the idea of the contest is for fun, the award rules are funny as the following:

- 0.The Champion will receive a "Mystery Award" (such as a BIG collection of students' research papers...).
- 1.Those who ranked as a prime number will receive the best award -- the Minions (小黄人)!
- 2.Everyone else will receive chocolates.

Given the final ranklist and a sequence of contestant ID's, you are supposed to tell the corresponding awards.

### Input Specification:

Each input file contains one test case. For each case, the first line gives a positive integer N (≤10<sup>4</sup>), the total number of contestants. Then N lines of the ranklist follow, each in order gives a contestant's ID (a 4-digit number). After the ranklist, there is a positive integer K followed by K query ID's.

### Output Specification:

For each query, print in a line `ID: award` where the award is `Mystery Award`, or `Minion`, or `Chocolate`. If the ID is not in the ranklist, print `Are you kidding?`instead. If the ID has been checked before, print `ID: Checked`.

### Sample Input:

```in
6
1111
6666
8888
1234
5555
0001
6
8888
0001
1111
2222
8888
2222
```

### Sample Output:

```out
8888: Minion
0001: Chocolate
1111: Mystery Award
2222: Are you kidding?
8888: Checked
2222: Are you kidding?
```

<!-- more -->

## 题意

给定一个排名列表,再给定一个查询列表,输出查询列表中每个id对应的排名所能获得的奖品,规则如下:

- 排名列表的排名从1开始
- 如果id排名第一,将获得`Mystery Award`
- 如果id排名是素数,将获得`Minion`
- 其他名次,将获得`Chocolate`
- 如果查询用户没有名次,输出`Are you kidding?`
- 如果查询的用户已经查询过了,输出`Checked`

## 思路

这道题是相对简单的一道题,用泛型字典做哈希表,存放用户id和对应的排名值,如果被查询过了,就将排名值改为-1.

## 代码

先实现一个检查素数函数:

```c#
public static bool IsPrime(int input)
{
    if (input <= 1)
    {
        return false;
    }
    for (int i = 2; i <= Math.Sqrt(input); i++)
    {
        if (input % i == 0)
        {
            return false;
        }
    }
    return true;
}
```

```c#
public static void Main()
{
    Dictionary<string, int> rankList = new Dictionary<string, int>();
    int count = int.Parse(Console.ReadLine());
    for (int i = 0; i < count; i++)
    {
        rankList.Add(Console.ReadLine(), i + 1);
    }
    count = int.Parse(Console.ReadLine());
    string queryID;
    for (int i = 0; i < count; i++)
    {
        queryID = Console.ReadLine();
        if (!rankList.ContainsKey(queryID))
        {
            Console.WriteLine("{0}: Are you kidding?", queryID);
        }
        else
        {
            if (rankList[queryID] == -1)
            {
                Console.WriteLine("{0}: Checked", queryID);
                continue;
            }
            if (rankList[queryID] == 1)
            {
                Console.WriteLine("{0}: Mystery Award", queryID);
            }
            else
            {
                if (IsPrime(rankList[queryID]))
                {
                    Console.WriteLine("{0}: Minion", queryID);
                }
                else
                {
                    Console.WriteLine("{0}: Chocolate", queryID);
                }
            }
            rankList[queryID] = -1;
        }
    }
}
```

