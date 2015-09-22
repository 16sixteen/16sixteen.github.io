---
layout:     post
title:      "Cocos2d-x 学习笔记（1）"
subtitle:   "cocos2d-x 配置环境 新建项目 初始认识"
date:       2015-05-15 17:51:00
categories: cocos2d-x
tags:       cocos2d-x
author:     "SixTeen"
header-img: "img/cocos2dx.png"
---

# Cocos2d-x 学习笔记（1）

标签（空格分隔）： cocos2d-x 配置环境 新建项目 初始认识

---


by vamei
阅读与笔记

学习技术最基本的方式是阅读。无论是哪一种技术，可以选择的阅读资料总是很多。经典资料是个不错的出发点。到豆瓣上搜索一下关键字，然后根据评分选择合适的书。StackOverflow和一些博客也会列出许多经典的书单。然而经典书籍并非一个限制。同一本书可能并不适用于所有人(即使许多人承认它是经典)。阅读是一种寻求共鸣的过程，而不是单方面的灌输。如果在阅读中，这本书无法唤起共鸣，你觉得枯燥，那么不必犹豫，你大可以换一本更符合自己口味的书。此外，一本书中的内容显得复杂或深奥，往往是因为我们缺乏更加基础的东西。我们可以去寻找一些更基础性的内容来读。比如，如果没有http协议的背景知识，那么web框架的内容就会显得过分的复杂。这个时候，与其在密林中挣扎，不如减少复杂性，花些时间学习基础内容。

 

阅读时，记笔记是个不错的习惯。你可以边读边在本子上记下一两句唤起共鸣的话，也可以阅读一个章节之后，记下脑海中印象深刻的部分。如果条件允许，你还可以用高亮笔在资料上写写画画，在页边写笔记。

CRUD模式 C create R retrieve U update D delete



推送
指服务器定向将信息实时发送给客户端

长连接
服务器和客户端一直进行通信连接，随时可以进行通信

短连接
有数据交互才建立一个连接，数据发送完就断开

json 

创建方法:
object = {
    propertyName1: propertyValue1,
    propertyName2: propertyValue2

}

xml

<object>
<propertyName1>propertyValue1</propertyName1>
</object>


LinearLayout 必须要有属性android:orientation 
match_parent与母元素相同
wrap_content与包含的内容相同

出错无法运行的时候，可以查看logcat中爆出的错误，只看cause by就可以知道错误发生在哪里
例如：java.lang.RuntimeException: Unable to start activity ComponentInfo{test.test.study.pro1/test.test.study.pro1.MainActivity}: android.view.InflateException: Binary XML file line #12: Error inflating class Botton就是打错一个字母，所以找不到Botton这个类




