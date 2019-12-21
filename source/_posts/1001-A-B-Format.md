---
title: 1001 A+B Format
date: 2019-04-16 11:00:09
tags: PAT
categories: [算法学习,PAT]
---

## 题目

Calculate *a*+*b* and output the sum in standard format -- that is, the digits must be separated into groups of three by commas (unless there are less than four digits).

### Input Specification:

Each input file contains one test case. Each case contains a pair of integers *a* and *b* where −10<sup>6</sup>≤*a*,*b*≤10<sup>6</sup>. The numbers are separated by a space.

### Output Specification:

For each test case, you should output the sum of *a* and *b* in one line. The sum must be written in the standard format.

### Sample Input:

```in
-1000000 9
```

### Sample Output:

```out
-999,991
```

<!-- more -->

## 题意

把a和b两个数加起来,结果要用规定格式表示,规定格式是:从右往左数,每隔3位要加个逗号.

## 思路

这道题如果直接从右往左硬撸会很不优雅,我一开始写的时候发现,怎么写都很啰嗦,结果在网上找到了[这篇答案](https://www.liuchuo.net/archives/1888),发现里面有个判断逗号的方法相当巧妙:

```c#
if ((i + 1) % 3 == s.Length % 3 && i != s.Length - 1)
	{
     	Console.Write(",");
    }
```

当我看到这个公式我就意识到我走错路了,不应该硬写,而是应该找其中的数学规律.

为什么当

> (i + 1) % 3 == s.Length % 3

的时候,可以放一个逗号呢?

我们来尝试找一下规律:

- 1,234,567 第**1**位,第**4**位后面加逗号,全长**7**位
- 12,234 第**2**位后面加逗号,全长**5**位
- 123,456 第**3**位后面加逗号,全长**6**位

也就是说,除了第一个数的前面,和最后一个数的后面,我们从左往右数的第一个逗号应该在s.Length % 3这个位置,下一个逗号应该在这个位置往后走3位的位置,也就是说:

- 第一个逗号:(i+1)==s.length%3
- 第二个逗号:(i+1)+3+3==s.length%3
- 第三个逗号:(i+1)+3+3+3==s.length%3
- 第n个逗号:(i+1)+3n==s.length%3

这时候你会发现,加再多的3n结果再%3还是等于i+1,((i+1)+3n)%3==i+1,所以我们最后的公式就是:

> (i + 1) % 3 == s.Length % 3

## 代码

```c#
static void Main(string[] args)
        {
            string[] input = Console.ReadLine().Split(' ');
            int a = Convert.ToInt32(input[0]);
            int b = Convert.ToInt32(input[1]);
            string s = (a + b).ToString();
            for (int i = 0; i < s.Length; i++)
            {
                Console.Write(s[i]);
                if (s[i] == '-')
                {
                    continue;
                }
                if ((i + 1) % 3 == s.Length % 3 && i != s.Length - 1)
                {
                    Console.Write(",");
                }
            }
        }      
```