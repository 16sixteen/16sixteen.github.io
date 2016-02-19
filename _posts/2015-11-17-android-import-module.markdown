---
layout:     post
title:      "AndroidStudio中导入开源库"
subtitle:   "import modules"
date:       2015-11-17 22:29:00
categories: android
tags:       android
author:     "SixTeen"
header-img: "img/solvingReport.jpg"
published:   true
---

---

##如何在androidStudio中导入开源库(jar版)

先下载一个jar放入project中，现在app的build.gradle是

![](/img/android/module1.png)

找到该包，右击，选择add as library

![](/img/android/moduleproject2.png)

添加之后，build.gradle会自动加入一句话

![](/img/android/module3.png)

现在可以使用所导入库中的资源了






>参考资料:<br/>1.[如何在Android Studio项目中导入开源库？](http://www.bkjia.com/Androidjc/965082.html)<br/>2.[AndroidSwipeLayout](https://github.com/daimajia/AndroidSwipeLayout)

    FIN 11.17/22.40
