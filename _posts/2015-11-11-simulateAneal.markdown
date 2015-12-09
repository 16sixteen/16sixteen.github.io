---
layout:     post
title:      "模拟退火法"
subtitle:   "simulateAneal implement"
date:       2015-11-11 19:29:00
categories: algorithm
tags:       algorithm
author:     "SixTeen"
header-img: "img/solvingReport.jpg"
published:   true
---

#<a href="#01">模拟退火</a> <a href="#02">伪代码</a> <a href="#03">C++实现</a>

---

##<a name="01"></a>什么是模拟退火

爬山算法是一种只往高处走的算法，它不会考虑下山这一种选择，容易停留在局部最优解。模拟退火法试图把爬山法和随机行走结合起来，同时获得完备性和效率。

把模拟退火比作一个冶炼过程，在温度高的时候，我们很容易改变他的形状，而随着时间的推移，温度逐渐下降的时候，这块金属趋于定型。模拟退火就是这样一种算法，引入一个温度，在温度高的时候，我们有一定概率允许它作出下山的决定，而在温度低的时候，我们更趋向于上山。

##<a name="02"></a>伪代码

![pseudocode](/img/algorithm/simulateAneal.png)

##<a name="03"></a>C++实现

{% highlight c++ %}
/*fin 11.11
simulateAneal
*/
#ifndef SIMULATEANEAL
#define SIMULATEANEAL
#include"queenCreator.h"
#include<iostream>
#include<vector>
#include<stdlib.h>
#include<time.h>
#include<cmath>
using namespace std;

queen getRandomSuccessor2(queen q){
    //随机取一个后继
    int col = rand() % 8;
    int row = rand() % 8;
    while (row == q.getrow(col)){
        col = rand() % 8;
        row = rand() % 8;
    }
    queen newq(q);
    newq.queenset(col, col, row);
    newq.correct();
    return newq;
}

bool simulateAneal(queen q,int* sum){
    q.correct();
    //设置初始温度，下降速率，凝固温度
    double temperature = 10000.0;
    double coolingRate = 0.9999;
    double absolutetemperature = 0.0001;
    queen newq(q);
    while (temperature > absolutetemperature){
        //相当于schedule[i] = temperature*coolingrate^i
        double T = temperature;

        if (newq.getconflict() == 0){ return true; }
        
        queen q2 = getRandomSuccessor2(newq);
        double E = newq.getconflict() - q2.getconflict();
        double posibility = exp(E / T);
        double r = rand() % 100;
        if (E > 0){
            newq = q2;
            (*sum)++;
        }
        else if(r < posibility*100){
            newq = q2;
            (*sum)++;
        }
        temperature *= coolingRate;
    }
    return false;
}


#endif

{% endhighlight %}

>参考资料：<br />[大白话解析模拟退火算法](http://www.cnblogs.com/heaad/archive/2010/12/20/1911614.html)



    FIN 11.11/19.53

