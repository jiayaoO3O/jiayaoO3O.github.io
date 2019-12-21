---
title: 1112 Stucked Keyboard
date: 2019-05-01 15:40:13
tags: PAT
categories: [算法学习,PAT]
---

## 题目

On a broken keyboard, some of the keys are always stucked. So when you type some sentences, the characters corresponding to those keys will appear repeatedly on screen for *k* times.

Now given a resulting string on screen, you are supposed to list all the possible stucked keys, and the original string.

Notice that there might be some characters that are typed repeatedly. The stucked key will always repeat output for a fixed *k* times whenever it is pressed. For example, when *k*=3, from the string `thiiis iiisss a teeeeeest` we know that the keys `i` and `e` might be stucked, but `s` is not even though it appears repeatedly sometimes. The original string could be `this isss a teest`.

### Input Specification:

Each input file contains one test case. For each case, the 1st line gives a positive integer *k* (1<*k*≤100) which is the output repeating times of a stucked key. The 2nd line contains the resulting string on screen, which consists of no more than 1000 characters from {a-z}, {0-9} and `_`. It is guaranteed that the string is non-empty.

### Output Specification:

For each test case, print in one line the possible stucked keys, in the order of being detected. Make sure that each key is printed once only. Then in the next line print the original string. It is guaranteed that there is at least one stucked key.

### Sample Input:

```in
3
caseee1__thiiis_iiisss_a_teeeeeest
```

### Sample Output:

```out
ei
case1__this_isss_a_teest
```

<!-- more -->

## 题意

在一个坏掉的键盘上有一些按键是卡住的,所以当我们输入句子的时候,总有一些按键会固定重复出现K次,每按一下这个坏了的按键,字符就会重复出现K次,现在给定一个重复次数K,和给一个结果字符串,尝试找出粘键的按键和原始的字符串,每一个结果字符串保证至少有一个坏掉的按键.

注意,上面的例子中,e和i是坏掉的按键,因为每次按一下e和i,他们都会重复3次,但是s却不是,因为它并没有每按一次s就重复3次.

## 思路

这道题需要记录每个按键是否坏掉,也就是说我们要记录字符串里每个字符的具体情况,所以可以使用哈希表.

哈希表内每个字符对应的位置的值,代表着这个按键的情况:

- 如果还没判断,按键默认值就是0.

- 如果判断出这个按键没有坏,就将值设置为-1.
- 判断按键已经卡住了,就把按键设置为1.

要如何判断按键是否卡住,可以设置一个变量为nowKey,表示现在正在判断的按键字符;再设置一个nowKeyCount变量表示这个正在判断的字符已经连续出现的次数,如果nowKeyCount%K==0表示这个nowKey重复出现了k的倍数次,是坏键;如果不等于0,说明nowKey出现的次数不是k的倍数,这个键不是坏键.

## 代码

```c#
public static void Main()
{
    int repeatedlyTimes = int.Parse(Console.ReadLine());//重复次数.
    string str = Console.ReadLine();//输入的结果字符串.
    int[] characters = new int[128];//哈希表.
    char nowKey = str[0];//当前判断的按键,先将它设置为字符串第一个字符.
    int nowKeyCount = 0;//当前判断按键出现的次数.
    string brokenKeys = "";//坏掉的按键结果.
    string originalString = "";//原始字符串.
    foreach (var character in (str + " "))
    {
        //for循环的每一个字符Character会比nowKey领先一个位置,为了保证最后一个字符的结果能得到比较,要在str字符串后面再加一个位置.
        if (character == nowKey)
        {
            nowKeyCount++;
        }
        else
        {
            if (nowKeyCount % repeatedlyTimes == 0 && characters[nowKey] != -1)//characters[nowKey] != -1是为了保证已经被判断没坏的字符不能被重新设置为坏了.
            {
                characters[nowKey] = 1;//坏了.
            }
            else
            {
                characters[nowKey] = -1;//没坏.
            }
            nowKey = character;//判断结束,将新的character赋给nowKey,成为新的判断对象.
            nowKeyCount = 1;
        }
    }
    for (int i = 0; i < str.Length; i++)
    {
        if (characters[str[i]] == 1)
        {
            if (!brokenKeys.Contains(str[i].ToString()))//保证坏了的键只出现一次.
            {
                brokenKeys += str[i];
            }
            i += repeatedlyTimes - 1;//让i往后跳repeatedlyTimes - 1个位置,例如重复次数为3,那就让i往后跳2个位置,这样就可以保证按键只输出一次.
            originalString += str[i];
        }
        else
        {
            originalString += str[i];
        }
    }
    Console.WriteLine(brokenKeys);
    Console.WriteLine(originalString);
}
```

