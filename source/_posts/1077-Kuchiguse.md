---
title: 1077 Kuchiguse
date: 2019-04-26 15:58:53
tags: PAT
categories: [算法学习,PAT]
---

## 题目

The Japanese language is notorious for its sentence ending particles. Personal preference of such particles can be considered as a reflection of the speaker's personality. Such a preference is called "Kuchiguse" and is often exaggerated artistically in Anime and Manga. For example, the artificial sentence ending particle "nyan~" is often used as a stereotype for characters with a cat-like personality:

- Itai nyan~ (It hurts, nyan~)
- Ninjin wa iyada nyan~ (I hate carrots, nyan~)

Now given a few lines spoken by the same character, can you find her Kuchiguse?

### Input Specification:

Each input file contains one test case. For each case, the first line is an integer *N* (2≤*N*≤100). Following are *N* file lines of 0~256 (inclusive) characters in length, each representing a character's spoken line. The spoken lines are case sensitive.

### Output Specification:

For each test case, print in one line the kuchiguse of the character, i.e., the longest common suffix of all *N* lines. If there is no such suffix, write `nai`.

### Sample Input 1:

```in
3
Itai nyan~
Ninjin wa iyadanyan~
uhhh nyan~
```

### Sample Output 1:

```out
nyan~
```

### Sample Input 2:

```in
3
Itai!
Ninjinnwaiyada T_T
T_T
```

### Sample Output 2:

```out
nai
```

<!-- more -->

## 题意

这是我开始做PAT以来首次遇到的一道,看完完全不知道在说什么,用了谷歌翻译仍然不知道在说什么,去看了别人的博客解释才明白说的是什么的题目.直到现在,我也只是知道题目问的是什么,但是题目什么意思还没明白.

这道题要问的就是从字符串后面开始,最大的字符子串是什么,如果有就输出最大子串,没有就输出**nai**.

## 思路

这道题写出代码比读懂题目简单多了,解决的思路很简单.

先建立一个记录最大子串的变量count.

将输入的几个字符串的第一个字符串,作为标准,将它翻转(因为翻转之后可以从头开始比较),然后对所有字符串进行翻转然后逐位字符比较,如果都一样了,count++.

如果比较不相等,说明最大字符子串结束,返回count.

这个时候就得到了最大字符子串的长度,然后用任意一个字符串s对(s.Length-count)进行截取,得到的就是字符子串了.

## 代码

先创建一个获取最大字符子串长度的函数:

```c#
public static int MaxLengthOfSubstring(string[] inputs)
{
    int result = 0;
    char[] standard = inputs[0].ToCharArray();//建立一个标准串.
    char[] str;
    Array.Reverse(standard);
    for (int i = 0; i < standard.Length; i++)
    {
        foreach (var input in inputs)
        {
            str = input.ToCharArray();
            Array.Reverse(str);//对每一个输入串进行翻转,然后比较.
            if (str[i] != standard[i])
            {
                return result;
            }
        }
        result++;
    }
    return result;
}
```

```c#
public static void Main()
{
    int count = int.Parse(Console.ReadLine());
    string[] inputs = new string[count];
    for (int i = 0; i < count; i++)
    {
        inputs[i] = Console.ReadLine();
    }
    count = MaxLengthOfSubstring(inputs);//变量重复利用.
    string substring = inputs[0].Substring(inputs[0].Length - count);
    Console.WriteLine(count == 0 ? "nai" : substring);
}
```

