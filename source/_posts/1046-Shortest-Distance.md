---
title: 1046 Shortest Distance
date: 2019-04-20 23:55:39
tags: PAT
categories: [算法学习,PAT]
---

## 题目

The task is really simple: given *N* exits on a highway which forms a simple cycle, you are supposed to tell the shortest distance between any pair of exits.

### Input Specification:

Each input file contains one test case. For each case, the first line contains an integer *N* (in [3,10<sup>5</sup>]), followed by *N* integer distances D<sub>1</sub> D<sub>2</sub> ⋯ D<sub>N</sub>, where D<sub>i</sub> is the distance between the *i* -th and the (*i*+1)-st exits, and D<sub>N</sub> is between the N-th and the 1st exits. All the numbers in a line are separated by a space. The second line gives a positive integer M(≤10<sup>4</sup>), with M lines follow, each contains a pair of exit numbers, provided that the exits are numbered from 1 to N. It is guaranteed that the total round trip distance is no more than 10<sup>7</sup>.

### Output Specification:

For each test case, print your results in *M* lines, each contains the shortest distance between the corresponding given pair of exits.

### Sample Input:

```in
5 1 2 4 14 9
3
1 3
2 5
4 1
10
```

### Sample Output:

```out
3
10
7
```

<!-- more -->

## 题意

在一条环状高速公路上有很多节点,并且已知每个节点到下一个节点的距离,现在给定两个节点,要求求出节点的最近距离是多少.

## 思路

这道题我一开始的思路是直接顺时针求出A到B和B到A的距离,看看谁小,这样做思路并没有错,但是因为我求距离的方法是用一个for循环,获取每个节点上的距离,再累加,这样分别循环两次得到的结果,但是当测试案例有很多个节点的时候,这样做会太浪费时间,导致超时.

所以对于上面的超时,要进行的思路改进有两点:

- 不应该顺时针循环算两遍,因为高速公路是一个圆,只要算出A到B的距离,然后用总距离减去A到B距离,结果就是B到A了.

- 算A到B距离,没必要用循环累加每个节点的距离这种方式,我们用数组保存节点距离的时候,没必要将**节点与下一个节点的距离**保存到数组中,而是应该将**节点与第一个节点的距离**保存在数组中,例如例子中的:

  > 1 2 4 14 9

  是指当前节点,到下一个节点的距离,而我们保存数据的时候,可以保存为:

  > 0 1 3 7 21

  这是指每一个点到第一个点的距离,这样的话我们算A到B的距离就不用循环了,直接是第A个节点减去地B个节点的距离(下标0是第一个节点).

## 代码

先实现一个获得最短路径的函数,这个函数传入4个参数,分别是:保存各个节点数据的数组,需要计算的开始节点,需要计算的结束节点,还有总距离.

我们要做的是比较开始节点到结束节点之间的距离和总距离减去两节点之间的距离,谁更小就取哪个值.

```c#
public static int GetShortestDistance(int[] nodes, int beginNode, int endNode, int totalDistance)
{
    int distance = beginNode > endNode ? nodes[beginNode - 1] - nodes[endNode - 1] : nodes[endNode - 1] - nodes[beginNode - 1];
    return distance < (totalDistance - distance) ? distance : (totalDistance - distance);
}
```

```c#
public static void Main()
{
    string[] tokens = System.Console.ReadLine().Split();
    int nodesCount = int.Parse(tokens[0]);
    int[] nodes = new int[nodesCount];
    int totalDistance = 0;
    for (int i = 0; i < nodes.Length; i++)
    {
        nodes[i] = totalDistance;
        totalDistance += int.Parse(tokens[i + 1]);
    }
    int pairsCount = int.Parse(Console.ReadLine());
    for (int i = 0; i < pairsCount; i++)
    {
        string[] pairs = Console.ReadLine().Split();
        Console.WriteLine(GetShortestDistance(nodes, int.Parse(pairs[0]), int.Parse(pairs[1]), totalDistance));
    }
}
```

这里我其实还是有一个对于PAT的疑惑的,按照题目所说,不是应该等输入完了所有的节点对之后,再输出三个最短距离结果吗?为什么现在是输入一个节点对,马上输出一个结果,这样也可以呢?不影响程序判定的吗?