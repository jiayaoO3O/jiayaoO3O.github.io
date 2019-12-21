---
title: CapsLock键的AutoHotKey映射方案
date: 2019-06-26 20:46:36
tags: autohotkey
categories: 技术
---
CapsLock键,占着最好的位置,产出最低的贡献,所以很多人包括我都会选择将它映射成其他功能.
vim党喜欢将CapsLock键映射成esc,这样退出输入模式就会非常的轻松,但是我vim用得不多,所以更想要将它映射成ctrl键,并且由于vim的hjkl方向移动很好用,所以我也打算将CapsLock＋hjkl实现方向移动.

<!-- more -->

但是这里问题就出现了,如果直接将CapsLock–>ctrl,那假设想要实现CapsLock＋hjkl方向移动,就意味着要将ctrl+hjkl进一步映射成左下右上,但是这样会发生冲突,因为ctrl+hjkl本身是有含义的,例如chrome下ctrl+h就是历史记录,如果修改了这个按键映射那就没法直接调出历史记录了.

所以,将CapsLock映射为ctrl,和hjkl实现方向移动,总是会产生冲突的.

但是我想到了一个完美的方法去解决这个问题,那就是将CapsLock键映射为右ctrl键,注意不是ctrl,而是右ctrl,这个操作就可以完美实现我需要的两个功能.

因为右手边的ctrl键隔得太远平时就很少用,所以将CapsLock–>右ctrl的好处是,不会影响左ctrl与hjkl的组合功能.

例如,现在按住CapsLock+h,光标会往左边移动,但是按住左边的ctrl+h,仍然可以调出历史记录,并且CapsLock可以实现ctrl的全部功能,例如CapsLock+c,v复制粘贴功能仍然能够实现.

附上autohotkey脚本:

```
SetCapsLockState, AlwaysOff 
CapsLock::RControl ;CapsLock映射右ctrl.
#CapsLock::CapsLock ;win+CapsLock映射为CapsLock.

>^h::Send, {Left} ;右ctrl+hjkl实现移动.
>^j::Send, {Down}
>^k::Send, {Up}
>^l::Send, {Right}

>^+h::Send, +{Left} ;右ctrl+shift+hjkl实现选定移动.
>^+j::Send, +{Down}
>^+k::Send, +{Up}
>^+l::Send, +{Right}
```




  