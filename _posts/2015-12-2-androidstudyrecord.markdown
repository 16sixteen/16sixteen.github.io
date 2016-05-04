---
layout:     post
title:      "Android学习笔记"
subtitle:   "Android学习笔记"
date:       2015-11-17 22:29:00
categories: android
tags:       android
author:     "SixTeen"
header-img: "img/solvingReport.jpg"
published:   false
---


AVD： (android virtual machine):安卓虚拟设备,就是安卓的模拟器
ADT： (android development tools)安卓开发工具
SDK：(software development kit)软件开发工具包,就是安卓系统,平台架构等的工具集合,如adb.exe
AndroidManifest.xml：app包名 + 组件声明 + 程序兼容的最低版本 + 所需权限等程序的配置文件
DX工具：将.class转换成.dex文件

这个R文件可以理解为字典，res下每个资源都都会在这里生成一个唯一的id！

使用资源
java：R.x.xx
xml @x/xx