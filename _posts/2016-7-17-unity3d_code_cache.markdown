---
layout:     post
title:      "Unity3d 代码缓存和效率"
subtitle:   "Find GetComponent"
date:       2016-07-17 19:39:00
categories: Unity3d
tags:       Unity3d
author:     "SixTeen"
header-img: "img/unity3d.jpg"
published:   true
---

* TOC
{:toc}

## 什么是代码缓存和效率

首先放一个[视频连接(Caching - Code Efficiency Tut)](http://www.iqiyi.com/w_19rrx4prp1.html?list=19rro6h3iq)，视频里有这篇博文的所有内容。然后解释一下视频和这里所讨论的代码缓存和效率实际上指的是```GameObject.Find()```和```GetComponent<>()```的效率。

### Find

```Find```是遍历hierarchy寻找匹配的```GameObject```,如果2个名字相同，返回的是任意一个，因此尽量不用Find，因为Find对性能的消耗也很巨大，频繁的Find会导致update有巨大的性能损耗，而且Find不是必须的。

### GetComponent

```GetComponent```和```Find```很像，也有一定的性能损耗，尽量不放在update中使用。

### 性能测试代码

{% highlight c# %}

using UnityEngine;
using System.Collections;

public class cache : MonoBehaviour {

    GameObject halfcache;
    Light fullcache;

    void Start () {
        halfcache = GameObject.Find("Directional Light");
        fullcache = GameObject.Find("Directional Light").GetComponent<Light>();
    }

    void noCache() {
        for (int i = 0; i < 2000; i++) {
            GameObject.Find("Directional Light").GetComponent<Light>().intensity = 1;
        }
    }

    void halfCache() {
        for(int i = 0; i < 2000; i++) {
            halfcache.GetComponent<Light>().intensity = 1;
        }
    }

    void fullCache() {
        for(int i = 0; i < 2000; i++) {
            fullcache.intensity = 1;
        }
    }


    void Update () {
        //noCache();
        //halfCache();
        fullCache();
    }
}

{% endhighlight %}

依次运行```noCache```（执行find，getComponent），```halfCache```（预先执行find缓存起来，在update中执行getComponent）,```fullCache```（把需要操作的组件先缓存起来）

### 性能的大致对比

ctrl+7就可以快捷调出profiler，也可以在window菜单栏中按出，游戏运行中可以在profiler中的cpu usage中看到脚本代码的效率。

#### noCache

![](/img/unity3d/daylearning/7.17/nocache.png)

#### halfCache

![](/img/unity3d/daylearning/7.17/halfcache.png)

#### fullCache

![](/img/unity3d/daylearning/7.17/fullcache.png)

## 总结

一般需要频繁操作的对象都应该缓存起来，可以写成public形式拖入inspector中，也可以先find出来（不推荐），动态产生的对象更不应该使用find去寻找，因为如果对象还没有生产出来，还要写很多的逻辑判断去避免出错，对于动态生成的对象，最好的方法应该有一个负责管理该类对象的管理类。

    FIN 2016.7.25/20.32

