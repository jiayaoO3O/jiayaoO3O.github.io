---
title: 1108 Finding Average
date: 2019-04-30 20:10:25
tags: PAT
categories: [算法学习,PAT]
---

## 题目

The basic task is simple: given *N* real numbers, you are supposed to calculate their average. But what makes it complicated is that some of the input numbers might not be legal. A **legal** input is a real number in [−1000,1000] and is accurate up to no more than 2 decimal places. When you calculate the average, those illegal numbers must not be counted in.

### Input Specification:

Each input file contains one test case. For each case, the first line gives a positive integer *N* (≤100). Then *N* numbers are given in the next line, separated by one space.

### Output Specification:

For each illegal input number, print in a line `ERROR: X is not a legal number` where `X` is the input. Then finally print in a line the result: `The average of K numbers is Y` where `K` is the number of legal inputs and `Y` is their average, accurate to 2 decimal places. In case the average cannot be calculated, output `Undefined` instead of `Y`. In case `K` is only 1, output `The average of 1 number is Y` instead.

### Sample Input 1:

```in
7
5 -3.2 aaa 9999 2.3.4 7.123 2.35
```

### Sample Output 1:

```out
ERROR: aaa is not a legal number
ERROR: 9999 is not a legal number
ERROR: 2.3.4 is not a legal number
ERROR: 7.123 is not a legal number
The average of 3 numbers is 1.38
```

### Sample Input 2:

```in
2
aaa -9999
```

### Sample Output 2:

```out
ERROR: aaa is not a legal number
ERROR: -9999 is not a legal number
The average of 0 numbers is Undefined
```

<!-- more -->

## 题意

任务很简单,就是算所有数的平均数,但是要求是只算所有"合法"的数的平均数,规则为:

- 输入的数如果不是有理数,不合法.
- 输入的数如果大于1000或者小于-1000,不合法.
- 输入的数如果小数点后超过2位,不合法.

输出规则为:

- 如果有不合法的数,那就先逐行依次输出 `ERROR: X is not a legal number `
- 如果合法数为0,那就输出`The average of 0 numbers is Undefined`
- 如果合法数为1,那就输出`The average of 1 number is Y`,注意这里的number没有加s.
- 剩余情况,就输出`The average of K numbers is Y`,注意,平均数规定要精确到小数点后两位.

## 思路

这道题难度不高,我们要做的其实只有三件事情:

- 判断输入的某个数是否合法.
- 把不合法的数收集起来,然后输出.
- 记录合法数的和与合法数的数量,求平均值.

对于收集不合法的数,我们可以使用泛型队列,只要判断不合法,就将它入队,等到全部判断完了,再将不合法的数出队,这样可以保证顺序不出错.

## 代码

先实现一个用于判断输入是否合法的函数:

```c#
public static bool IsLegalInput(string input)
{
    double result = 0;
    if (double.TryParse(input, out result))//使用TryParse函数尝试转换,转换失败说明绝对不是合法数字,转换成功会将转换结果赋值给result.
    {
        if (result < -1000 || result > 1000)
        {
            return false;
        }
        else
        {
            //到了这个位置,开始判断是否是在小数点后两位之内,如果字符串的长度减去小数点的下标位置大于3,说明小数点后面不止两位了.
            //这里要注意,要用input判断而是不result来判断,因为类似1.000000这种字符串转换成double型的result后会直接变成1,所以要直接用字符串来做判断.
            //如果indexOf('.')的结果是-1那说明没有小数点,肯定是合法数字.
            if (input.IndexOf('.') > 0 && input.ToString().Length - input.ToString().IndexOf('.') > 3)
            {
                return false;
            }
            else
            {
                return true;
            }
        }
    }
    else
    {
        return false;
    }
}
```

```c#
public static void Main()
{
    int count = int.Parse(Console.ReadLine());
    int legalCount = 0;
    double legalSum = 0;
    string[] inputs = Console.ReadLine().Split();
    Queue<string> illegalInput = new Queue<string>();
    foreach (var input in inputs)
    {
        if (IsLegalInput(input))
        {
            legalSum += double.Parse(input);
            legalCount++;
        }
        else
        {
            illegalInput.Enqueue(input);
        }
    }
    while (illegalInput.Count != 0)
    {
        Console.WriteLine("ERROR: {0} is not a legal number", illegalInput.Dequeue());
    }
    if (legalCount == 0)
    {
        Console.WriteLine("The average of 0 numbers is Undefined");
    }
    if (legalCount == 1)
    {
        Console.WriteLine("The average of 1 number is {0}", legalSum.ToString("0.00"));
    }
    if (legalCount > 1)
    {
        Console.WriteLine("The average of {0} numbers is {1}", legalCount, (legalSum / legalCount).ToString("0.00"));
    }
}
```

