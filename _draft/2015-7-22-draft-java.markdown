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

2015.7.22
java
配置环境中的classpath配置错误会导致在cmd中java类名的时候出现"找不到或者无法加载主类"的错误。常见的错误就是环境变量中的classpath前面少了".;"

javac xx.java 可以编译出.class文件
java xx 可以运行编译好的文件里的main方法

变量要先声明再使用


数组
声明: int[] a;
分配空间: int[] a = new int[100];
          int[] a = new int[]{1,3,5,7,9};

8.1
xx.java文件里的类名应该和文件名一样 否则会出现错误

class类 method方法 语句statement

语句以;号结尾

面向对象
data member(field)
member method(method)
instance

/** comments  */

如果我们提供显式初始值，那么数据成员就会采用显式初始值，而不是默认初始值。但如果我们既提供显式初始值，又在构造器初始化同一数据成员，最终的初始值将由构造器决定

构建方法 > 显式初始值 > 默认初始值

overloading重载

encapsulation封装

interface接口

封装提高了产品的安全性

implements
extends
package com.vamei.society;
import com.vamei.society.*;
static

abstract class Food {
    public abstract void eat();
    public void happyFood();
    {
        System.out.println("Good! Eat Me!");
    }
}

decoration

BufferedReader()是一个装饰器(decorator)，它接收一个原始的对象，并返回一个经过装饰的、功能更复杂的对象。修饰器的好处是，它可以用于修饰不同的对象。我们这里被修饰的是从文件中读取的文本流。其他的文本流，比如标准输入，网络传输的流等等，都可以被BufferedReader()修饰，从而实现缓存读取。

多线程


容器
System.arraycopy(aFrom, 1, aTo, 0, 3);


List<String> l1 = new ArrayList<String>();
        l1.add("good");
        l1.add("bad");
        l1.add("shit");
        l1.remove(0);
        System.out.println(l1.get(1));
        System.out.println(l1.size());


Set<Integer> s1 = new HashSet<Integer>();
        s1.add(4);
        s1.add(5);
        s1.add(4);
        s1.remove(5);
        System.out.println(s1);
        System.out.println(s1.size());


Java的GUI功能主要集中在awt和swing两个包中。awt是GUI底层包。swing包是高层的封装，更容易移植。这里将更侧重于swing包。

import javax.swing.*;
import java.awt.*;

public class HelloWorldSwing {
    private static void createAndShowGUI() {
        JFrame frame = new JFrame("HelloWorld");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        // Pane's layout
        Container cp = frame.getContentPane();
        cp.setLayout(new FlowLayout());

        // create button
        JButton b1 = new JButton("click me");
        JButton b2 = new JButton("shit");

        // add buttons
        cp.add(b1);
        cp.add(b2);

        // show the window
        frame.pack();
        frame.setVisible(true);
    }

    public static void main(String[] args) {
        Runnable tr = new Runnable() {
            public void run() {
                createAndShowGUI();
            }
        };
        javax.swing.SwingUtilities.invokeLater(tr);
    }
}


