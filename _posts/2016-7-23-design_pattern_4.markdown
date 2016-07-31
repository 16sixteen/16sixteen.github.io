---
layout:     post
title:      "设计模式-设计模式入门（4）"
subtitle:   "设计原则 状态模式"
date:       2016-07-23 10:01:00
categories: DesignPattern
tags:       设计模式
author:     "SixTeen"
header-img: "img/designPattern.jpg"
published:   true
---

* TOC
{:toc}

### 简单的状态模式

在我的理解里，状态模式就是描述一个状态图的一种模式。状态图里有状态和转换条件，状态模式里也有这2个东西。在简单的状态模式中，使用静态整型描述状态，使用if的条件判断来实现状态跳转（条件转换）。

{% highlight c++ %}

public class stateExample{

    private static int state_lock = 0;
    private static int state_open = 1;
    private static int state_empty = 2;
    private int state = state_lock;
    public void someOperation(){
        if(state == state_lock){
            //do something
            //状态迁移
            state = state_open;
        }else if(state == state_open){
            //do something
        }else if(state == state_empty){
            //do something
        }else{
            //error handle
        }
    }
}

{% endhighlight %}

这样，在操作中通过判断是什么状态，实现对应的动作，并发生状态变化，这就实现了一个状态图。但是，如果突然改变了需求，现在需要加入10个新状态！excuse me???我想你已经想到了加入10个新状态到底有多麻烦，这意味着我每个操作都要多写一连串的if！

### 状态模式

和之前一样，直接上最直观的类图：

![](/img/unity3d/daylearning/7.25/class.png)

Context（上下文）中保存了当前的状态，面对操作就执行状态中的处理函数，处理函数根据实际上的状态会动态变化。状态模式使用组合的方式实现了操作的变化。如果我现在需要加入新的状态，我只需要继承state实现新的状态类，并在能跳转到新状态的状态类中加入对应的跳转即可。将状态类化的好处在于，我们不用再写条件判断来实现状态跳转。

如果状态没有内部信息，那么我们可以将状态类静态化，所有的Context使用相同的状态类。如果状态有内部信息，那么在构建Context的时候就必须实例化一组该Context独有的状态类。

### 一些概念

#### 状态模式

> 状态模式允许对象在内部状态改变时改变它的行为，对象看起来好像修改了它的类。

#### 共享状态

> 状态对象持有它们自己的内部状态的时候，不能共享。

#### 其它

> 通过将每一个状态封装进一个类，我们把以后需要做的任何改变局部化了。


<br/><br/><br/>

>参考书籍：<br/>《Head First设计模式》

    FIN 2016.7.31/11.04


