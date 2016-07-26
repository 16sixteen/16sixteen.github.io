---
layout:     post
title:      "装饰模式实现装备附魔"
subtitle:   "UNET学习 装备附魔 装饰模式"
date:       2016-07-15 16:30:00
categories: Unity3d
tags:       Unity3d 设计模式
author:     "SixTeen"
header-img: "img/unity3d.jpg"
published:   true
---

使用[装饰模式](/DesignPattern/design_pattern_3)来简单实现游戏中的装备附魔系统

## 类图

![class_1](/img/unity3d/daylearning/7.15/class.png)

武器接口中有2个方法，第一个方法是获取武器的伤害，另一个方法是获取武器的名字。我们知道一般附魔的武器的名字都会在武器前面加一个定冠词，例如:```"木剑" ===> "燃烧的木剑"```，这里装饰器也采用类似的方法，返回对应的名字。在装饰器（附魔接口）中，增加一个方法，是附魔对应的特殊效果，这里我定义火附魔是增加10%的伤害。

## 代码

{% highlight c# %}

//weapon.cs

using UnityEngine;
using System.Collections;

public interface weapon {
    float getDamage();
    string getName();
}

//concreteWeapon
public class woodSword : weapon{
    private float damage = 1;
    private string name = "Wood Sword";
    public float getDamage() {
        return damage;
    }
    public string getName() {
        return name;
    }
}

//decorator
public interface enchant : weapon {
    void getExtraEffect();
}

//concreteDecorator
public class fireEnchant : enchant {
    private float fireExtraDamage = 0.1f;
    private string name = "glow";
    private float extraDamage;
    private weapon wp;
    public fireEnchant(weapon w) {
        wp = w;
        getExtraEffect();
    }
    public float getDamage() {
        return extraDamage + wp.getDamage();
    }
    public string getName() {
        return name + " " + wp.getName();
    }
    public void getExtraEffect() {
        extraDamage = wp.getDamage() * fireExtraDamage;
    }
}

{% endhighlight %}

{% highlight c# %}

//test.cs
using UnityEngine;
using System.Collections;

public class test : MonoBehaviour {

    weapon myWeapon;

    void Start () {
        weapon w = new woodSword();
        Debug.Log(w.getName() + " damage is : " + w.getDamage());
        myWeapon = new fireEnchant(w);
        Debug.Log(myWeapon.getName() + " damage is : " + myWeapon.getDamage());
    }
}

{% endhighlight %}

把test.cs挂载在一个空对象上，执行，效果如下：

![result](/img/unity3d/daylearning/7.15/result.png)

    FIN 2016.7.25/17.03