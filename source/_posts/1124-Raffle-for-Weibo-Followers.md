---
title: 1124 Raffle for Weibo Followers
date: 2019-05-04 14:32:01
tags: PAT
categories: [算法学习,PAT]
---

## 题目

John got a full mark on PAT. He was so happy that he decided to hold a raffle（抽奖） for his followers on Weibo -- that is, he would select winners from every N followers who forwarded his post, and give away gifts. Now you are supposed to help him generate the list of winners.

### Input Specification:

Each input file contains one test case. For each case, the first line gives three positive integers M (≤ 1000), N and S, being the total number of forwards, the skip number of winners, and the index of the first winner (the indices start from 1). Then M lines follow, each gives the nickname (a nonempty string of no more than 20 characters, with no white space or return) of a follower who has forwarded John's post.

Note: it is possible that someone would forward more than once, but no one can win more than once. Hence if the current candidate of a winner has won before, we must skip him/her and consider the next one.

### Output Specification:

For each case, print the list of winners in the same order as in the input, each nickname occupies a line. If there is no winner yet, print `Keep going...` instead.

### Sample Input 1:

```in
9 3 2
Imgonnawin!
PickMe
PickMeMeMeee
LookHere
Imgonnawin!
TryAgainAgain
TryAgainAgain
Imgonnawin!
TryAgainAgain
```

### Sample Output 1:

```out
PickMe
Imgonnawin!
TryAgainAgain
```

### Sample Input 2:

```in
2 3 5
Imgonnawin!
PickMe
```

### Sample Output 2:

```out
Keep going...
```

<!-- more -->

## 题意

john想在微博搞一个粉丝抽奖,现在要做一个抽奖程序,要求如下:

- 先输入三个数M,N,S
  M是指粉丝总人数.
  N是指下一个粉丝所在的位置的间隔.
  S是指第一个中奖的位置(从1开始数).
  例如9 3 2是指粉丝一共9个人,第2个人就是第一个中奖的位置,间隔是3即第2个往后数3个人也就是第5个粉丝,第8个粉丝也中奖位置.
  
- 粉丝名单里面有的粉丝可能不止出现了一次,如果选中的粉丝已经中奖过了,则往后推一个位置.
- 如果没有粉丝中奖,则输出`Keep going...`

## 思路

这道题难度不算高,要做的只是判断某个粉丝是否中过奖,中过了就跳到下一个就好了.这里可以有两个思路来判断粉丝是否中过奖,1可以用哈希表将中奖的粉丝设置为1,然后每次检查哈希表中的值是否为1来判断是否曾经中奖;2可以用泛型列表List的Contains()函数来判断粉丝是否已经存在于获奖名单里.

## 代码

```c#
public static void Main()
{
    string line = Console.ReadLine();
    int totalForwards = int.Parse(line.Split()[0]);
    int skipNumber = int.Parse(line.Split()[1]);
    int firstWinner = int.Parse(line.Split()[2]);
    if (totalForwards < firstWinner)//没有中奖者的情况只有一种,那就是粉丝数比第一个获奖位置还要小.
    {
        Console.WriteLine("Keep going...");
    }
    else
    {
        List<string> winners = new List<string>();//用来判断粉丝曾经是否已经获奖
        string follower;
        for (int i = 1; i <= totalForwards ; i++)
        {
            follower = Console.ReadLine();
            if (i == firstWinner)
            {
                if (winners.Contains(follower))//如果粉丝已经获奖过,那就将位置+1;
                {
                    firstWinner += 1;
                }
                else
                {
                    winners.Add(follower);//如果没有获奖那就将他添加入获奖名单.
                    Console.WriteLine(follower);
                    firstWinner += skipNumber;//将获奖位置向后挪动skipNumber个位置.
                }
            }
        }
    }
}
```

