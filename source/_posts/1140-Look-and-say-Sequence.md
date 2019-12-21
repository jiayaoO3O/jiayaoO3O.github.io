---
title: 1140 Look-and-say Sequence
date: 2019-05-09 10:24:28
tags: PAT
categories: [算法学习,PAT]
---

## 题目

Look-and-say sequence is a sequence of integers as the following:

```
D, D1, D111, D113, D11231, D112213111, ...
```

where `D` is in [0, 9] except 1. The (n+1)st number is a kind of description of the nth number. For example, the 2nd number means that there is one `D` in the 1st number, and hence it is `D1`; the 2nd number consists of one `D`(corresponding to `D1`) and one 1 (corresponding to 11), therefore the 3rd number is `D111`; or since the 4th number is `D113`, it consists of one `D`, two 1's, and one 3, so the next number must be `D11231`. This definition works for `D` = 1 as well. Now you are supposed to calculate the Nth number in a look-and-say sequence of a given digit `D`.

### Input Specification:

Each input file contains one test case, which gives `D` (in [0, 9]) and a positive integer N (≤ 40), separated by a space.

### Output Specification:

Print in a line the Nth number in a look-and-say sequence of `D`.

### Sample Input:

```in
1 8
```

### Sample Output:

```out
1123123111
```

<!-- more -->

## 题意

外观序列是指通过将序列的每个数的数目都"说出来"而形成的序列

```
D
D1 //D有1个
D111 //D有1个,1有1个
D113 //D有1个,1有3个
D11231 //D有1个,1有2个,3有1个
D112213111 //D有1个,1有2个,2有1个,3有1个,1有1个
...
```

现在输入一个数D和一个重复次数N,要求输出第N个重复序列.

## 思路

对于每一次循环的一个序列,比较当前的字符和上一个字符是否相同,如果相同那就让计数器+1,不一样的时候说明这个字符已经完结了,将这个答案拼接到一个临时结果上,最后将临时结果再次放入循环.

## 代码

```c#
public static void Main()
{
    string[] lines = Console.ReadLine().Split();
    string input = lines[0];
    int count = int.Parse(lines[1]);
    System.Text.StringBuilder result = new System.Text.StringBuilder("");
    while (--count > 0)//这里使用--count,即假设输入的是8,只需要循环7次,因为输入的D已经算是第一次了.
    {
        int nowCount = 1;
        for (int i = 1; i < input.Length; i++)
        {
            if (input[i] == input[i - 1])
            {
                nowCount++;
            }
            else
            {
                result.Append(input[i - 1] + nowCount.ToString());
                //将result定义成StringBuilder可以大幅度降低花费的时间.
                //如果直接用string类型的result+=input[i - 1] + nowCount.ToString()来拼接结果,将会导致超时!!!
                nowCount = 1;
            }
        }
        result.Append(input[input.Length - 1] + nowCount.ToString());
        input = result.ToString();//将临时结果赋值给input进行下次循环.
        result.Clear();
    }
    Console.WriteLine(input);
}
```

