---
title: 1128 N Queens Puzzle
date: 2019-05-05 16:24:50
tags: PAT
categories: [算法学习,PAT]
---

## 题目

The "eight queens puzzle" is the problem of placing eight chess queens on an 8×8 chessboard so that no two queens threaten each other. Thus, a solution requires that no two queens share the same row, column, or diagonal. The eight queens puzzle is an example of the more general *N* queens problem of placing *N* non-attacking queens on an *N*×*N* chessboard. (From Wikipedia - "Eight queens puzzle".)

Here you are NOT asked to solve the puzzles. Instead, you are supposed to judge whether or not a given configuration of the chessboard is a solution. To simplify the representation of a chessboard, let us assume that no two queens will be placed in the same column. Then a configuration can be represented by a simple integer sequence (*Q*1,*Q*2,⋯,*QN*), where *Qi* is the row number of the queen in the *i*-th column. For example, Figure 1 can be represented by (4, 6, 8, 2, 7, 1, 3, 5) and it is indeed a solution to the 8 queens puzzle; while Figure 2 can be represented by (4, 6, 7, 2, 8, 1, 9, 5, 3) and is NOT a 9 queens' solution.

| ![8q.jpg](https://images.ptausercontent.com/7d0443cf-5c19-4494-98a6-0f0f54894eaa.jpg) |      | ![9q.jpg](https://images.ptausercontent.com/d187e37a-4eb8-4215-8e2c-040a73c5c8d8.jpg) |
| :----------------------------------------------------------: | ---- | :----------------------------------------------------------: |
|                           Figure 1                           |      |                           Figure 2                           |

### Input Specification:

Each input file contains several test cases. The first line gives an integer *K* (1<*K*≤200). Then *K* lines follow, each gives a configuration in the format "*N* *Q*1 *Q*2 ... *QN*", where 4≤*N*≤1000 and it is guaranteed that 1≤*Qi*≤*N* for all *i*=1,⋯,*N*. The numbers are separated by spaces.

### Output Specification:

For each configuration, if it is a solution to the *N* queens problem, print `YES` in a line; or `NO` if not.

### Sample Input:

```in
4
8 4 6 8 2 7 1 3 5
9 4 6 7 2 8 1 9 5 3
6 1 5 2 6 4 3
5 1 3 5 2 4
```

### Sample Output:

```out
YES
NO
NO
YES
```

<!-- more -->

## 题意

给定一个棋盘序列,让你判断这个棋盘上的皇后位置是否符合正确摆放方法:
> 棋盘的横,竖,对角线都没有重复的皇后.

## 思路

这道题如果想要用多重循环暴力解法,那肯定是走错路了,这里给一个另类的解法,使用哈希表.

我们知道,一旦一个棋盘的某一个位置放了一个皇后,那这个皇后所在的行,列,上对角线,下对角线就不能再次使用了,所以我们可以将行,列,对角线都作为哈希表,一旦某个棋子有了一个位置,那哈希表内对应的行,列,对角线都置为-1,下一个棋子只要判断自己所在的行,列,对角线的哈希表值是否为-1来判断自己是否已经重复.

## 代码

先实现最关键的判断输入的棋盘是否符合正确摆放位置的函数:

```c#
public static bool IsNQueensPuzzleSolution(string chessInfo)
{
    string[] chessBoard = chessInfo.Split();
    int queensCount = int.Parse(chessBoard[0].ToString());//皇后的数量.
    int[] rows = new int[queensCount + 1];//行哈希表,如果皇后在某行,哈希表内的位置即设置为-1.
    int[] columns = new int[queensCount + 1];//题目已经明确列是不会重复的,所以这里可以不写列哈希表.
    int[] upDiagonal = new int[2 * queensCount];//上对角线哈希表.
    int[] downDiagonal = new int[2 * queensCount + 1];//下对角线哈希表.
    for (int i = 1; i < chessBoard.Length; i++)
    {
        int row = int.Parse(chessBoard[i].ToString());//获取行号和列号.
        int column = i;
        if (rows[row] == -1 || columns[column] == -1 || upDiagonal[queensCount + row - column] == -1 || downDiagonal[row + column] == -1)
        {
            //上对角线的特点:行-列始终相等.
            //下对角线的特点:行+列始终相等.
            //因为row-column可能为负数,而下标不能为负,所以要加上queensCount让它始终为正.
            return false;
        }
        else
        {
            rows[row] = -1;
            columns[column] = -1;
            upDiagonal[queensCount + (row - column)] = -1;
            downDiagonal[row + column] = -1;
        }
    }
    return true;
}
```

```c#
public static void Main()
{
    int lines = int.Parse(Console.ReadLine());
    for (int i = 0; i < lines; i++)
    {
        string chessInfo = Console.ReadLine();
        Console.WriteLine(IsNQueensPuzzleSolution(chessInfo) ? "YES" : "NO");
    }
}
```

