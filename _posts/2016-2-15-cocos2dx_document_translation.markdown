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

# 欢迎使用cocos文档

我们知道学习新东西很困难，尤其是当你没法找到能帮你解决你现有问题的有用的文档的时候。这份文档的设计就是为了帮助你学习如何通过使用cocos家族的产品去写游戏。你能把它当做一个引用来从头到尾的阅读。

# 篇章2：基础的cocos2d-x概念

这章节是你开始学习cocos2d-x，准备去做你理想的游戏。不用担心，这将会非常有趣。

cocos2d-x是一个跨平台游戏引擎。一个游戏引擎是一款提供游戏所需所有公共功能的软件。你也许曾经听说过引用api或者框架，在这篇引导里，我们称它为游戏引擎。

游戏引擎包括许多组件，这些组件一起用于帮助加快开发时间，提供比更好的平台。一个游戏引擎通常包含一些或者全部的一下组件：渲染器（renderer），2d/3d图形（2d/3d graphics），碰撞检测(collision detection)，物理引擎（physics engine），声音（sound），控制器支持（controller support），动画（animations）等等。游戏引擎通常支持多平台，因此能帮助你很轻松的将你游戏扩展到各个平台。

cocos2d-x也是一款游戏引擎，他提供简单的api使得很容易实现跨手机和桌面的游戏。通过封装来提供更容易使用的api，你可以专注于开发你的游戏而不用过多的担心底层的具体实现。

cocos2d-x提供Scene, Transition, Sprite, Menu, Sprite3D, Audio等等的对象。它包含了所有你游戏中需要用到的对象。

## 主要组件

cocos2d-x的核心是Scene,Node,Sprite,Menu,Action对象。看看你喜欢的游戏，你会发现所有的组件都会是其中一种对象。

下图是一个你可能曾经玩过的有趣的小游戏。

