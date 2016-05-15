---
layout:     post
title:      "剑指offer-阅读笔记（1）"
subtitle:   "sizeof 复制构造函数 赋值函数"
date:       2016-05-12 09:48:00
categories: C++
tags:       C++
author:     "SixTeen"
header-img: "img/contact-bg.jpg"
published:   true
---

### 1.sizeof：

定义一个空的类型，对该类型求sizeof，结果为1。原因是当我们声明该类型的实例的时候，它必须在内存中占有一定的空间，否则无法使用这些实例。至于占用多少内存，由编译器决定。在visual studio中每个空类型的实例占用1字节的空间。

{% highlight c++ %}

class temp{};

int main() {
    cout << sizeof(temp) << endl;
    return 0;
}
//output： 1

{% endhighlight %}

添加了构造函数和析构函数后，对其sizeof仍然是1，因为调用构造函数和析构函数只需要知道函数地址即可，这些地址只与类型相关，与实例无关。

{% highlight c++ %}

class temp{
    temp(){}
    ~temp(){}
};

int main() {
    cout << sizeof(temp) << endl;
    return 0;
}
//output: 1

{% endhighlight %}

如果析构函数是虚函数，编译器会为该类型生成虚函数表，并在该类型的每一个实例中添加一个指向虚函数表的指针。

{% highlight c++ %}

class temp{
    temp(){}
    virtual ~temp(){}
};

int main() {
    cout << sizeof(temp) << endl;
    return 0;
}
//x86 output: 4
//x64 output: 8

{% endhighlight %}


### 2.赋值构造函数：

{% highlight c++ %}

A(A other){ value = other.value; }
/*
* 以值传递的形式，在传参的时候，实参需要调用赋值构造函数将形参复制，
* 从而造成递归调用复制函数造成栈溢出
*/

A(const A& other){ value = other.value; }

{% endhighlight %}

### 3.赋值运算符函数

函数结束前返回实例自身的引用（*this），这样才可以允许连续赋值。

{% highlight c++ %}

//书中的实现方法
CMyString& CMyString::operator =(const CMyString &str){
    if(this != &str){
        CMyString strTemp(str);

        char* pTemp = strTemp.m_pData;
        strTemp.mpData = m_pData;
        m_pData = pTemp;
    }
}

{% endhighlight %}


    FIN 5.12/10.21