---
title: 1006 Sign In and Sign Out
date: 2019-05-28 11:40:27
tags: PAT
categories: [算法学习,PAT]
---
## 题目

At the beginning of every day, the first person who signs in the computer room will unlock the door, and the last one who signs out will lock the door. Given the records of signing in's and out's, you are supposed to find the ones who have unlocked and locked the door on that day.

### Input Specification:

Each input file contains one test case. Each case contains the records for one day. The case starts with a positive integer *M*, which is the total number of records, followed by *M* lines, each in the format:

```
ID_number Sign_in_time Sign_out_time
```

where times are given in the format `HH:MM:SS`, and `ID_number` is a string with no more than 15 characters.

### Output Specification:

For each test case, output in one line the ID numbers of the persons who have unlocked and locked the door on that day. The two ID numbers must be separated by one space.

Note: It is guaranteed that the records are consistent. That is, the sign in time must be earlier than the sign out time for each person, and there are no two persons sign in or out at the same moment.

### Sample Input:

```in
3
CS301111 15:30:28 17:00:10
SC3021234 08:00:00 11:25:25
CS301133 21:45:00 21:58:40
```

### Sample Output:

```out
SC3021234 CS301133
```

<!-- more -->
## 题意

给定一些签到信息,你要找出谁是最早来负责开门的人和谁是最晚来负责关门的人.

## 思路

这道题的重点是如何简单快捷地判断给定的时间大小,这里用C#的DateTime类型会非常简单,只要将两个时间字符串转换成DateTime型就可以比较大小了.

## 代码
```c#
public static void Main()
{
    int count = int.Parse(Console.ReadLine());
    string[] recordsID = new string[count];
    string[] signInRecords = new string[count];
    string[] signOutRecords = new string[count];
    int firstPerson = 0;
    int lastPerson = 0;
    for (int i = 0; i < count; i++)
    {
        string recode = Console.ReadLine();
        recordsID[i] = recode.Split()[0];
        signInRecords[i] = recode.Split()[1];
        signOutRecords[i] = recode.Split()[2];
        if (DateTime.Parse(signInRecords[i]) < DateTime.Parse(signInRecords[firstPerson]))
        {
            firstPerson = i;
        }
        if (DateTime.Parse(signOutRecords[i]) > DateTime.Parse(signOutRecords[lastPerson]))
        {
            lastPerson = i;
        }
    }
    Console.WriteLine(recordsID[firstPerson] + " " + recordsID[lastPerson]);
}
```

