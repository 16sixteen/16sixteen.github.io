---
layout:     post
title:      "Unity3d 常用数据结构"
subtitle:   "Unity3d学习 数据结构"
date:       2016-07-21 15:00:00
categories: Unity3d
tags:       Unity3d
author:     "SixTeen"
header-img: "img/unity3d.jpg"
published:   true
---

* TOC
{:toc}

## 最常用的数据类型

Unity3d中最常用的类型就是```List<T>```和```Dictionary<TKey,TValue>```,所有的事情都可以用这两个泛型数据结构来完成。不过还是要学习一下其他的数据结构的。

## Array ， ArrayList， List<T>

List<T>是底层以Array实现的动态数组，因为指定了对应的类型，因此不会有ArrayList中的装箱拆箱的消耗。ArrayList中存储的是Object对象，因此无论是什么类型，都先装到Object对象中，再放入数组中，取的时候再把箱子拆掉，这样在unity3d中会造成GC，从而导致性能的下降。因此使用List<T>或者直接使用Array即可。

## Queue<T> 和 Stack<T>

根据需要来使用，queue就是先进先出的队列，stack就是先进后出的栈，根据实际算法来使用。

## Dictionary<Tkey,TValue>

字典就是STL中的map，以键-值的形式索引存储。

## List<T> 和 Dictionary<Tkey,TValue>的常用成员变量和成员方法

{% highlight c# %}

void Start () {
    Dictionary<int, int> d = new Dictionary<int, int>();
    
    //插入数据
    d[1] = 1;
    d[2] = 2;
    d[4] = 4;
    //遍历key和值
    List<int> k = new List<int>(d.Keys);
    for(int i = 0; i < k.Count; i++) {
        Debug.Log("key " + k[i] + ":value:"+d[k[i]]);
    }

    //确定有无此key,在Dictionary是嵌套结构的时候很重要
    //例如Dictionary<int,List<int>>中，在key不存在的时候，赋值要进行new操作
    Debug.Log("has key 0:" + d.ContainsKey(0));
    Debug.Log("has key 1:"+d.ContainsKey(1));

    //删除
    if (d.ContainsKey(1)) {
        d.Remove(1);
    }
    Debug.Log("has key 1:" + d.ContainsKey(1));



    List<int> l = new List<int>();
    //如果数组比较大，可以先获取一定长度的连续空间，这样可以减少空间少的时候的成倍增长的消耗
    l.Capacity = 10;
    //插入数据
    l.Add(1);
    l.Add(2);
    //删除
    //l.Remove(item);
    //l.RemoveAt(index);
    Debug.Log(l.Count);
    Debug.Log(l[1]);
    Debug.Log(l.IndexOf(2));
    Debug.Log(l.Contains(0));
}

{% endhighlight %}

Dictionary:
![](/img/unity3d/daylearning/7.21/dictionary.png)
List:
![](/img/unity3d/daylearning/7.21/list.png)

> [Unity3D中常用的数据结构总结与分析](http://www.cnblogs.com/murongxiaopifu/p/4161648.html?utm_source=tuicool&utm_medium=referral)

    FIN 2016.7.28/17.38