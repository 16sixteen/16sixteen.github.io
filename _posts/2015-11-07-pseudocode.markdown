---
layout:     post
title:      "伪代码"
subtitle:   "pseudocode"
date:       2015-11-07 20:24:00
categories: Algorithm
tags:       Algorithm
author:     "SixTeen"
header-img: "img/solvingReport.jpg"
published:   true
---

#<a href="#01">赋值语句</a> <a href="#02">条件语句</a> <a href="#03">循环语句</a> <a href="#04">数组</a> 

---

总的来说，伪代码清晰易懂，有点像自然语言，主要突出的是算法的核心部分，而忽略部分的实现细节。下面是一些比较常见的伪代码格式：

##<a name="01"></a>赋值语句

伪代码:

    x←y 将y的值赋予x
    x←y←i 将i的值赋予x和y

Pascal:

    x:=y;
    x:=i;y:=i;

C++:

    x=y;
    x=y=i;

##<a name="02"/>条件语句

伪代码：

    If A Then B
    
    If A Then B
    Else C
    End If

##<a name = "03"/>循环语句

伪代码：
    
    While c
        ...
    End While
    
    Do ...
    Until c
    End do

    For i from 0 to 99
        ...
    End For

##<a name = "04"/>数组
    
    A[1...i]表示A[1]...A[i]

----
    FIN  11.7/20:50
