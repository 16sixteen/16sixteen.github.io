---
layout:     post
title:      "Unity3d can't add script"
subtitle:   " "
date:       2016-2-24 13:38:00
categories: Unity3d
tags:       Unity3d
author:     "SixTeen"
header-img: "img/solvingReport.jpg"
published:   true
---

![problem](/img/unity3d/cantaddscript.png)

今天在添加脚本的时候遇到这个问题，虽然提示的是你所添加的脚本不存在，但实际上我通过资源管理器找到了文件，也用Mono打开了文件，文件确实是存在的，那么究竟是什么问题呢？这时候请观察你的输出控制台，如果有提示报错，就证明发生错误的可能是你之前编辑的脚本出现了错误，你必须修改之后才能添加新的脚本。
