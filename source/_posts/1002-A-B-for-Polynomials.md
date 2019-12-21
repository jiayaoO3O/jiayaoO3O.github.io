---
title: 1002 A+B for Polynomials
date: 2019-05-10 11:27:58
tags: PAT
categories: [算法学习,PAT]
---

## 题目

This time, you are supposed to find *A*+*B* where *A* and *B* are two polynomials.

### Input Specification:

Each input file contains one test case. Each case occupies 2 lines, and each line contains the information of a polynomial:

*K* *N1* *aN1* *N2* *aN2* ... *NK* *aNK*

where *K* is the number of nonzero terms in the polynomial, *Ni* and *aNi* (*i*=1,2,⋯,*K*) are the exponents and coefficients, respectively. It is given that 1≤*K*≤10，0≤*NK*<⋯<*N*2<*N*1≤1000.

### Output Specification:

For each test case you should output the sum of *A* and *B* in one line, with the same format as the input. Notice that there must be NO extra space at the end of each line. Please be accurate to 1 decimal place.

### Sample Input:

```in
2 1 2.4 0 3.2
2 2 1.5 1 0.5
```

### Sample Output:

```out
3 2 1.5 1 2.9 0 3.2
```

<!-- more -->

## 题意

计算两个多项式的和,注意这里每一个输入的多项式最多只会有10项,但是多项式最大的项能到第1000项.

## 思路

用一个长度为1001的整型数组作为哈希表,下标表示第几项,值代表系数的值.

## 代码

```c#
public static void Main()
{
    string[] A = Console.ReadLine().Split();
    string[] B = Console.ReadLine().Split();
    double[] polynomialInfo = new double[1001];//定义一个用于存放系数信息的哈希表.
    for (int i = 1; i < A.Length - 1; i = i + 2)
    {
        polynomialInfo[int.Parse(A[i])] = double.Parse(A[i + 1]);
    }
    for (int i = 1; i < B.Length - 1; i += 2)
    {
        polynomialInfo[int.Parse(B[i])] += double.Parse(B[i + 1]);
    }
    int count = 0;
    string result = "";
    for (int i = 0; i < polynomialInfo.Length; i++)//这里的循环1000个位置只有最多10个是有用的,但是没有必要进行优化,因为我试了,时间都差不多.
    {
        if (polynomialInfo[i] != 0)
        {
            result = " " + i + " " + polynomialInfo[i].ToString("0.0") + result;
            count++;
        }
    }
    Console.Write(count + result);
}
```

