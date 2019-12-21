---
title: 1100 Mars Numbers
date: 2019-04-29 19:38:06
tags: PAT
categories: [算法学习,PAT]
---

## 题目

People on Mars count their numbers with base 13:

- Zero on Earth is called "tret" on Mars.
- The numbers 1 to 12 on Earth is called "jan, feb, mar, apr, may, jun, jly, aug, sep, oct, nov, dec" on Mars, respectively.
- For the next higher digit, Mars people name the 12 numbers as "tam, hel, maa, huh, tou, kes, hei, elo, syy, lok, mer, jou", respectively.

For examples, the number 29 on Earth is called "hel mar" on Mars; and "elo nov" on Mars corresponds to 115 on Earth. In order to help communication between people from these two planets, you are supposed to write a program for mutual translation between Earth and Mars number systems.

### Input Specification:

Each input file contains one test case. For each case, the first line contains a positive integer *N* (<100). Then *N* lines follow, each contains a number in [0, 169), given either in the form of an Earth number, or that of Mars.

### Output Specification:

For each number, print in a line the corresponding number in the other language.

### Sample Input:

```in
4
29
5
elo nov
tam
```

### Sample Output:

```out
hel mar
may
115
13
```

<!-- more -->

## 题意

火星的数字是13进制的,并且他们的数字并不用0~9来表示,而是:

- 当数字是1~12时,用jan, feb, mar, apr, may, jun, jly, aug, sep, oct, nov, dec来表示1~12.
- 当数字超过12,有两位时,高位用tam, hel, maa, huh, tou, kes, hei, elo, syy, lok, mer, jou来表示1~12.
- 0用tret表示.
- 当数字是13,也即火星上的"10"时,只用一个"tam"表示,"20"用"hel"表示,也就是说,当火星数字是10,20,30这种整数时,后面的零不用写.

现在要你设计一个翻译程序,如果输入的是地球整数,那就把它翻译成13进制的火星数字;如果输入是火星数字,就把它翻译成10进制的地球数字.

## 思路

这道题要考虑的情况相当相当多,既要进制转换,又要字符替换,还要对不同长度的数字进行不同情况的分析.

用火星数字获取火星文字,可以直接使用字符串数组,下标表示火星数字,数组存放着火星对应的文字.

用火星文字获取火星数字,可以使用泛型字典,通过火星文字直接获得对应的火星数字.

而这道题明确告诉我们火星的文字只有高低两位,所以判断的时候就比较方便.

## 代码

首先我们先实现一个地球数字转换成火星文字的函数:

```c#
public static string translateToMars(int EarthNumber)
{
    if (EarthNumber == 0)
    {
        return "tret";//如果地球数字输入0直接返回,不用判断了.
    }
    string[] lowerDigits = { "tret", "jan", "feb", "mar", "apr", "may", "jun", "jly", "aug", "sep", "oct", "nov", "dec" };
    string[] higherDigit = { "", "tam", "hel", "maa", "huh", "tou", "kes", "hei", "elo", "syy", "lok", "mer", "jou" };
    int[] number = { 0, 0 };//number用于存放10进制转换成13进制的结果,结果只有两位,高位和低位.
    int i = 1;
    while (EarthNumber != 0)//进行进制转换.
    {
        number[i--] = EarthNumber % 13;
        EarthNumber /= 13;
    }
    if (number[0] == 0)//如果进制转换的结果的第一位是0,例如05,07,09
    {
        return lowerDigits[number[1]];//那就直接返回低位number[1]对应的数字的火星文字.
    }
    else//如果进制转换的结果第一位不是0
    {
        if (number[1] == 0)//如果第二位是0,例如10,20,30
        {
            return higherDigit[number[0]];
            //那就直接输出高位number[0]对应的数字的火星文字,因为火星文字的低位0是不输出的.
        }
        else
        {
            return higherDigit[number[0]] + " " + lowerDigits[number[1]];
        }
    }
}
```

再实现一个火星文字转地球数字的函数:

```c#
public static int translateToEarth(string MarsNumber)
{
    //先创建两个字典用于通过火星文字得到火星数字.
    Dictionary<string, int> lowerDigits = new Dictionary<string, int>() {
        {"tret",0},{"jan",1},{"feb",2},{"mar",3},{"apr",4},{"may",5},
        {"jun",6},{"jly",7},{"aug",8},{"sep",9},{"oct",10},{"nov",11},{"dec",12}};
    Dictionary<string, int> higherDigit = new Dictionary<string, int>() {
        {"tam",1},{"hel",2},{"maa",3},{"huh",4},{"tou",5},
        {"kes",6},{"hei",7},{"elo",8},{"syy",9},{"lok",10},{"mer",11},{"jou",12}};
    string[] numbers = MarsNumber.Split();//尝试将火星文字进行分割.
    if (numbers.Length == 1)//如果长度是1,说明只输入了一个单词.
    {
        if (lowerDigits.ContainsKey(numbers[0]))//如果这个单词在低位字典里,那就返回对应数字.
        {
            return lowerDigits[numbers[0]];
        }
        else
        {
            return higherDigit[numbers[0]] * 13;//如果在高位字典里,那就返回13乘以这个数字.
        }
    }
    else
    {
        //如果长度是2,那就说明输入了两个单词,那就高位的乘以13,低位的直接获取.
        return higherDigit[numbers[0]] * 13 + lowerDigits[numbers[1]];
    }
}
```

```c#
public static void Main()
{
    int result;
    int count = int.Parse(Console.ReadLine());
    while (count-- != 0)
    {
        string input = Console.ReadLine();
        if (int.TryParse(input, out result))//尝试转换成整型,如果成功那就是地球数字,如果失败那就是火星文字.
        {
            Console.WriteLine(translateToMars(result));
        }
        else
        {
            Console.WriteLine(translateToEarth(input));
        }
    }
}
```

