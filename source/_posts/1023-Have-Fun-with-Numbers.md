---
title: 1023 Have Fun with Numbers
date: 2019-04-17 16:21:45
tags: PAT
categories: [算法学习,PAT]
---

## 题目

Notice that the number 123456789 is a 9-digit number consisting exactly the numbers from 1 to 9, with no duplication. Double it we will obtain 246913578, which happens to be another 9-digit number consisting exactly the numbers from 1 to 9, only in a different permutation. Check to see the result if we double it again!

Now you are suppose to check if there are more numbers with this property. That is, double a given number with *k* digits, you are to tell if the resulting number consists of only a permutation of the digits in the original number.

### Input Specification:

Each input contains one test case. Each case contains one positive integer with no more than 20 digits.

### Output Specification:

For each test case, first print in a line "Yes" if doubling the input number gives a number that consists of only a permutation of the digits in the original number, or "No" if not. Then in the next line, print the doubled number.

### Sample Input:

```in
1234567899
```

### Sample Output:

```out
Yes
2469135798
```

<!-- more -->

## 题意

让你判断一个数,翻倍之后,是否还符合要求:

- 翻倍之后的每一个数字,都要是没翻倍之前出现过的.
- 翻倍之后的每一个数字,出现的频率都要和没翻倍之前一样.

## 思路

这一题最难的地方,是读懂题目,我就是因为没看明白原来还有频率要求,被卡好久了,只要明确了有这两个要求,就比较好办了.

另一点要注意的是,题目规定是20位之内的大数翻倍,所以不应该用整型长整型这些数据类型,而是应该用数组保存数据.

## 代码

既然要判断翻倍之后的数字是不是翻倍之前就存在的,那我们先创建一个用来判断某个数是不是在数组里面的函数:

```c#
public static bool Contains(char[] input, char index)
{
    for (int i = 0; i < input.Length; i++)
    {
        if (input[i] == index)
        {
            return true;
        }
    }
    return false;
}
```

再创建一个函数,用来将整型数组翻倍:

```c#
public static int[] DoubleIntegerArray(int[] num)
{
    int flag = 0;
    int length = num.Length - 1;
    while (num[length] != -1)
    {
        if (num[length] * 2 >= 10)
        {
            num[length] = num[length] * 2 % 10 + flag;
            flag = 1;
        }
        else
        {
            num[length] = num[length] * 2 % 10 + flag;
            flag = 0;
        }
        length--;
    }
    if (flag == 1)
    {
        num[length] = 1;
    }
    return num;
}
```

然后创建一个函数用来判断频率是不是已经全为0了,用来判断翻倍之后的数字和翻倍之前频率是否一致

```c#
public static bool FrequencyIsEmpty(int[] frequency)
{
    foreach (var fre in frequency)
    {
        if (fre != 0)
        {
            return false;
        }
    }
    return true;
}
```

```c#
public static void Main()
{
    char[] line = System.Console.ReadLine().ToCharArray();
    int[] num = new int[22];
    int[] frequency = new int[10];
    //用来判断新数字出来的频率和原数字是否一致.如果程序结束后frequency全为0则频率一致.
    for (int i = 0; i < num.Length; i++)
    {
        //将数组置为-1;
        num[i] = -1;
    }
    for (int i = 0; i < line.Length; i++)
    {
        num[num.Length - line.Length + i] = line[i] - '0';//char转int要-48或者-'0'.
        frequency[line[i] - '0']++;//记录对应位置的频率.
    }
    num = DoubleIntegerArray(num);
    for (int i = num.Length - 1; num[i] != -1; i--)
    {
        if (!Contains(line, (char)(num[i] + 48)))
        {
            //如果出现了不存在的数字,直接NO
            Console.WriteLine("No");
            break;
        }
        frequency[num[i]]--;
        if (num[i - 1] == -1)
        {
            if (FrequencyIsEmpty(frequency))
            {
                Console.WriteLine("Yes");
            }
            else
            {
                Console.WriteLine("No");
            }
        }
    }
    for (int i = 0; i < num.Length; i++)
    {
        if (num[i] != -1)
        {
            Console.Write(num[i]);
        }
    }
    Console.WriteLine();
}
```