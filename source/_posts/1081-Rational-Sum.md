---
title: 1081 Rational Sum
date: 2019-04-26 22:31:33
tags: PAT
categories: [算法学习,PAT]
---

## 题目

Given *N* rational numbers in the form `numerator/denominator`, you are supposed to calculate their sum.

### Input Specification:

Each input file contains one test case. Each case starts with a positive integer *N* (≤100), followed in the next line *N* rational numbers `a1/b1 a2/b2 ...` where all the numerators and denominators are in the range of **long int**. If there is a negative number, then the sign must appear in front of the numerator.

### Output Specification:

For each test case, output the sum in the simplest form `integer numerator/denominator` where `integer` is the integer part of the sum, `numerator` <`denominator`, and the numerator and the denominator have no common factor. You must output only the fractional part if the integer part is 0.

### Sample Input 1:

```in
5
2/5 4/15 1/30 -2/60 8/3
```

### Sample Output 1:

```out
3 1/3
```

### Sample Input 2:

```in
2
4/3 2/3
```

### Sample Output 2:

```out
2
```

### Sample Input 3:

```in
3
1/3 -1/6 1/8
```

### Sample Output 3:

```out
7/24
```

<!-- more -->

## 题意

计算分数加法,要求如下:

- 分数的结果一定要是最简分数.
- 分子如果大于分母,则要化简为带分数,即**c又a分之b**的整数和分数形式,.
- 当整数为0,只输出分数部分.
- 当分数为0,只输出整数部分.
- 当都为0.输出0.

## 思路

这道题第一眼看到可能会觉得很乱,因为这个题不仅要求和,还要化简,化简之后还要判定分子是否大于分母,看起来觉得很乱,但是只要仔细思考,就知道,这道题的重点其实只有4个

- 为了化简,我们需要约分,而约分要用到最大公因数,所以第一个问题是,怎么快速得到最大公因数.
- 如何进行分数的求和.
- 分子比分母大的假分数如何化简成整数和分数形式的带分数.
- 如何防止分子或者分母超出long int范围.

第一个点,我们可以用[辗转相除法](https://zh.wikipedia.org/wiki/%E8%BE%97%E8%BD%AC%E7%9B%B8%E9%99%A4%E6%B3%95)获得最大公因数,得到了最大公因数,就可以对分数进行化简.

第二个点,我们直接使用分数加法公式,分子交叉相乘再相加得到分子的和,分母相乘通分.

第三个点,我们其实并不需要判断分子是否大于分母,只要将分子除以分母作为整数部分即可,如果分子比分母小,那分子除以分母的结果就是0,而分子%分母的结果,就是新分子.

第四个点,为了防止溢出,分数求和之后的结果要马上进行约分化简,而不能等到所有分数相加完了再化简.

## 代码

先创建一个获得最大公因数的函数:

```c#
public static long GetGreatestCommonDivisor(long numerator, long denominator)
{
    return denominator == 0 ? Math.Abs(numerator) : GetGreatestCommonDivisor(denominator, numerator % denominator);
    //辗转相除法的递归形式.
}
```

再创建一个分数求和的函数,这个函数传入两个分数,返回一个**已经化简了**的分数:

```c#
public static long[] GetRationalSum(long[] rationalSum, long[] rationalNumber)
{
    long greatestCommonDivisor = GetGreatestCommonDivisor(rationalNumber[0], rationalNumber[1]);//取得最大公约数.
    rationalNumber[0] = rationalNumber[0] / greatestCommonDivisor;//先将分数化简.
    rationalNumber[1] = rationalNumber[1] / greatestCommonDivisor;
    rationalSum[0] = rationalSum[0] * rationalNumber[1] + rationalSum[1] * rationalNumber[0];//分子交叉相乘
    rationalSum[1] = rationalSum[1] * rationalNumber[1];//分母通分.
    greatestCommonDivisor = GetGreatestCommonDivisor(rationalSum[0], rationalSum[1]);//获得求和结果的最大公约数.
    rationalSum[0] = rationalSum[0] / greatestCommonDivisor;//将结果也化简.
    rationalSum[1] = rationalSum[1] / greatestCommonDivisor;
    return rationalSum;
}
```

```c#
public static void Main()
{
    Console.ReadLine();//第一个数字没用到.
    string[] inputs = Console.ReadLine().Split();//用空格分割分数.
    long[] rationalSum = new long[2] { 0, 1 };//先初始化一个存放结果的分数为0/1.
    long[] rationalNumber = new long[2];
    long integer = 0;
    foreach (var input in inputs)
    {
        rationalNumber[0] = long.Parse(input.Split('/')[0]);//对于每个分数,用/分割分子分母.
        rationalNumber[1] = long.Parse(input.Split('/')[1]);
        rationalSum = GetRationalSum(rationalSum, rationalNumber);
    }
    integer = rationalSum[0] / rationalSum[1];//获得整数部分.
    rationalSum[0] = rationalSum[0] % rationalSum[1];//更新分子.
    if (integer == 0)
    {
        if (rationalSum[0] == 0)
        {
            Console.WriteLine("0");
        }
        else
        {
            Console.WriteLine("{0}/{1}", rationalSum[0], rationalSum[1]);
        }
    }
    else
    {
        if (rationalSum[0] == 0)
        {
            Console.WriteLine(integer);
        }
        else
        {
            Console.WriteLine("{0} {1}/{2}", integer, rationalSum[0], rationalSum[1]);
        }
    }
}
```

