---
layout:     post
title:      "设计模式-设计模式入门（5）"
subtitle:   "简单工厂模式"
date:       2016-07-27 15:34:00
categories: DesignPattern
tags:       设计模式
author:     "SixTeen"
header-img: "img/designPattern.jpg"
published:   true
---

* TOC
{:toc}

### 简单工厂模式

类图 

![](/img/unity3d/daylearning/7.27/class.png)

原来我们必须在客户端处new指定的类，通过简单工厂，我们只需要用工厂制造，条件判断的代码都写在工厂类中，这样我们如果有多个用户使用这个工厂的时候，就可以不用写多次条件判断代码，在加入新产品的时候也不用将每个用户的代码更新，只需要更新工厂中的方法即可。

工厂模式在我的理解中就是生产产品的工厂，如果我们不使用工厂，完全商店本地制造，那么我们开分店的时候，就需要重新搭建一次制造的设备，而使用工厂，我们所有店铺都可以使用这个工厂来生产，而且有新的产品，只要在工厂中修改，所有的商店都有新产品出售。

{% highlight c# %}

//simpleFactory.cs
using UnityEngine;
using System.Collections;

public interface product {
    //加肉
    void addMeat();
    //加酱
    void addPatse();
    //console
    void print();
}

public class product1 : product {
    public void addMeat() {
        Debug.Log("producer1 addMeat");
    }
    public void addPatse() {
        Debug.Log("producer1 addPatse");
    }
    public void print() {
        Debug.Log("product1");
    }
}

public class product2 : product {
    public void addMeat() {
        Debug.Log("producer2 addMeat");
    }
    public void addPatse() {
        Debug.Log("producer2 addPatse");
    }
    public void print() {
        Debug.Log("product2");
    }
}

public class simpleFactory{
    public static product create(string pro) {
        product prod;
        if (pro == "producer1") {
            prod = new product1();
        }
        else{
            prod = new product2();
        }
        return prod;
    }
}

{% endhighlight %}

客户获取产品后可以选择性使用产品的功能，例如这里的产品可以加肉和加酱，商店通过工厂获取了产品之后，可以根据需要来加肉或者加酱。

{% highlight c# %}

//store.cs
using UnityEngine;

public class store : MonoBehaviour {

    void Start () {
        product prod = simpleFactory.create("product1");
        prod.print();
        prod.addMeat();
        prod.addPatse();
        product prod2 = simpleFactory.create("product2");
        prod2.print();
        prod2.addMeat();
        prod2.addPatse();
    }
    
}

{% endhighlight %}

![](/img/unity3d/daylearning/7.27/result1.png)

简单工厂模式把生产环节放到里工厂里，这样就把所有商店需要做的工作放到了一个类之中，一旦生产发生改变，或者增加新产品，我们的修改就很简单。这将商店和具体的产品进行了解耦，商店只需要通过工厂就可以获得想要的产品，而不需要具体的进行new操作。

<br/><br/><br/>

>参考书籍：<br/>《Head First设计模式》

    FIN 2016.7.31/17.05