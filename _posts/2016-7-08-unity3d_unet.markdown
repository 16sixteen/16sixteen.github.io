---
layout:     post
title:      "Unity3d 初识UNET"
subtitle:   "UNET学习 简单角色控制器"
date:       2016-07-08 11:09:00
categories: Unity3d
tags:       Unity3d
author:     "SixTeen"
header-img: "img/unity3d.jpg"
published:   true
---

* TOC
{:toc}

## UNET是什么

> A network library that help you write your multiplayer game with only few line of codes.

UNET是一个让我们快速开发多人在线游戏的库。

## UNET 中的api

UNET中有2种api，一种是高层api(应用层，主机客户端型)，还有就是底层api（传输层，sockets）

## 简单使用UNET

### 1.在场景中创建一个plane作为我们的游戏地面。

### 2.然后创建一个cube并附带简单的控制器脚本作为player prefab。(需要添加network Identity组件)

![](/img/unity3d/daylearning/7.9/cube_component.png)

#### 简单的角色控制器

{% highlight c# %}

using UnityEngine;
using System.Collections;

public class cubemove : MonoBehaviour {

    public float rotationSpeed;
    public float moveSpeed;

    // Update is called once per frame
    void Update () {
        //获取输入的方向向量
        Vector3 forward = new Vector3(Input.GetAxis("Horizontal"), 0, Input.GetAxis("Vertical"));
        //取一个最大的值作为输入的值
        float inputMotionValue = Mathf.Max(Mathf.Abs(forward.x), Mathf.Abs(forward.z));

        if (inputMotionValue > 0) {
            //Creates a rotation that looks along forward with the the head upwards along upwards
            //y轴朝向up，z轴朝向view
            Quaternion lookRotation = Quaternion.LookRotation(forward);
            //球型插值
            //Spherically interpolates from towards to by t.
            //球形插值，通过t值from向to之间插值。
            //transform.rotation = Quaternion.Slerp (from.rotation, to.rotation, Time.time * speed);
            transform.rotation = Quaternion.Slerp(transform.rotation, lookRotation, rotationSpeed * inputMotionValue * Time.deltaTime);
            //向前移动
            transform.Translate(Vector3.forward * inputMotionValue * moveSpeed * Time.deltaTime);
        }
    }
}

{% endhighlight %}

#### 插值

1.插值：插值在我的理解，根据2个给定的离散的点，计算出中间的值，可以使得整个过程平滑（smooth）

### 3.然后创建一个空对象，并给空对象加上Network Manager和Network Manager HUD组件

![unity_network_component](/img/unity3d/daylearning/7.9/unet_network_component.png)

#### Network Manager

1.Network Info:主要是设置一些服务端信息的，例如address，port等等

2.Spawn Info:主要设置场景中需要同步实例化的一些对象，例如我发射子弹，在别人的客户端中也应该看到子弹。(Spawn Info必须设置player prefab，player prefab必须添加network identity组件)

## 控制权与同步问题

当我们build and run，同时ide也运行一个，1个点击```lan Host(H)```，一个点击```LAN client(C)```,就可以发现我们的场景中已经出现2个player了。

![demo](/img/unity3d/daylearning/7.9/unet_demo_1.png)

可是当我们操纵player我们就会发现，我们居然能同时操纵2个player!而且同步对方位置的同步存在问题！

### 控制权的解决

我们之所以可以操纵别人，是因为我们的player挂载的是相同的脚本，接收相同的输入实际，一个最简单的方法就是我们将我们的脚本继承自```NetworkBehaviour```。并使用里面的isLocalPlayer判断是不是本地客户端生成的player。

{% highlight c# %}

using UnityEngine;
using UnityEngine.Networking;
using System.Collections;

public class cubemove : NetworkBehaviour {
    void Update () {
        if (!isLocalPlayer) { return; }
    }
}

{% endhighlight %}

### 同步问题的解决

我们给我们的player添加一个新的组件：```Network Transform```,将里面的Transform Sync Mod设为Sync Transform。这样就可以同步了。

## 最终效果：

![gif](/img/unity3d/daylearning/7.9/unet.gif)


> [[视频教程]Unity3D多人游戏开发：UNET快速入门](http://tieba.baidu.com/p/4314320473?see_lz=1)

    FIN 2016.7.11/17.18

