---
title: 1061 Dating
date: 2019-04-23 11:17:14
tags: PAT
categories: [算法学习,PAT]
---

## 题目

Sherlock Holmes received a note with some strange strings: `Let's date! 3485djDkxh4hhGE 2984akDfkkkkggEdsb s&hgsfdk d&Hyscvnm`. It took him only a minute to figure out that those strange strings are actually referring to the coded time `Thursday 14:04` -- since the first common capital English letter (case sensitive) shared by the first two strings is the 4th capital letter `D`, representing the 4th day in a week; the second common character is the 5th capital letter `E`, representing the 14th hour (hence the hours from 0 to 23 in a day are represented by the numbers from 0 to 9 and the capital letters from `A` to `N`, respectively); and the English letter shared by the last two strings is `s` at the 4th position, representing the 4th minute. Now given two pairs of strings, you are supposed to help Sherlock decode the dating time.

### Input Specification:

Each input file contains one test case. Each case gives 4 non-empty strings of no more than 60 characters without white space in 4 lines.

### Output Specification:

For each test case, print the decoded time in one line, in the format `DAY HH:MM`, where `DAY` is a 3-character abbreviation for the days in a week -- that is, `MON`for Monday, `TUE` for Tuesday, `WED` for Wednesday, `THU` for Thursday, `FRI` for Friday, `SAT` for Saturday, and `SUN` for Sunday. It is guaranteed that the result is unique for each case.

### Sample Input:

```in
3485djDkxh4hhGE 
2984akDfkkkkggEdsb 
s&hgsfdk 
d&Hyscvnm
```

### Sample Output:

```out
THU 14:04
```

<!-- more -->

## 题意

有人给了福尔摩斯一张小纸条,要跟他约会,但是约会时间已经被加密了,现在要通过以下规则解密:

- 输入的4个字符串分为两组,第一组用来得出日期和小时,第二组得出分钟.
- 第一组中第一对位置相同的同一大写字母(A到G)代表了日期,A表示MON,G表示SUN.
- 第一组中第二对位置相同的同一数字或者大写字母(A到N)代表了小时,0表示0点,A表示10点,N表示23点.
- 第二组中第一对位置相同的同一大写或小写字母的位置代表分钟,下标0即第0分钟,下标4则第4分钟.

## 思路

又是一道因为题目规则没看明白浪费了一个上午的题,原来连题目都没看懂,真的烦,明白了规则其实这道题就比较简单了,就是相对位置一路比较过去就行了,但是一定要注意每个题目限定要求.

## 代码

```c#
public static void Main()
{
    string[] tokens = new string[4];
    string[] result = { "", "", "" };
    int flag = 0;
    string[] week = { "MON", "TUE", "WED", "THU", "FRI", "SAT", "SUN" };
    for (int i = 0; i < tokens.Length; i++)
    {
        tokens[i] = System.Console.ReadLine();
    }
    for (int i = 0; i < tokens[0].Length && i < tokens[1].Length; i++)
    {
        if (tokens[0][i] == tokens[1][i] && tokens[0][i] >= 'A' && tokens[0][i] <= 'G')//规定1,一定要在A到G之间.
        {
            result[0] = week[tokens[0][i] - 'A'];
            flag = i + 1;
            break;
        }
    }
    for (int i = flag; i < tokens[0].Length && i < tokens[1].Length; i++)
    {
        //规定2,i一定要在上一个循环结束的下一个位置开始,因为现在要找在第一对相同位置之后的第二对.
        if (tokens[0][i] == tokens[1][i] && (tokens[0][i] >= '0' && tokens[0][i] <= '9' || tokens[0][i] >= 'A' && tokens[0][i] <= 'N'))
        //规定3,判定范围不能>='0'&&<='N'.
        {
            result[1] = tokens[0][i] > '9' ? (tokens[0][i] - '7').ToString() : "0" + tokens[0][i];
            //规定4,不足10的数要在前面补个0.
            break;
        }
    }
    for (int i = 0; i < tokens[2].Length && i < tokens[3].Length; i++)
    {
        if (tokens[2][i] == tokens[3][i] && (tokens[2][i] >= 'A' && tokens[2][i] <= 'Z' || tokens[2][i] >= 'a' && tokens[2][i] <= 'z'))
        {
            result[2] = i < 10 ? "0" + i : i.ToString();
            Console.WriteLine(result[0] + " " + result[1] + ":" + result[2]);
            return;
        }
    }
}
```

