---
layout:     post
title:      "Cocos2d-x 官方文档的阅读和翻译"
subtitle:   "cocos2d-x 官方文档"
date:       2016-02-15 20:45:00
categories: cocos2d-x
tags:       cocos2d-x
author:     "SixTeen"
header-img: "img/cocos2dx.png"
---

欢迎使用cocos文档

我们知道学习新东西很困难，尤其是当你没法找到能帮你解决你现有问题的有用的文档的时候。这份文档的设计就是为了帮助你学习如何通过使用cocos家族的产品去写游戏。你能把它当做一个引用来从头到尾的阅读。

篇章2：基础的cocos2d-x概念

这章节是你开始学习cocos2d-x，准备去做你理想的游戏。不用担心，这将会非常有趣。

cocos2d-x是一个跨平台游戏引擎。一个游戏引擎是一款提供游戏所需所有公共功能的软件。你也许曾经听说过引用api或者框架，在这篇引导里，我们称它为游戏引擎。

游戏引擎包括许多组件，这些组件一起用于帮助加快开发时间，提供比更好的平台。一个游戏引擎通常包含一些或者全部的一下组件：渲染器（renderer），2d/3d图形（2d/3d graphics），碰撞检测(collision detection)，物理引擎（physics engine），声音（sound），控制器支持（controller support），动画（animations）等等。游戏引擎通常支持多平台，因此能帮助你很轻松的将你游戏扩展到各个平台。

cocos2d-x也是一款游戏引擎，他提供简单的api使得很容易实现跨手机和桌面的游戏。通过封装来提供更容易使用的api，你可以专注于开发你的游戏而不用过多的担心底层的具体实现。

cocos2d-x提供Scene, Transition, Sprite, Menu, Sprite3D, Audio等等的对象。它包含了所有你游戏中需要用到的对象。

主要组件

cocos2d-x的核心是Scene,Node,Sprite,Menu,Action对象。看看你喜欢的游戏，你会发现所有的组件都会是其中一种对象。

下图是一个你可能曾经玩过的有趣的小游戏。

