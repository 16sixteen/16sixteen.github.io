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

添加资源，直接在系统复制图片，之后在编译器的资源处右击点paste就可以添加了
    int[] images = new int[] {
        R.drawable.java,
        R.drawable.qq,
        R.drawable.qq2,
    };
    这样使用资源
final ImageView image = new ImageView(this);
image.setImageResource(images[0]);

@Override的作用
1.当注释
2.编译器会验证是不是父类中的函数，如果没有则报错
