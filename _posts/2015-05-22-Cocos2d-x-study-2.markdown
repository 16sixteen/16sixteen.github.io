---
layout:     post
title:      "Cocos2d-x 学习笔记（2）"
subtitle:   "cocos2d-x 坐标系"
date:       2015-05-22 21:23:00
author:     "SixTeen"
header-img: "img/post-bg-06.jpg"
---

# Cocos2d-x 学习笔记（2）

标签（空格分隔）： cocos2d-x 坐标系

---

##**Cocos2d-x游戏设计理念**
1.Director:是游戏的大脑，通过它来控制场景跳转，事件分发等功能。
2.Scene:通常一个场景是游戏的一个界面
3.Layer：一个场景包含多个图层，每个图层有各自的角色信息
4.Sprite：可以理解为游戏的每一个角色为Sprite，它时处理图片与动画的基本层
5.Action：角色可以通过Action实现运动，旋转等

##**Cocos2d-x坐标系**

###**UI坐标系**
IOS/Android/Windows SDK中的通用UI坐标系：
•起点坐标(x=0, y=0)位于左上角
•X轴从屏幕最左边开始，由左向右渐增
•Y轴坐标从屏幕最上方开始，由上向下渐增
<font color = "red">
注意：Cocos2d-x中<font color = "blue">触摸返回</font>的坐标为UI坐标
</font>

###**Cocos2d-x坐标系**
Cocos2d-x使用的是Opengl坐标系：
•起点坐标(x=0, y=0)位于左下角
•X轴从屏幕最左边开始，由左向右渐增
•Y轴坐标从屏幕最下方开始，由下向往上增
<font color = "red">
注意：Cocos2d-x中<font color = "blue">setPosition</font>使用的是Cocos2d-x坐标系
</font>

###**1.世界坐标系**
    与GL坐标系相同，是屏幕全局坐标
###**2.本地坐标系**
    是节点（CCNode）的坐标系，原点在节点左下角，x轴向右，y轴向上。cocos2d中的元素是有父子关系的层级结构，我们通过Node的position设定元素的位置使用的是相对与其父节点的本地坐标系而非世界坐标系。最后在绘制屏幕的时候cocos2d会把这些元素的本地坐标映射成世界坐标系坐标。
<font color="red">
    注意：Cocos2d-x中<font color = "blue">setPosition</font>使用的是对于父节点的本地坐标
</font>

###锚点（AnchorPoint）###
锚点可认为是一个对象的中心点，父对象通过把子对象的锚点放到position上来实现布局。


**本地坐标与世界坐标的相互转换：**
```C++
•CCPoint CCNode::convertToNodeSpace(const CCPoint& worldPoint);
•CCPoint CCNode::convertToWorldSpace(const CCPoint& nodePoint);
•CCPoint CCNode::convertToNodeSpaceAR(const CCPoint& worldPoint);
•CCPoint CCNode::convertToWorldSpaceAR(const CCPoint& nodePoint);
```

**Cocos2d-x内存管理机制**
虽然Cocos2d-x是用C++实现，但Cocos2d-x采用的是Object-C的内存管理方式。通过给每个对象维护一个引用计数器，记录该对象当前被引用的次数。当对象增加一次引用时，计数器加1；而对象失去一次引用时，计数器减1；当引用计数为0时，标志着该对象的生命周期结束，自动触发对象的回收释放
和java的自动回收的机制是一样的。


参考文档：
1.TA的pdf
2.http://cn.cocos2d-x.org/