---
title: 1015 Reversible Primes
date: 2019-04-16 19:50:34
tags: PAT
categories: [算法学习,PAT]
---

## 题目

A **reversible prime** in any number system is a prime whose "reverse" in that number system is also a prime. For example in the decimal system 73 is a reversible prime because its reverse 37 is also a prime.

Now given any two positive integers *N* (<10<sup>5</sup>) and *D* (1<*D*≤10), you are supposed to tell if *N* is a reversible prime with radix *D*.

### Input Specification:

The input file consists of several test cases. Each case occupies a line which contains two integers *N* and *D*. The input is finished by a negative *N*.

### Output Specification:

For each test case, print in one line `Yes` if *N* is a reversible prime with radix *D*, or `No` if not.

### Sample Input:

```in
73 10
23 2
23 10
-2
```

### Sample Output:

```out
Yes
Yes
No
```

<!-- more -->

## 题意

判断一个素数是不是可逆素数,这个题目稍微理解困难一点点的是,它并不是直接给你一个37,让你判断将它反转之后的73是不是素数,还有一个D,这个radix D是指基数,你也可以理解为进制,就是你要将N转换成D进制数,反转,再转换成10进制数,然后看它是不是可逆素数.

这道题的另一个让我困惑的点是,原来并不是让它输完了数据之后,再显示结果,而是直接输入完一行,就显示一行,所以上面那个例子,在程序里面,应该是:

```
73 10
Yes
23 2
Yes
23 10
No
-2
```

这样子的.

## 思路

这里我们为了让思维更加规整,先创建两个工具函数,一个用来判断某个数字是不是素数:

```c#
public static bool IsPrime(int input)
{
    if (input <= 1)
    {
        return false;
    }
    for (int i = 2; i < input; i++)
    {
        if (input % i == 0)
        {
            return false;
        }
    }
    return true;
}
```

另一个函数用来反转素数:

```c#
public static int Reverse(int prime, int radix)
{
    int n = prime;
    int result = 0;
    while (n != 0)
    {
        result = result * radix + (n % radix);
        n /= radix;
    }
    return result;
}
```

然后我们只要获取到输入的数据,进行判断就行了.

## 代码

```c#
public static void Main()
{
    string line;
    int N = 0;
    int D = 0;
    while ((line = System.Console.ReadLine()) != null)
    {
        string[] tokens = line.Split();
        if (int.Parse(tokens[0]) < 0)
        {
            break;
        }
        else
        {
            N = int.Parse(tokens[0]);
            D = int.Parse(tokens[1]);
            if (!IsPrime(Reverse(N, D)) || !IsPrime(int.Parse(tokens[0])))
            {
                Console.WriteLine("No");
            }
            else
            {
                Console.WriteLine("Yes");
            }
        }
    }
}
```

