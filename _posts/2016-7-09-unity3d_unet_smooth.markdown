---
layout:     post
title:      "Unity3d UNET平滑"
subtitle:   "UNET学习 简单角色控制器"
date:       2016-07-09 14:33:00
categories: Unity3d
tags:       Unity3d
author:     "SixTeen"
header-img: "img/unity3d.jpg"
published:   true
---

* TOC
{:toc}

## 使用插值让变化平滑

即便是unet是局域网组建，传输的延迟可以忽略不计，但是每一秒传输的次数却会使得我们在观看效果的时候会觉得对象在瞬移。插值是能让这种瞬间移动变得平滑过渡的一个数学方法。在2次传输的时间差内，用离散的2个点的值，插值去预测计算出连续的值，让游戏达到看起来连续的效果。

## UNET中的Command

Command其实是一种RFC策略，调用远程主机的方法，在客户端中的调用会打包发到服务器中执行。

## SyncVar

SyncVar标明一个变量，发生变化会同步到别的客户端。

## 代码

把player prefab中用的自带的network transform替换成下面脚本即可运行。

{% highlight c# %}

using UnityEngine;
using UnityEngine.Networking;
using System.Collections;

public class unetSync : NetworkBehaviour {

    [SyncVar]public Vector3 serverPosition = Vector3.zero;
    [SyncVar]public Quaternion serverRotation; 
    public float speed = 0;

    public void lerp() {
        transform.position = Vector3.Lerp(transform.position, serverPosition, Time.fixedDeltaTime * speed);
        transform.rotation = Quaternion.Slerp(transform.rotation, serverRotation, Time.fixedDeltaTime * speed);
    }

    [Command]
    public void CmdupdatePosition(Vector3 pos,Quaternion rot) {
        serverPosition = pos;
        serverRotation = rot;
    }

    void FixedUpdate () {
        if (isLocalPlayer) {
            CmdupdatePosition(transform.position,transform.rotation);
        } else {
            lerp();
        }
    }
}

{% endhighlight %}

在网上看了一些资料，使用unet一般用于制作局域网游戏，游戏的客户端也是服务器端，有点像cs开房的那种局域网游戏的感觉。unet在服务端，客户端都保存了同步的对象，标明的SYNCVAR的变量也会同步到客户端。

> [视频教程Unity3D多人游戏开发：UNET快速入门](http://tieba.baidu.com/p/4314320473?see_lz=1)

    FIN 2016.7.18/15.17