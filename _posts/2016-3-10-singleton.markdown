---
layout:     post
title:      "单例模式"
subtitle:   " "
date:       2016-03-09 14:21:00
categories: Algorithm
tags:       Algorithm
author:     "SixTeen"
header-img: "img/unity3d.jpg"
published:   true
---

单例模式有3个特点：

1.单例类只能有一个实例

2.这个实例必须是这个单例类自己创建

3.必须给其他对象使用这个实例

个人认为比较好的实现方法是在类创建的时候同时实例化，这样创建类之后就能一直使用。

{% highlight c++ %}

#include<iostream>
using namespace std;

class singleton {
private:
    static int times;
    static singleton* instance;
    singleton() {
        times++;
    }
    //防止被复制
    singleton(const singleton&) {}
    singleton operator=(const singleton&){}
public:
    static singleton* getInstance() {
        cout << times << endl;
        return instance;
    }
};

int singleton::times = 0;
singleton* singleton::instance = new singleton();


int main() {
    singleton* p1 = singleton::getInstance();
    singleton* p2 = singleton::getInstance();
    return 0;
}

{% endhighlight %}

by 3.14 00.58 

最近课上的一个作业加深了对单例模式的理解，感受到了设计模式的重要性。单例模式的作用在于对象与对象的交互，用户与对象的交互不用在对象上的代码实现。这样可以讲交互的接口放在单例类中实现。而对象的类只用处理自身属性的变化，至于什么时候变化可以在单例类中放置管理对象的实例，用于管理。

>参考资料：<br/>1.[JAVA设计模式之单例模式](http://blog.csdn.net/jason0539/article/details/23297037)<br/>2.[单例模式](http://www.cnblogs.com/cxjchen/p/3148582.html)


    FIN 3.11 14.19
