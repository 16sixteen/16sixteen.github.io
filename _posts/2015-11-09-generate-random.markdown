---
layout:     post
title:      "C/C++里如何生成随机数"
subtitle:   "rand "
date:       2015-11-09 20:24:00
categories: Algorithm
tags:       Algorithm
author:     "SixTeen"
header-img: "img/solvingReport.jpg"
published:   true
---


---

    头文件：
    #include<stdlib.h>
    #include<time.h>
    srand((int)time(0));//设置时间为种子
    int random = rand() % (x);//生成0-(x-1)的随机数


>参考资料:<br/>
1.[百度百科](http://baike.baidu.com/link?url=ytq_9xaIIwrZ2-jUA42Sh6GhfQQNC2oCSysYXIZ4gilzD135GjuX6uSrA5wTuq9d7dJkq_XlWkc1A0H2qWGluq)

----

    FIN 10.9/20.20
