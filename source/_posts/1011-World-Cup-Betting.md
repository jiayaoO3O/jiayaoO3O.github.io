---
title: 1011 World Cup Betting
date: 2019-04-16 15:02:46
tags: PAT
categories: [算法学习,PAT]
---

## 题目

With the 2010 FIFA World Cup running, football fans the world over were becoming increasingly excited as the best players from the best teams doing battles for the World Cup trophy in South Africa. Similarly, football betting fans were putting their money where their mouths were, by laying all manner of World Cup bets.

Chinese Football Lottery provided a "Triple Winning" game. The rule of winning was simple: first select any three of the games. Then for each selected game, bet on one of the three possible results -- namely `W` for win, `T` for tie, and `L` for lose. There was an odd assigned to each result. The winner's odd would be the product of the three odds times 65%.

For example, 3 games' odds are given as the following:

```
 W    T    L
1.1  2.5  1.7
1.2  3.1  1.6
4.1  1.2  1.1
```

To obtain the maximum profit, one must buy `W` for the 3rd game, `T` for the 2nd game, and `T` for the 1st game. If each bet takes 2 yuans, then the maximum profit would be (4.1×3.1×2.5×65%−1)×2=39.31 yuans (accurate up to 2 decimal places).

### Input Specification:

Each input file contains one test case. Each case contains the betting information of 3 games. Each game occupies a line with three distinct odds corresponding to `W`, `T` and `L`.

### Output Specification:

For each test case, print in one line the best bet of each game, and the maximum profit accurate up to 2 decimal places. The characters and the number must be separated by one space.

### Sample Input:

```in
1.1 2.5 1.7
1.2 3.1 1.6
4.1 1.2 1.1
```

### Sample Output:

```out
T T W 39.31
```

<!-- more -->

## 题意

就是说世界杯到了,赌球的时候也到了,现在有一种赌球玩法,有三个队伍,他们的获得W(胜利),T(平手),L(失败)的概率是表格里的数据,我们买的时候,分别买三个队伍里面概率最高的,每个买2元,就可以通过下面的公式算出我们的最大利润:

> (4.1×3.1×2.5×65%−1)×2=39.31

现在给你三个队伍的数据,也是每个买2元,要你算出最大利润,并且要在输出结果时,分别说出你买了每个队的哪个情况,T T W 39.31是指第一个队伍买T,第二个队伍买T,第三个队伍买W,总利润39.31.

## 思路

这一题也很简单了,给你三个数据,让你找出数据里面最大的数,然后代入公式就行了.

## 代码

```c#
static void Main(string[] args)
{
    string[] teams1 = System.Console.ReadLine().Split();
    string[] teams2 = System.Console.ReadLine().Split();
    string[] teams3 = System.Console.ReadLine().Split();
    string[] WTL = { "W", "T", "L" };
    double[] t1 = new double[teams1.Length];
    double[] t2 = new double[teams2.Length];
    double[] t3 = new double[teams3.Length];
    for (int i = 0; i < t1.Length; i++)
    {
        t1[i] = double.Parse(teams1[i]);
        t2[i] = double.Parse(teams2[i]);
        t3[i] = double.Parse(teams3[i]);
    }
    double max = 0;
    int maxindex = 0;
    double socre = 1;
    for (int i = 0; i < t1.Length; i++)
    {
        if (t1[i] > max)
        {
            max = t1[i];
            maxindex = i;
        }
    }
    Console.Write(WTL[maxindex] + " ");
    socre *= max;
    max = 0;
    for (int i = 0; i < t2.Length; i++)
    {
        if (t2[i] > max)
        {
            max = t2[i];
            maxindex = i;
        }
    }
    Console.Write(WTL[maxindex] + " ");
    socre *= max;
    max = 0;
    for (int i = 0; i < t3.Length; i++)
    {
        if (t3[i] > max)
        {
            max = t3[i];
            maxindex = i;
        }
    }
    Console.Write(WTL[maxindex] + " ");
    socre *= max;
    socre = socre * 0.65 * 2 - 2;
    Console.Write(socre.ToString("0.00"));
}
```

