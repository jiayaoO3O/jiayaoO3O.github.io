---
title: 1009 Product of Polynomials
date: 2019-05-28 20:08:50
tags: PAT
categories: [算法学习,PAT]
---
## 题目

This time, you are supposed to find *A*×*B* where *A* and *B* are two polynomials.

### Input Specification:

Each input file contains one test case. Each case occupies 2 lines, and each line contains the information of a polynomial:

*K* *N1* *aN1* *N2* *aN2* ... *NK* *aNK*

where *K* is the number of nonzero terms in the polynomial, *Ni* and *aNi* (*i*=1,2,⋯,*K*) are the exponents and coefficients, respectively. It is given that 1≤*K*≤10, 0≤*NK*<⋯<*N*2<*N*1≤1000.

### Output Specification:

For each test case you should output the product of *A* and *B* in one line, with the same format as the input. Notice that there must be **NO** extra space at the end of each line. Please be accurate up to 1 decimal place.

### Sample Input:

```in
2 1 2.4 0 3.2
2 2 1.5 1 0.5
```

### Sample Output:

```out
3 3 3.6 2 6.0 1 1.6
```

<!-- more -->
## 题意

给定两个多项式,其中N是指数,aNi是对应的系数,现在要算两个多项式的乘积.

系数精确到小数点后一位.

## 思路

这道题要说难吧,其实不难,只要将两个多项式用泛型字典存起来,再定义一个排序的泛型字典接收他们相乘的结果就可以了.

但是里面有坑,尤其是第0个测试点,就是一个坑,因为题目要求精确到小数点的后1位,但是假如有两个系数分别是double类型的0.1和0.4,那他们相乘的结果是0.04保留一位有效数字那就是0.0了,所以虽然这个乘积不是0,但是是要被四舍五入舍去的,所以我们最后还要有一步判断最后的值是否>=0.05.

## 代码
```c#
public static void Main()
{
    Dictionary<int, double> A = new Dictionary<int, double>();
    Dictionary<int, double> B = new Dictionary<int, double>();
    var product = new SortedDictionary<int, double>();//注意这里定义了一个排序字典,会自动帮我们把里面的数据排序.
    string[] inputsA = Console.ReadLine().Split();
    string[] inputsB = Console.ReadLine().Split();
    string result = "";
    for (int i = 1; i < inputsA.Length - 1; i += 2)
    {
        A.Add(int.Parse(inputsA[i]), double.Parse(inputsA[i + 1]));
    }
    for (int i = 1; i < inputsB.Length - 1; i += 2)
        //这里其实可以不需要B字典,直接在循环里面计算A多项式的每个项与inputB相乘也是可以的.
        //但是我发现速度相差不大,就直接定义两个字典了,反正代码更容易读.
    {
        B.Add(int.Parse(inputsB[i]), double.Parse(inputsB[i + 1]));
    }
    foreach (var a in A)
    {
        foreach (var b in B)
        {
            if (!product.ContainsKey(a.Key + b.Key))
            {
                product.Add(a.Key + b.Key, a.Value * b.Value);
            }
            else
            {
                product[a.Key + b.Key] += a.Value * b.Value;
            }
        }
    }
    int count = 0;
    foreach (var p in product)
    {
        if (Math.Abs(p.Value) >= 0.05)
            //注意这里用到了绝对值,因为如果系数是-0.1和0.5相乘结果是-0.05也是要保留的.
        {
            result = " " + p.Key + " " + p.Value.ToString("0.0") + result;
            count++;
        }
    }
    Console.WriteLine(count + result);
}
```