![p1](http://www.cocos2d-x.org/docs/programmers-guide/2-img/2n_main.png)

我们对截图里的组件进行划分。

![p2](http://www.cocos2d-x.org/docs/programmers-guide/2-img/2n_annotated_scaled.png)

你可以看到一个菜单，一些精灵，标签，所有的这些都能在cocos2d-x里找到对应的对象。你可以打开你的一些游戏，看看这些游戏里有哪些组件，你会发现在cocos2d-x里能找到对应的对象来实现。

Director（导演）

cocos2d-x使用导演的概念，就像在电影中一样，导演控制操作流程和告诉接受者要做的事情。试着想象你是制片人，你告诉导演应该做什么。Director的任务就是控制Scene的交替。Director是共享单例模式（shared singleton）。

下图是一个经典游戏的流程，Director的任务就是关注你游戏中场景的转换。

![p3](http://www.cocos2d-x.org/docs/programmers-guide/2-img/scenes.png)

你是你游戏的导演，你决定什么时候发生怎样发生什么事情（what,when,how）.

Scene
在你的游戏里，你也许会想要一个主菜单，一些中间场景和结束场景。那你要如何组织所有的这些分离的块呢？场景。电影也是一样，把画面分到不同的场景，不同的场景有着故事主线。如果我们应用到游戏中，那么我们最少设置几个场景来使用。

看回之前使用的一张图片：

![p1](http://www.cocos2d-x.org/docs/programmers-guide/2-img/2n_main.png)

这就是主菜单，它就是一个独立的场景。这个场景由数个组件组成。场景是由渲染器绘制的。渲染器负责渲染精灵和其他对象到屏幕上。要更充分理解的话，我们要稍稍谈谈scene graph。

Scene Graph

场景图是一个存储整理场景的数据结构。场景图是一个包含节点对象的树。

![p4](http://www.cocos2d-x.org/docs/programmers-guide/2-img/tree.jpg)

这看起来非常复杂。我肯定你会问为什么要注意这些技术细节，cocos2dx不是封装起来了吗？但实际上着对于你理解场景是怎么由渲染器描绘出来是非常重要的。

当你开始添加节点，精灵，动画到你游戏的时候，你想让这些按照你所想的那样被绘制出来。但如果不符合你的要求的时候，当你的精灵被背景遮挡的时候，你想他移到最前面的时候，应该怎么做？不是一件很难的事情，你只需要退回去，把运行的场景图在纸上画一画，我敢肯定你会非常快的找到你的错误。

当场景图是一棵树的时候，你可以遍历一遍这棵树。cocos2d-x使用先序遍历算法。先序遍历算法是先遍历树的左子树，然后根节点，最后是右子树。当最右边的子树被最后渲染，因此他显示在画面最前面。

![p6](http://www.cocos2d-x.org/docs/programmers-guide/2-img/in-order-walk.png)

场景图很容易展示。

![p7](http://www.cocos2d-x.org/docs/programmers-guide/2-img/2n_main.png)

这个场景将会以一棵树的形式绘制。如图：

![p8](http://www.cocos2d-x.org/docs/programmers-guide/2-img/2n_mainScene-sceneGraph.png)

另外一点要说的是，这些元素都有一个z轴坐标在他们的左子树，当元素的轴坐标是正数的时候，他会在根的右边，把这点加到你的思考里。当然，你可以把各种元素按任意顺序加入，他们会自动按z轴大小排序，构成合法的树。

有了这些知识，我们可以知道场景就是一个点对象的集合。我们可以把场景分成下面的部分然后看看场景图如何使用z轴坐标去把他们放到场景里：

![p9](http://www.cocos2d-x.org/docs/programmers-guide/2-img/layers.png)

从图里可以看到，左边的场景实际是多个节点对象通过不同的z轴一层一层绘制的。

在cocos2d-x里，你可以通过使用```addChild()```这个API来构建场景图：

{% highlight c++ %}

//添加一个title_node节点到scene里
//scene是根，因为z轴是负数，他会加到根节点的左边
scene->dddChild(title_node,-2);

//当你没有标明z轴坐标大小的时候，默认为0
scene->addChild(label_node);

//添加一个z轴大小为1的节点到场景里
//他会添加到跟节点的右边
scene->addChild(sprite_node,1);

{% endhighlight %}

Sprite

所有的游戏都有精灵对象，也许只是你没有意识到他们的存在。精灵是一种你可以在屏幕内任意移动的对象。你可以操纵他们。你在游戏里控制的主要角色可能就是一个精灵。我知道你可能会认为任何图形对象都是精灵，其实不是。因为只有你需要四处移动的对象才是精灵，不四处移动的仅仅只是一个节点对象。

用一张图片来指出期中的精灵和节点：

![p9](http://www.cocos2d-x.org/docs/programmers-guide/2-img/2n_main_sprites_nodes.png)

精灵在游戏里是一个非常重要的对象。你可能有主要的角色是某一些图片中的一类，那就是精灵。

精灵非常容易创建，他们有一些需要配置的属性，例如：位置(position)，旋转(rotation)，大小(scale)，透明度(opacity)，色彩(color)等等。

{% highlight c++ %}

//这是使用一张图片去创造一个精灵
auto mySprite = Sprite::create("mysprite.png");

//这是如何改变精灵的属性
mySprite->setPosition(Vec2(500,0));
mySprite->setRotation(40);
//设置scale是同时缩放x和y轴的
mySprite->setScale(2.0);
//设置锚点
mySprite->setAnchorPoint(Vec2(0,0));

{% endhighlight %}

下面说明一下各个属性:

![ps1](http://www.cocos2d-x.org/docs/programmers-guide/2-img/2n_level1_action_start.png)

我们通过```mySprite->setPosition(Vec2(500,0));```设置精灵的位置

![ps2](http://www.cocos2d-x.org/docs/programmers-guide/2-img/2n_level1_action_end.png)

注意到精灵的位置已经发生改变，从原来的位置移动到了我们设置的新位置。

我们现在设置新的旋转，```mySprite->setRotation(40);```

![ps3](http://www.cocos2d-x.org/docs/programmers-guide/2-img/2n_level1_action_end_rotation.png)

你可以发现精灵发生了旋转，并且旋转是我们所指定的

现在我们设置新的大小，```mySprite->setScale(2.0);```

![ps4](http://www.cocos2d-x.org/docs/programmers-guide/2-img/2n_level1_action_end_scale.png)

同样的，我们可以发现精灵现在变得和我们代码所设置的一样。

最后，所有的节点对象都有锚点（精灵是节点对象的子类）。锚点是设定你的角色使用那一点作为坐标原点。

看看实际效果：
![pa1](http://www.cocos2d-x.org/docs/programmers-guide/2-img/2n_level1_anchorpoint_0_0.png)

![pa2](http://www.cocos2d-x.org/docs/programmers-guide/2-img/2n_level1_anchorpoint_05_05.png)

![pa3](http://www.cocos2d-x.org/docs/programmers-guide/2-img/2n_level1_anchorpoint_1_1.png)

图中的红点就是锚点。你可以发现，锚点对于设置节点位置是非常重要的。你甚至可以动态调节锚点来模拟你的游戏效果。

Actions

