---
title: 1054 The Dominant Color
date: 2019-04-22 17:37:33
tags: PAT
categories: [算法学习,PAT]
---

## 题目

Behind the scenes in the computer's memory, color is always talked about as a series of 24 bits of information for each pixel. In an image, the color with the largest proportional area is called the dominant color. A **strictly** dominant color takes more than half of the total area. Now given an image of resolution *M*by *N* (for example, 800×600), you are supposed to point out the strictly dominant color.

### Input Specification:

Each input file contains one test case. For each case, the first line contains 2 positive numbers: *M* (≤800) and *N* (≤600) which are the resolutions of the image. Then *N* lines follow, each contains *M* digital colors in the range [0,2<sup>24</sup>). It is guaranteed that the strictly dominant color exists for each input image. All the numbers in a line are separated by a space.

### Output Specification:

For each test case, simply print the dominant color in a line.

### Sample Input:

```in
5 3
0 0 255 16777215 24
24 24 0 0 24
24 0 24 24 24
```

### Sample Output:

```out
24
```

<!-- more -->

## 题意

给定一张照片的分辨率为MxN,每一个像素点拥有一个颜色,任务是求出这张照片中一定存在的那个主颜色(颜色超过像素数目的一半).

## 思路

我真的差点就被这道题搞自闭了,这个题目考察的点是哈希表,通过将出现过的颜色放入哈希表,颜色每出现一次,就将哈希表对应的数值加一,当颜色出现的次数大于一半像素时,即为所求颜色.

但是当我用这个办法去解这道题时,其他测试案例都通过了,唯独最后一个测试案例却永远是错误的,哪怕我已经觉得我的代码和别人的都一样了,只是用C#语言翻译过来而已,都仍然不能够通过,最后我意识到了,还有唯一的一种情况我没有设想到,那就是只有一个像素点的时候.

幸好最后我在[这个仓库](https://github.com/lynnprosper/ZJU_PAT/tree/master/Advanced_Level)中看到了一个新的解法,把这道题过了.

这个巧妙的解法并没有用到哈希表,而是用一个全新的思路.因为我们已知,图片上肯定有一个主颜色,这个颜色的出现次数肯定大于剩下所有的颜色出现次数之和,也就是说,这个主颜色的出现次数减去剩下所有颜色出现的次数,都要大于0,所以可以定义两个变量,一个是当前颜色nowColor.

另一个是当前颜色出现的次数nowColorCount初始化为1.

如果输入颜色等于当前颜色,那就让nowColorCount++;

如果输入颜色不等于当前颜色,就让nowColorCount--;

如果nowColorCount自减成0,那说明nowColor还不是主颜色,那就将输入颜色设置为当前颜色nowColor.

这样到最后,nowColor肯定会成为主颜色,nowColorCount也肯定大于0.

## 代码

```c#
public static void Main()
{
    string[] tokens = System.Console.ReadLine().Split();
    int column = int.Parse(tokens[0]);
    int row = int.Parse(tokens[1]);
    int half = (column * row) / 2;
    int nowColorCount = 1;
    string nowColor = "";
    for (int i = 0; i < row; i++)
    {
        tokens = System.Console.ReadLine().Split();
        foreach (var token in tokens)
        {
            if (token == nowColor)
            {
                nowColorCount++;
            }
            else
            {
                nowColorCount--;
                if (nowColorCount == 0)
                {
                    nowColor = token;
                    nowColorCount = 1;
                }
            }
        }
    }
    Console.WriteLine(nowColor);
}
```

下面是把我搞自闭的，用字典实现哈希功能的代码

```c#
public static void Main()
{
    string[] tokens = System.Console.ReadLine().Split();
    int column = int.Parse(tokens[0]);
    int row = int.Parse(tokens[1]);
    int half = (column * row) / 2;
    Dictionary<string, int> dict = new Dictionary<string, int>();
    for (int i = 0; i < row; i++)
    {
        tokens = System.Console.ReadLine().Split();
        foreach (var token in tokens)
        {
            if (dict.ContainsKey(token))
            {
                dict[token]++;
                if (dict[token] > half)
                {
                    Console.WriteLine(token);
                    return;
                }
            }
            else
            {
                dict.Add(token, 1);
                if (dict[token] > half)//把我搞自闭的就是这个位置,如果像素点只有一个,那添加进来的时候就要判断了.
                {
                    Console.WriteLine(token);
                    return;
                }
            }
        }
    }
}
```