![p1](http://www.cocos2d-x.org/docs/programmers-guide/2-img/2n_main.png)

我们对截图里的组件进行划分。

![p2](http://www.cocos2d-x.org/docs/programmers-guide/2-img/2n_annotated_scaled.png)

你可以看到一个菜单，一些精灵，标签，所有的这些都能在cocos2d-x里找到对应的对象。你可以打开你的一些游戏，看看这些游戏里有哪些组件，你会发现在cocos2d-x里能找到对应的对象来实现。

## Director（导演）

cocos2d-x使用导演的概念，就像在电影中一样，导演控制操作流程和告诉接受者要做的事情。试着想象你是制片人，你告诉导演应该做什么。Director的任务就是控制Scene的交替。Director是共享单例模式（shared singleton）。

下图是一个经典游戏的流程，Director的任务就是关注你游戏中场景的转换。

![p3](http://www.cocos2d-x.org/docs/programmers-guide/2-img/scenes.png)

你是你游戏的导演，你决定什么时候发生怎样发生什么事情（what,when,how）.

## Scene （场景）

在你的游戏里，你也许会想要一个主菜单，一些中间场景和结束场景。那你要如何组织所有的这些分离的块呢？场景。电影也是一样，把画面分到不同的场景，不同的场景有着故事主线。如果我们应用到游戏中，那么我们最少设置几个场景来使用。

看回之前使用的一张图片：

![p1](http://www.cocos2d-x.org/docs/programmers-guide/2-img/2n_main.png)

这就是主菜单，它就是一个独立的场景。这个场景由数个组件组成。场景是由渲染器绘制的。渲染器负责渲染精灵和其他对象到屏幕上。要更充分理解的话，我们要稍稍谈谈scene graph。

## Scene Graph （场景图）

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

## Sprite （精灵）

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

## Actions （动作）

创建一个场景然后添加精灵对象在屏幕上仅仅是你需要完成的工作的一部分。让一个游戏更生动你必须让事物都动起来。动作对象是每个游戏中不可分割的一部分。动作对象允许你在一个时间段内转变节点对象。想将一个精灵从一个点移动到另一个点，然后使用回调函数？没问题。你可以创建一个一系列动作的Sequence（顺序动作）。你可以改变节点的属性，例如位置，旋转，大小。例如：MoveBy,Rotate,Scale.所有的游戏都使用动作。

举个例子：

![pac1](http://www.cocos2d-x.org/docs/programmers-guide/2-img/2n_level1_action_start.png)

5秒之后，精灵会移动到一个新的位置。

![pac2](http://www.cocos2d-x.org/docs/programmers-guide/2-img/2n_level1_action_end.png)

动作对象非常容易创建：

{% highlight c++ %}

auto mySprite = Sprite::create("Blue_Front1.png");

//向右移动一个精灵50个像素点，向上10个像素点，历时2秒
auto moveBy = MoveBy::create(2,Vec(50,10));
mySprite->runAction(moveBy);

//将精灵移动去一个指定的位置，历时2秒
auto moveTo = MoveTo::create(2,Vec(50,10));
mySprite->runAction(moveTo);

{% endhighlight %}

Sequences and Spawns

顺序是多个动作对象以指定的顺序运行。如果需要将顺序反序运行？没问题，cocos2d-x可以让你不用额外的工作就能处理这个问题。

举个例子：

![s1](http://www.cocos2d-x.org/docs/programmers-guide/2-img/2_sequence_scaled.png)

创建的代码非常容易：

{% highlight c++ %}

auto mySprite = Node::create();
//移动到50,10,历时2秒
auto moveTo1 = MoveTo::create(2,Vec2(50,10));

//从现在的位置，向右位移100，向上位移10，历时2秒
auto moveBy1 = MoveBy::create(2,Vec2(100,10));

//移动到150，10的位置，历时2秒
auto moveTo2 = MoveTo::create(2,Vec2(150,10));

//创建延时
auto delay = DelayTime::create(1);

mySprite->runAction(Sequence::create(moveTo1,delay,moveBy1,delay.clone(),moveTo2,nullptr));

{% endhighlight %}

上面的例子是运行一个顺序，但如果想要同时运行所有的动作呢，cocos2d-x支持这个，使用的是Spawn。Spawn会同时运行所有的动作，有一些动作的持续时间可能更长，所以所有的动作不一定在同一时刻完成。

{% highlight c++ %}

auto myNode = Node::create();

auto moveTo1 = MoveTo::create(2,Vec2(50,10));
auto moveBy1 = MoveBy::create(2,Vec2(100,10));
auto moveTo2 = MoveTo::create(2,Vec2(150,10));

myNode->runAction(Spawn::create(moveTo1,moveBy1,moveTo2,nullptr));

{% endhighlight %}

为什么Spawn会发生？可能当你的人物获得更强大的力量之后，就能同时进行多个动作。也许击败boss之后会获得这样的强大力量。

## Parent Child Relationship(父子关系)

cocos2d-x使用父子关系。这意味着改变父亲节点的属性，同样会应用到儿子处。试着想象一个单独的节点和一个有孩子的节点：

![pcr1](http://www.cocos2d-x.org/docs/programmers-guide/2-img/2n_parent.png)

![pcr2](http://www.cocos2d-x.org/docs/programmers-guide/2-img/2n_parent_rotation.png)

{% highlight c++ %}

auto myNode = Node::create();
myNode->setRotation(50);

{% endhighlight %}

![pcr3](http://www.cocos2d-x.org/docs/programmers-guide/2-img/2n_parent_scaled.png)

{% highlight c++ %}

auto myNode = Node::create();
myNode->setScale(2.0);

{% endhighlight %}

## Logging as a way to output messages(通过log来输出信息)

很多时候，当你的应用运行的时候，你可能希望看到一些信息输出到控制台来方便debug。使用```log()```可以达到这个目的。

{% highlight c++ %}

//输出string
log("This would be outputted to the console");

//a string and a variable
string s = "variable";
log("string is %s",s);

//a double and a variable
double dd = 42;
log("double is %f",dd);

//an integer and a variable
int i = 6;
log("integer is %d",i);

//a float and a variable
float f = 2.0f;
log("float is %f",f);

//a bool and a variable
bool b = true;
if b == true then
    log("bool is true");
else 
    log("bool is false");
end

{% endhighlight %}

当然，你可以使用你更熟悉的std::cout来代替log(),log()适合更复杂的格式输出。

## Conclusion

我们已经学习了cocos2d-x的很多概念。不用担心。跟着你的思绪脚踏实地慢慢前进。cocos2d-x和编程是没有一夜通的捷径的。练习和理解是最有效的！


# 篇章3：精灵

## 什么是精灵

精灵是可以通过改变他的属性（例如旋转，位置，大小，颜色）的2d图片。

## 创建精灵

创建精灵有非常多种方法，使用哪种方法完全取决于你的需要。你可以通过使用PNG,JPEG,TIFF等格式的图片来创建精灵。

一个精灵可以通过制定一个图片文件来创建：

{% highlight c++ %}

auto mySprite = Sprite::create("mysprite.png");

{% endhighlight %}

![sp1](http://www.cocos2d-x.org/docs/programmers-guide/3-img/i1.png)

这个语句使用mysprite.png这张图片创建了一个精灵。这个精灵使用的是整一张图片，精灵和图片的尺寸一样。如果图片文件是200x200,那么精灵也是200x200.

### 创建矩形精灵

在前面的例子中，创建的精灵和原来的图片文件的大小是一致的。如果你仅仅想使用图片文件的一部分去创建精灵，你可以使用一个矩形去指定一部分作为你的精灵。

Rect有4个参数，源图x(origin x)，源图y(origin y)，宽度(width)，高度(height)

{% highlight c++ %}

auto mySprite = Sprite::create("mysprite.png",Rect(0,0,40,40));

{% endhighlight %}

![sp2](http://www.cocos2d-x.org/docs/programmers-guide/3-img/i4.png)

矩形的起始点是从左上角开始的。这和场景布置的坐标系不同，场景的坐标系是从左下角开始的。精灵仅仅是图片文件的一部分，上图的精灵仅仅使用了源图从左上角开始的40x40.

如果你没有使用Rect，cocos2dx会自动的使用图片文件的宽度和高度作为精灵的大小。看看下面的例子，如果我们使用尺寸为200x200的图片，这2个语句的效果是一样的。

{% highlight c++ %}

auto mySprite = Sprite::create("mysprite.png");
auto mySprite = Sprite::create("mysprite.png",Rect(0,0,200,200));

{% endhighlight %}

## Creating a Sprite from a Sprite Sheet（通过精灵片创建精灵）

一个精灵片是一种把多个精灵放到一个文件里的文件。和每个精灵一个文件相比，将所有的精灵整合到一个文件中能减少文件的容量。这意味着你能更有效的减少内存的使用，文件的大小和载入的时间。

使用精灵片能更好的批处理draw calls。你可以在优化偏里阅读更多这方面的知识。这是工业中其中一种非常有效的优化方法。

当一个精灵片第一次加载的时候，他会作为一个整体加载到```SpriteFrameCache```。```SpriteFrameCache```是一个缓存类，存有```SpriteFrame```对象，为了将来更快的访问。```SpriteFrame```第一次载入之后就会保存在```SpriteFrameCache```中。

下图是一个精灵片：

![sp3](http://www.cocos2d-x.org/docs/programmers-guide/3-img/3_1.png)

下图是从单个精灵整合成一个精灵片：

![sp4](http://www.cocos2d-x.org/docs/programmers-guide/3-img/spritesheet.png)

你可以看到，精灵片减少了不必要的空间和综合了所有的精灵在一个文件中。

### Loading a Sprite Sheet（加载精灵片）

加载精灵片到```SpriteFrameCache```，大概发生在AppDelegate中：

{% highlight c++ %}

//加载精灵片
auto spritecache = SpriteFrameCache::getInstance();

spritecache->addSpriteFramesWithFile("sprites.plist");

{% endhighlight %}

现在你有一个加载了精灵片的```SpriteFrameCache```，我们能利用他创建精灵对象了。

### Creating a Sprite from SpriteFrameCache

{% highlight c++ %}

//我们的.plist文件里每个对应的精灵有他的名字
//我们通过名字mysprite来从精灵片中获取精灵
auto mysprite = Sprite::createWithSpriteFrameName("mysprite.png");

{% endhighlight %}

![sp4](http://www.cocos2d-x.org/docs/programmers-guide/3-img/i3.png)

Creating a Sprite from a SpriteFrame

另一种创建精灵的方法是从```SpriteFrameCache```中获取```SpriteFrame```，然后通过```SpriteFrame```创建精灵。

{% highlight c++ %}

//最终效果和前面的例子一致
//和前一个例子不同的是，这次是从cache中检索SpriteFrame
auto newspriteFrame = SpriteFrameCache::getInstance()->getSpriteFrameByName("Blue_Front1.png");
auto newSprite = Sprite::createWithSpriteFrame(newspriteFrame);

{% endhighlight %}

![sp5](http://www.cocos2d-x.org/docs/programmers-guide/3-img/i3.png)

### Tools for creating Sprite Sheets

手动的制作精灵片是一个鼓噪烦闷的过程。幸运的是有一些工具能帮助我们自动的生成精灵片。这些工具提供多种方法去让你更好的制作最适合的精灵片。

[Cocos Studio](http://www.cocos2d-x.org/wiki/Cocos_Studio)<br/>
[ShoeBox](http://renderhjs.net/shoebox/)<br/>
[Texture Packer](https://www.codeandweb.com/texturepacker)<br/>
[Zwoptex](https://zwopple.com/zwoptex/)<br/>

### Sprite Manipulation（精灵控制器）

当你创建一个精灵后，你就可以访问控制这个精灵的各种属性。

创建精灵

{% highlight c++ %}

auto mySprite = Sprite::create("mysprite.png");

{% endhighlight %}

![sp6](http://www.cocos2d-x.org/docs/programmers-guide/3-img/i1.png)

#### Anchor Point and Position(锚点和位置)

锚点是当你设置位置时用来决定精灵的对应位置的一个点。锚点仅仅影响可以变换的属性（这包括scale, rotation, skew，不包括颜色和透明度）。锚点使用左下角坐标系统。这意味着xy坐标值的起始点是左下角。默认的，所有的节点对象默认的锚点的值是（0.5,0.5）。

设置锚点的方法非常简单：

{% highlight c++ %}

//所有精灵默认的锚点
mySprite->setAnchorPoint(0.5,0.5);

//左下
mySprite->setAnchorPoint(0,0);

//左上
mySprite->setAnchorPoint(0,1);

//右下
mySprite->setAnchorPoint(1,0);

//右上
mySprite->setAnchorPoint(1,1);

{% endhighlight %}

效果是这样的：

![sp7](http://www.cocos2d-x.org/docs/programmers-guide/3-img/i6.png)

#### Sprite properties effected by anchor point(锚点影响的精灵属性)

锚点仅仅影响那些能发生转换的属性。例如：Position,Scale,rotation,skew.

Position

一个精灵的位置受到锚点的影响。锚点是设置位置的起始点。注意红线和精灵的位置与锚点的关系。我们改变锚点的值，精灵的位置发生了改变。我们没有使用```setPosition()```去改变精灵的位置，这些改变完全是因为锚点发生了改变而改变的。

![sp6](http://www.cocos2d-x.org/docs/programmers-guide/3-img/i9.png)

设置精灵的方法不仅仅只有设置锚点这一种方法。精灵对象也可以通过```setPosition()```这个方法来改变。

{% highlight c++ %}

//x = 100, y = 200
mySprite->setPosition(Vec2(100,200));

{% endhighlight %}

Rotation

通过一个正或者负的度数来改变精灵的旋转。正数代表顺时针，负数代表逆时针。默认值是0.

{% highlight c++ %}

//+20degrees
mySprite->setRotation(20.0f);

//-20degrees
mySprite->setRotation(-20.0f);

//+60degrees
mySprite->setRotation(60.0f);

//-60degrees
mySprite->setRotation(-60.0f);

{% endhighlight %}

![sp8](http://www.cocos2d-x.org/docs/programmers-guide/3-img/i8.png)

Scale

可以改变x轴大小，y轴大小，或者xy轴同时改变来改变精灵的大小。x轴y轴的默认值都是1.0。

{% highlight c++ %}

//同时在x轴，y轴变为2倍
mySprite->setScale(2.0);

//仅仅x轴变为2倍
mySprite->setScaleX(2.0);

//仅仅y轴变为2倍
mySprite->setScaleY(2.0);

{% endhighlight %}

![spx](http://www.cocos2d-x.org/docs/programmers-guide/3-img/i5.png)

Skew

可以改变x轴大小，y轴大小，或者xy轴同时改变来改变精灵的歪斜。x轴y轴的默认值都是0。

{% highlight c++ %}

//调整x的歪斜为20.0
mySprite->setSkewX(20.0f);

//调整y的歪斜为20.0
mySprite->setSkewY(20.0f);

{% endhighlight %}

![spx](http://www.cocos2d-x.org/docs/programmers-guide/3-img/i7.png)

Sprite properties not affected by anchor point











>资料均来源于<br/>[Chapter 2: Basic Cocos2d-x Concepts](http://www.cocos2d-x.org/docs/programmers-guide/2/index.html)<br/>[Chapter 3: Sprites](http://www.cocos2d-x.org/docs/programmers-guide/3/index.html#creating-sprites)<br/>