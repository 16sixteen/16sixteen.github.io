---
layout:     post
title:      "Unity3d 初识profile及简单优化"
subtitle:   "profile 性能优化"
date:       2016-08-26 23:59:59
categories: Unity3d
tags:       Unity3d
author:     "SixTeen"
header-img: "img/unity3d.jpg"
published:   true
---

* TOC
{:toc}

## 使用profile

```ctrl+7```就可以从unity3d中唤出profile，关于profile的介绍可以查看unity手册(点击help->unity manual->搜索profile)。profile是一个能将每一帧的信息都显示出来的视图。我们能根据每一帧中各部分的详细信息来优化我们的游戏。

## profile窗口

![profile](/img/unity3d/profile/profile_window.png)

profile中我们能看到各种信息，例如CPU使用信息，渲染信息，内存使用信息等。点击profile，就能查看某一帧中的详细信息。

## profile CPU使用优化

CPU中需要关注的首先是Time ms，如果Time ms很大，那么这个地方是必须优化的，其次，GC alloc也是需要CPU去花时间处理的，因此GC alloc也是我们需要关注的。

![profile](/img/unity3d/profile/profile_cpu_usage.png)

## 优化心得

CPU中的GC ALLOC很高，要从2方面优化，第一方面是foreach的使用，每执行一次foreach会产生20B的GC，如果foreach写在update中，那么每一帧就会造成20B的GC，一旦数量多起来，GC多，修改又麻烦，因此尽量避免使用foreach。第二方面就是setActive的使用，为了实现对象池，我在update中调用了单例中的方法，在update中进行了setActive，setActive对普通对象的消耗不大，但是对于UGUI，却会造成大量的GC，查阅很多资料发现似乎是UI组件频繁的setActive，会不停的调用onEnable，UI在此函数中的消耗很大，因此只能将不活跃的对象移到视野之外。（如果是有脚本的对象，那么必须将脚本设为不活跃）

![profile](/img/unity3d/profile/setActive_GC.png.png)

    2016.7.7/12.03



