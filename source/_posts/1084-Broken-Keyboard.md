---
title: 1084 Broken Keyboard
date: 2019-04-28 10:46:24
tags: PAT
categories: [算法学习,PAT]
---

## 题目

On a broken keyboard, some of the keys are worn out. So when you type some sentences, the characters corresponding to those keys will not appear on screen.

Now given a string that you are supposed to type, and the string that you actually type out, please list those keys which are for sure worn out.

### Input Specification:

Each input file contains one test case. For each case, the 1st line contains the original string, and the 2nd line contains the typed-out string. Each string contains no more than 80 characters which are either English letters [A-Z] (case insensitive), digital numbers [0-9], or `_` (representing the space). It is guaranteed that both strings are non-empty.

### Output Specification:

For each test case, print in one line the keys that are worn out, in the order of being detected. The English letters must be capitalized. Each worn out key must be printed once only. It is guaranteed that there is at least one worn out key.

### Sample Input:

```in
7_This_is_a_test
_hs_s_a_es
```

### Sample Output:

```out
7TI
```

<!-- more -->

## 题意

键盘的某些按键坏了,导致你输入的和屏幕显示的结果不一样,现在要找出哪些键位坏了,规则如下:

- 每一个输入字符串都只有大小写字母,数字和下划线.
- 要求输出的结果只输出大写字母,而且每个坏按键只输出一次.

## 思路

这道题是相当简单的一道题了,可以有两种解决思路:

- 对于第一个字符串中的每一个字符,直接查询是否包含在第二个字符串中,如果没有,说明是坏了.
- 用第一个字符串建立一个哈希表,然后对于第二个字符串中的每一个字符,修改哈希表,剩下的没被修改的值就是坏了的按键.

## 代码

方法一:直接查询.

```c#
public static void Main()
{
    string original = System.Console.ReadLine().ToUpper();
    string typedOut = System.Console.ReadLine().ToUpper();
    string result = "";
    foreach (var item in original)
    {
        if (!typedOut.Contains(item.ToString()) && !result.Contains(item.ToString()))
        {
            //这个字符不仅要不存在第二个字符串中,还要不存在结果字符串中,否则有可能重复输出同一个按键
            result += item;
        }
    }
    Console.WriteLine(result);
}
```

方法二:建立哈希表.

```c#
public static void Main()
{
    string original = System.Console.ReadLine().ToUpper();
    string typedOut = System.Console.ReadLine().ToUpper();
    string result = "";
    int[] ascii = new int[128];
    foreach (var item in original)
    {
        ascii[item] = 1;
    }
    foreach (var item in typedOut)
    {
        if (ascii[item] == 1)
        {
            ascii[item] = 0;
        }
    }
    foreach (var item in original)
    {
        if (ascii[item] == 1)
        {
            result += item;
            ascii[item] = 0;
        }
    }
    Console.WriteLine(result);
}
```

