---
layout:     post
title:      "Unity3d学习"
subtitle:   " "
date:       2016-2-24 13:38:00
categories: Unity3d
tags:       Unity3d
author:     "SixTeen"
header-img: "img/solvingReport.jpg"
published:   true
---

在C#代码中使用的公有变量可以在Inspector中直接更改

![public_var](/img/unity3d/public_var.png)
![public_var_inspector](/img/unity3d/public_var_inspector.png)
![public_var_change](/img/unity3d/public_var_change.png)

然而这个更改不会更改你的脚本中的变量的值，只改变挂载的那个的值，最终实际应用到游戏的值是inspector上的值（对应的挂载对象）：

![public_var](/img/unity3d/public_var.png)



