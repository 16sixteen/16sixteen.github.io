---
layout:     post
title:      "单例模式"
subtitle:   " "
date:       2016-03-09 14:21:00
categories: Algorithm
tags:       Algorithm
author:     "SixTeen"
header-img: "img/solvingReport.jpg"
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


>参考资料：<br/>1.[JAVA设计模式之单例模式](http://blog.csdn.net/jason0539/article/details/23297037)<br/>2.[单例模式](http://www.cnblogs.com/cxjchen/p/3148582.html)


    FIN 3.11 14.19
