---
layout:     post
title:      "Unity3d 脚本生命周期"
subtitle:   "UNET学习 生命周期"
date:       2016-07-18 10:34:00
categories: Unity3d
tags:       Unity3d
author:     "SixTeen"
header-img: "img/unity3d.jpg"
published:   true
---

最近用到的比较重要的有：Awake,OnEnable,Start,FixedUpdate,Update,yield,LateUpdate,OnDisable,OnDestroy

在脚本非激活状态下无法执行协程，在OnDisable,OnDestroy中无法startCoroutine

[Execution Order of Event Functions](http://docs.unity3d.com/Manual/ExecutionOrder.html)

![](/img/unity3d/daylearning/7.18/life.png)

    FIN 2016.7.27/23.07