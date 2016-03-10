---
layout:     post
title:      "编辑距离"
subtitle:   "Levenshtein算法"
date:       2016-03-09 12:11:00
categories: Algorithm
tags:       Algorithm
author:     "SixTeen"
header-img: "img/solvingReport.jpg"
published:   true
---

## 编辑距离

我们先定义我们能对字符串进行的3个操作：

1.添加一个字符

2.将其中一个字符修改为另一个字符

3.删除一个字符

编辑距离就是对一个字符串进行上面3种操作，转换成另一个字符串，所需要的最少操作次数。

例如： abc转换成dbad

1. a替换成d

2. c替换成a

3. 添加一个d

这个算法其实就是使用了动态规划来求解。定义一个函数edit(i,j)。i表示的是第一个字符串的0-i位组成的子串，j表示的是第二个字符串0-j位组成的子串，edit（i，j）表示的是这2个子串的编辑距离。

通过这样的定义，我们显然可以得到一些初始状态：

1. ```if i == 0 && j == 0 ，edit（i，j）= 0```

2. ```if i == 0 && j >  0 , edit (i, j) = j```

3. ```if i >  0 && j == 0 , edit (i, j) = i```

转换方程：```if i >= 1 && j  >= 1, edit(i,j) = min{edit(i-1,j)+1,edit(i,j-1)+1,edit(i-1,j-1)+f(i,j)}```其中```f(i,j)```在```string1[i]==string2[j]```的时候为0，否则为1.

这个转换方程中,edit(i,j)可以从3种状态转换过来，i子串进行一次删除操作变成i-1的子串，然后最少的编辑次数就是```edit（i-1,j）+ 1```,同理，也可以由```edit（i，j-1）+ 1```得出，至于```edit（i-1，j-1）+f（i，j）```中的```f（i，j）```则是表示对第i位和第j位进行比较，如果相同，那么就不用多余的操作，如果不一样，则进行一次替换操作，因此```f（i，j）```的取值是0或1.


>参考资料：<br/>[编辑距离及编辑距离算法](http://www.cnblogs.com/biyeymyhjob/archive/2012/09/28/2707343.html)

    FIN 3.10 13.28
