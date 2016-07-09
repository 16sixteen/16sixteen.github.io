---
layout:     post
title:      "Unity3d canvas问题与DrawLine"
subtitle:   "profile 性能优化"
date:       2016-07-07 20:41:00
categories: Unity3d
tags:       Unity3d
author:     "SixTeen"
header-img: "img/unity3d.jpg"
published:   true
---


* TOC
{:toc}

## 1.canvas的显示问题：（个人理解）

①canvas之间是不存在遮挡关系的，在同一个order in layer（同一个层中）的canvas不会因为3d位置的远近关系而遮挡，因为他们是在同一个层中渲染的。因为游戏中我使用了canvas作为墙（正确的应该在canvas后设置一个plane，这样canvas就不会穿透'墙'显示出来），因此我将'墙'的order in layer增加了（后渲染），因此就墙后的canvas就不会被看到了。（canvas的setActive会造成大量的GC，因此我选择将canvas移到墙外来减少setActive（false）造成的开销）。

## 2.Debug.DrawLine(vector3 pos1,vector3 pos2, color)

项目中我使用划线的方法确定人物的大概视角，大概确定需要显示的物体，根据这个修改显示的算法。

camera中的field of View大概就是视角，初始是60度，我们根据这个60度画出红线。

![大概效果](/img/unity3d/daylearning/7.8/drawline.png)

### 代码

{% highlight c# %}

Debug.DrawLine(cam.transform.position, (cam.transform.forward * 15 + cam.transform.position), Color.red);
Quaternion rotation1 = Quaternion.Euler(new Vector3(0, 60, 0));
Quaternion rotation2 = Quaternion.Euler(new Vector3(0, -60, 0));
Debug.DrawLine(cam.transform.position, (rotation1 * cam.transform.forward * 15 + cam.transform.position), Color.red);
Debug.DrawLine(cam.transform.position, (rotation2 * cam.transform.forward * 15 + cam.transform.position), Color.red);

{% endhighlight %} 

### quaternion

关于quaternion，[quaternion-wiki](https://en.wikipedia.org/wiki/Quaternion),大概就是超复数（复数是a+bi,超复数是a+bi+cj+dk....）数学方面的看wiki，我看不懂，但是在unity3d中，四元数可以很方便的帮我们将一个3维的点旋转。例如：```Quaternion.Euler(new Vector3(0, 60, 0))* cam.transform.forward```，就是将forward这个点（方向向量）绕y轴旋转60度。

> 参考资料： 1.[Quaternion.Euler](http://www.ceeger.com/Script/Quaternion/Quaternion.Euler.html)