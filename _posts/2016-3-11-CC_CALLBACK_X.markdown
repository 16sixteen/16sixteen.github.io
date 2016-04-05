---
layout:     post
title:      "Cocos2d-x CC_CALLBACK_X"
subtitle:   "std::bind"
date:       2016-03-11 20:59:00
categories: cocos2d-x
tags:       cocos2d-x
author:     "SixTeen"
header-img: "img/cocos2dx.png"
---

## std::bind

{% highlight c++ %}

#include<iostream>
#include<functional>
using namespace std;

int add(int i, int j){
    return i + j;
}

int main(){
    auto func = std::bind(add, placeholders::_1, placeholders::_2);
    cout << func(1, 2);
    return 0;
}
//output:3

{% endhighlight %}

绑定就是将一个函数绑定，上面的例子中，placeholder是占位符，表示func接收的参数将会使用到add方法中，如果我们将站位符改成固定的数字：

{% highlight c++ %}

#include<iostream>
#include<functional>
using namespace std;

int add(int i, int j){
    return i + j;
}

int main(){
    auto func = std::bind(add, 1, 2);
    cout << func();
    return 0;
}
//output:3

{% endhighlight %}

std::bind还可以绑定成员函数，静态函数，不过其不能绑定重载函数。

## CC_CALLBACK_X

CC_CALLBACK_X在cocos2dx中的定义：

{% highlight c++ %}

// new callbacks based on C++11
#define CC_CALLBACK_0(__selector__,__target__, ...) std::bind(&__selector__,__target__, ##__VA_ARGS__)
#define CC_CALLBACK_1(__selector__,__target__, ...) std::bind(&__selector__,__target__, std::placeholders::_1, ##__VA_ARGS__)
#define CC_CALLBACK_2(__selector__,__target__, ...) std::bind(&__selector__,__target__, std::placeholders::_1, std::placeholders::_2, ##__VA_ARGS__)
#define CC_CALLBACK_3(__selector__,__target__, ...) std::bind(&__selector__,__target__, std::placeholders::_1, std::placeholders::_2, std::placeholders::_3, ##__VA_ARGS__)

{% endhighlight %}

实际的效果就是让target调用绑定的selector回调函数，并绑定第0-3个参数后面参数的值。

>参考资料:<br/>1.[CC_CALLBACK之间的区别](http://www.cnblogs.com/skysand/p/4247823.html)<br/>2.[ C++11 std::bind std::function 高级用法](http://blog.csdn.net/eclipser1987/article/details/24406203)