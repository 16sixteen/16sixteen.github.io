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

我们知道学习新东西很困难，尤其是当你没法找到能帮你解决你现有问题的有用的文档的时候。这份文档的设计就是为了帮助你学习如何通过使用cocos家族的产品去写游戏。你能把它当做一个引用来从头到尾的阅读。(官方文档的示例代码中的精灵都是创建之后没有设置位置，没有加入scene中，运行的时候注意自己手动加入)

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

当场景图是一棵树的时候，你可以遍历一遍这棵树。cocos2d-x使用中序遍历算法。中序遍历算法是先遍历树的左子树，然后根节点，最后是右子树。当最右边的子树被最后渲染，因此他显示在画面最前面。

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

Sprite properties not affected by anchor point（不受锚点影响的精灵属性）

有一些精灵属性是不受锚点影响的。例如颜色和透明度。

Color

改变精灵的颜色，这是通过传输一个```Color3B```对象来实现的。Color3B对象是```RGB```值。我们之前没有提过Color3B，Color3B是一个简单的定义了RGB的颜色对象。一个RGB颜色是一个3byte值（0-255）。Cocos2d-x也提供了预定义的颜色，你可以直接使用它们。你可以非常方便快捷的使用它们，一个简单的例子```Color3B::White```,```Color3B::Red```。

{% highlight c++ %}

// set the color by passing in a pre-defined Color3B object.
mySprite->setColor(Color3B::WHITE);

// Set the color by passing in a Color3B object.
mySprite->setColor(Color3B(255, 255, 255)); // Same as Color3B::WHITE

{% endhighlight %}

![spx](http://www.cocos2d-x.org/docs/programmers-guide/3-img/i10.png)

Opacity

一个不透明的对象是不透明的。这个属性的值从0-255.255表示完全不透明，0表示完全透明。0其实意味着不可见。默认的初始值是255。

{% highlight c++ %}

//把透明度设为30，精灵是11.7%的不透明
//30/256=0.1171875
mySprite->setOpacity(30);

{% endhighlight %}

![spx](http://www.cocos2d-x.org/docs/programmers-guide/3-img/i11.png)

Polygon Sprite(多边形精灵)

多边形精灵也是精灵的一种。和一般由2个三角形组成的矩形不同，多边形精灵对象是由多个三角形构成的。

为什么使用多边形精灵？

因为更适合。

有很多的技术术语和像素填充率有关的，我们这里不深入，但我们可以用形象的例子：多边形精灵根据精灵的形状来描绘而不是根据最大的宽和高来描绘。这节省了很多不必要的描绘。看看下面的例子：

![spx](http://www.cocos2d-x.org/docs/programmers-guide/3-img/polygonsprite.png)

注意左右2个不同版本之间的不同点：

左边，一个普通的精灵由2个三角形描绘

右边，一个多边形精灵由许多更小的三角形描绘

更多的三角形意味着更少的像素，因此需要更少的描绘。

AutoPolygon（自动多边形）

自动多边形是一个助手类。目的是在运行时把图片变成2d多边形网格。在运行的过程中，从跟踪所有的点，测绘三角形。这样我们可以用精灵对象的create函数来创建一个多边形精灵：

{% highlight c++ %}

// Generate polygon info automatically.
auto pinfo = AutoPolygon::generatePolygon("filename.png");

// Create a sprite with polygon info.
auto sprite = Sprite::create(pinfo);

{% endhighlight %}

# Chapter 4: Actions

动作对象就像他的名字一样，他会让节点改变自身的属性达到动态的效果。动作对象允许节点在一段时间里改变自身的属性。任何对象只要是继承```Node```对象都可以使用```Action```来执行动作。举个例子，你可以在你所指定的时间段内将一个精灵从一个位置移动到另一个位置。

```MoveTo```和```MoveBy```例子：

{% highlight c++ %}

//将精灵在2秒内移动到50，10
auto moveTo = MoveTo::create(2,Vec2(50,10));
mySprite1->runAction(moveTo);

//将精灵向右移20
auto moveBy = MoveBy::create(2,Vec2(20,0));
mySprite2->runAction(moveBy);

{% endhighlight %}

### By and To, what is the difference?(By和To的不同点)

你会注意```Action```有By和To2个版本。为什么？因为他们的实现方式是不一样的。By是和节点现在的状态有关的。而To的动作是绝对的，这意味着To不需要获取节点现在的状态。看看下面的例子：

{% highlight c++ %}

auto mySprite = Sprite::create("mysprite.png");
mySprite->setPosition(Vec2(200,256));

//MoveBy 让精灵沿着x轴方向在2秒内移动300
//MoveBy x= 200+300 = 500，精灵的位置在500，256
auto moveBy = MoveBy::create(2,Vec2(300,mySprite->getPositonY()));

//MoveTo 让精灵在2秒内移动到300,256
//精灵的位置就在300，256
auto moveTo = MoveTo::create(2,Vec2(300,mySprite->getPositionY()));

//Delay 创造一个小延时
auto delay = DelayTime::create(1);

auto seq = Sequence::create(moveBy,delay,moveTo,nullptr);

mySprite->runAction(seq);

{% endhighlight %}

![actionx](http://www.cocos2d-x.org/docs/programmers-guide/4-img/i0.png)

### Basic Actions and how to run them

基础动作通常是一次单独的动作，因此它只完成一个目标。让我们看一些例子：

#### Move

在一段时间内移动一个节点：

{% highlight c++ %}

auto mySprite = Sprite::create("mysprite.png");

//在2秒内移动一个精灵到指定的位置
auto moveTo = MoveTo::create(2,Vec2(50,0));

mySprite->runAction(moveTo);

//在2秒内移动一个精灵到自身的右边50，上方0
auto moveBy = MoveBy::create(2,Vec2(50,0));

mySprite->runAction(moveBy);

{% endhighlight %}

![actionx](http://www.cocos2d-x.org/docs/programmers-guide/4-img/i1.png)

### Rotate

在2秒内顺时针旋转一个节点。

{% highlight c++ %}

auto mySprite = Sprite::create("mysprite.png");

//在2秒内顺时针旋转到指定的角度
auto rotateTo  = RotateTo::create(2.0f,40.0f);
mySprite->runAction(rotateTo);

//2秒内在原来的基础上顺时针旋转40度
auto rotateBy = RotateBy::create(2.0f,40.0f);
mySprite->runAction(rotateBy);

{% endhighlight %}

![actionx](http://www.cocos2d-x.org/docs/programmers-guide/4-img/i3.png)

### Scale

在2秒内按比例放大3倍。

{% highlight c++ %}

auto mySprite = Sprite::create("mysprite.png");

//在2秒内按比例同时放大现在大小的x轴，y轴3倍
auto scaleBy = ScaleBy::create(2.0f,3.0f);

//在2秒内放大到现在大小的x轴3倍，y轴3倍
auto scaleBy = ScaleBy::create(2.0f,3.0f,3.0f);
mySprite->runAction(scaleBy);

//在2秒内放大到原来大小的3倍
auto scaleTo = ScaleTo::create(2.0f,3.0f);
mySprite->runAction(scaleTo);

//在2秒内放大到原来大小的x轴3倍，y轴3倍
auto scaleTo = ScaleTo::create(2.0f,3.0f,3.0f);
mySprite->runAction(scaleTo);

{% endhighlight %}

![actionx](http://www.cocos2d-x.org/docs/programmers-guide/4-img/i4.png)

### Fade In/Out

渐入淡出一个节点。

通过改变透明度从0-255的变化，实现渐入。将这个变化反过来，就是淡出。

{% highlight c++ %}

auto mySprite = Sprite::create("mysprite.png");

//在1秒内渐入
auto fadeIn = FadeIn::create(1.0f);
mySprite->runAction(fadeIn);

//在2秒内淡出
auto fadeOut = FadeOut::create(2.0f);
mySprite->runAction(fadeOut);

{% endhighlight %}

![actionx](http://www.cocos2d-x.org/docs/programmers-guide/4-img/i2.png)

### Tint (染色)

对一个节点染色就是通过```NodeRGB```协议将节点在指定的时间内从现在的颜色染成指定的颜色。

{% highlight c++ %}

auto mySprite = Sprite::create("mysprite.png");

//在2秒内通过指定的RGB值对节点进行染色
auto tintTo = TintTo::create(2.0f,120.0f,232.0f,254.0f);
mySprite->runAction(tintTo);

//2秒内在原有的RGB值上变化指定的RGB值
auto tintBy = TintBy::create(2.0f,120.0f,232.0f,254.0f);
mySprite->runAction(tintBy);

{% endhighlight %}

![actionx](http://www.cocos2d-x.org/docs/programmers-guide/4-img/i5.png)

### Animate(动画)

使用```Animate```可以让你的精灵实现简单的帧播放的动画效果。只要简单间断的替换掉当前的显示帧到动画显示的下一帧，就可以实现。看看下面的例子：

{% highlight c++ %}

auto mySprite = Sprite::create("mysprite.png");

//现在创建精灵动画帧
Vector<SpriteFrame*> animateFrames;
animateFrames.reserve(12);
animateFrames.pushBack(SpriteFrame::create("Blue_Front1.png",Rect(0,0,65,81)));
animateFrames.pushBack(SpriteFrame::create("Blue_Front2.png",Rect(0,0,65,81)));
animateFrames.pushBack(SpriteFrame::create("Blue_Front3.png",Rect(0,0,65,81)));
animateFrames.pushBack(SpriteFrame::create("Blue_Left1.png",Rect(0,0,65,81)));
animateFrames.pushBack(SpriteFrame::create("Blue_Left2.png",Rect(0,0,65,81)));
animateFrames.pushBack(SpriteFrame::create("Blue_Left3.png",Rect(0,0,65,81)));
animateFrames.pushBack(SpriteFrame::create("Blue_Back1.png",Rect(0,0,65,81)));
animateFrames.pushBack(SpriteFrame::create("Blue_Back2.png",Rect(0,0,65,81)));
animateFrames.pushBack(SpriteFrame::create("Blue_Back3.png",Rect(0,0,65,81)));
animateFrames.pushBack(SpriteFrame::create("Blue_Right1.png",Rect(0,0,65,81)));
animateFrames.pushBack(SpriteFrame::create("Blue_Right2.png",Rect(0,0,65,81)));
animateFrames.pushBack(SpriteFrame::create("Blue_Right3.png",Rect(0,0,65,81)));

//用动画帧创建动画
Animation* animation = Animation::createWithSpriteFrames(animateFrames,0.1f);
Animate* animate = Animate::create(animation);

//运行动作和不断循环播放
mySprite->runAction(RepeatForever::create(animate));

{% endhighlight %}

### Easing

```Easing```是一个指定了加速度的动画效果，能使动画看起来更平滑。我们不用担心速度的问题，因为ease动作的起始时间和结束时间都是一样的。Ease动作一种模仿物理动作的很好方法。也许你想添加一些类似物理效果的动作，但是又不想所有的动作都附上复杂的物理效果，那么这是一个很好的选择。一个很好的例子就是在菜单和按钮上的使用。

下图是一些常用的easing函数的图形：

![actionx](http://www.cocos2d-x.org/docs/programmers-guide/4-img/easing-functions.png)

cocos2d-x支持上图大部分的easing函数。他们非常容易实现。让我们看看使用的实例：丢一个精灵对象到屏幕顶端，然后让他反弹。

{% highlight c++ %}

//create a sprite
auto mySprite = Sprite::create("mysprite.png");

// create a MoveBy Action to where we want the sprite to drop from.
auto move = MoveBy::create(2, Vec2(200, dirs->getVisibleSize().height -
 newSprite2->getContentSize().height));
auto move_ease_in_back = move->reverse();

// create a BounceIn Ease Action
auto move_ease_in = EaseBounceIn::create(move->clone() );

// create a delay that is run in between sequence events
auto delay = DelayTime::create(0.25f);

// create the sequence of actions, in the order we want to run them
auto seq1 = Sequence::create(move_ease_in, delay, move_ease_in_back,
    delay->clone(), nullptr);

// run the sequence and repeat forever.
mySprite->runAction(RepeatForever::create(seq1));

{% endhighlight %}

#### Sequences and how to run them (顺序和如何使用顺序)

顺序是一系列的动作以顺序的形式运行。顺序可以包含任何数目的Action，Function甚至是顺序。cocos2dx有一个```CallFunc```对象允许你创建一个FUNCTION()然后将他放到顺序里执行。这允许你往顺序对象中加入你自己设定的函数，这意味着你不仅仅能执行内置的Action。下图是一个顺序运行的时候的大概情况：

![actionx](http://www.cocos2d-x.org/docs/programmers-guide/4-img/sequence.png)

一个例子：

{% highlight c++ %}

auto mySprite = Sprite::create("mysprite.png");

// create a few actions.
auto jump = JumpBy::create(0.5, Vec2(0, 0), 100, 1);

auto rotate = RotateTo::create(2.0f, 10);

// create a few callbacks
auto callbackJump = CallFunc::create([](){
    log("Jumped!");
});

auto callbackRotate = CallFunc::create([](){
    log("Rotated!");
});

// create a sequence with the actions and callbacks
auto seq = Sequence::create(jump, callbackJump, rotate, callbackRotate, nullptr);

// run it
mySprite->runAction(seq);

{% endhighlight %}

这个顺序动作会作什么？他会以下面的顺序运行动作：Jump -> callbackJump() -> Rotate -> callbackRotate()。

### Spawn

Spawn和顺序非常相似，不同的一点是它是同时运行所有的动作。你可以包含任意的动作对象，甚至是Spawn对象。

![actionx](http://www.cocos2d-x.org/docs/programmers-guide/4-img/spawn.png)

Spawn和连续多个的```runAction()```语句的效果一样。然而，spawn的好处是你可以将spawn放到Sequence中来帮助你达到你想要的效果。结合```Spawn```和```Sequence```是非常有力的。

例子,给定：
{% highlight c++ %}

//create 2 action and run Spawn on a Sprite
auto mySprite = Sprite::create("mysprite.png");

auto moveBy = MoveBy::create(10,Vec2(400,100));
auto fadeTo = FadeTo::create(2.0f,120.0f);

{% endhighlight %}

使用Spawn：

{% highlight c++ %}

//running the above Actions with Spawn
auto mySpawn = Spawn::createWithTwoActions(moveBy,fadeTo);
mySprite->runAction(mySpawn);

{% endhighlight %}

使用连续的```runAction()```语句：

{% highlight c++ %}

//running the above Actions with consecutive runAction() statements.
mySprite->runActionn(moveBy);
mySprite->runAction(fadeTo);

{% endhighlight %}

这2种方法有一样的效果。然而，我们能将Spawn放入Sequence中。下面的流程图展示了他是怎么运作的：

![actionx](http://www.cocos2d-x.org/docs/programmers-guide/4-img/spawn_in_a_sequence.png)

{% highlight c++ %}

// create a Sprite
auto mySprite = Sprite::create("mysprite.png");

// create a few Actions
auto moveBy = MoveBy::create(10, Vec2(400,100));
auto fadeTo = FadeTo::create(2.0f, 120.0f);
auto scaleBy = ScaleBy::create(2.0f, 3.0f);

// create a Spawn to use
auto mySpawn = Spawn::createWithTwoActions(scaleBy, fadeTo);

// tie everything together in a sequence
auto seq = Sequence::create(moveBy, mySpawn, moveBy, nullptr);

// run it
mySprite->runAction(seq);

{% endhighlight %}

### Clone(克隆)

如果你有一个动作，你可以使用clone让这个动作应用到各个节点上。你为什么必须要去克隆？这是一个好问题。动作对象有内部状态。当他们运行的时候，他们实际上在改变节点的属性。如果不使用```clone()```，你不能真正的把唯一的动作应用到节点上。这会带来不可预料的结果，因为你不确定动作现在正在改变的是什么属性。

让我们举个例子，你现在有一个heroSprite，他的位置是（0，0），如果你运行一个动作：

{% highlight c++ %}

MoveBy::create(10,Vec2(400,100));

{% endhighlight %}

这会使heroSprite在10秒内从（0，0）移动到（400，100）。heroSprite现在有一个新的位置（400，100），更重要的是Action把这个位置存到了他的内部状态中，现在你有一个emenySprite在（200，200）。然后你对这个emenySprite同样使用这个动作，它会定位到（800，200）而不是你预料中的位置。你知道为什么吗？因为Action已经有一个内部位置。克隆一个Action来避免这一点。这会确保你拥有一个确定唯一的动作版本应用到你的节点上。（实际测试是只有后一个节点能应用这个动作，前一个节点没有运行动作）

{% highlight c++ %}

// create our Sprites
auto heroSprite = Sprite::create("herosprite.png");
auto enemySprite = Sprite::create("enemysprite.png");

// create an Action
auto moveBy = MoveBy::create(10, Vec2(400,100));

// run it on our hero
heroSprite->runAction(moveBy);

// run it on our enemy
enemySprite->runAction(moveBy); // oops, this will not be unique!
// uses the Actions current internal state as a starting point.

{% endhighlight %}

{% highlight c++ %}

// create our Sprites
auto heroSprite = Sprite::create("herosprite.png");
auto enemySprite = Sprite::create("enemysprite.png");

// create an Action
auto moveBy = MoveBy::create(10, Vec2(400,100));

// run it on our hero
heroSprite->runAction(moveBy);

// run it on our enemy
enemySprite->runAction(moveBy->clone()); // correct! This will be unique


{% endhighlight %}

### Reverse

Reverse就是如果你有一系列的动作，你可以使用```reverse()```去运行，它会按相反的顺序运行。也可以让一个单独简单动作反向运行。使用```reverse()```实际上是控制原来的Sequence和Spawn的属性反转。

使用一个Spawn作为一个简单的例子：

{% highlight c++ %}

//reverse a sequence , spawn or action
mySprite->runAction(mySpawn->reverse());

{% endhighlight %}

大部分的动作和顺序对象都是可以反转的：

{% highlight c++ %}

//create a sprite
auto mySprite = Sprite::create("mysprite.png");
mySprite->setPosition(50,56);

// create a few Actions
auto moveBy = MoveBy::create(2.0f, Vec2(500,0));
auto scaleBy = ScaleBy::create(2.0f, 2.0f);
auto delay = DelayTime::create(2.0f);

// create a sequence
auto delaySequence = Sequence::create(delay, delay->clone(), delay->clone(),
delay->clone(), nullptr);

auto sequence = Sequence::create(moveBy, delay, scaleBy, delaySequence, nullptr);

// run it
newSprite2->runAction(sequence);

// reverse it
newSprite2->runAction(sequence->reverse());

{% endhighlight %}

上面的代码会发生什么？如果我们把每一步都记录下来，以一个表的形式来看，应该很有帮助：

{% highlight c++ %}

mySprite is created
mySprite position is set to (50, 56)
sequence starts to run
sequence moves mySprite by 500, over 2 seconds, mySprite new position (550, 56)
sequence delays for 2 seconds
sequence scales mySprite by 2x over 2 seconds
sequence delays for 8 more seconds (notice we run another sequence to accomplish this)
we run a reverse() on the sequence so we re-run each action backwards
sequence is delayed for 8 seconds
sequence scales mySprite by -2x over 2 seconds
sequence delays for 2 seconds
sequence moves mySprite by -500, over 2 seconds, mySprite new position (50, 56)

{% endhighlight %}

你可以看见```reverse()```使用起来非常的方便，但它的内部逻辑其实很不简单。这些cocos2dx已经帮你解决。

# Chapter 5: Building and Transitioning Scenes

### 什么是场景

一个场景是一个容器，这个容易里有精灵，标签，节点和其他你的游戏需要的对象。一个场景负责运行游戏逻辑和渲染每一帧的内容。你的游戏需要最少一个场景。你可以把游戏看做一场电影。观众实时看到正在发生的事情就是运行中的场景。你可以有任意个场景对象并对他们进行流畅的过渡。cocos2d-x提供了scene transitions，你可以使用很酷的过渡效果。

### 创建一个场景

{% highlight c++ %}

auto myScene = Scene::create();

{% endhighlight %}

### Remember the Scene Graph?

在第二章的引导中，我们学习过scene graph（场景图），我们知道我们游戏中的场景是按什么顺序绘制的。场景图决定了GUI元素的绘制顺序。场景图的构成又和z-order有关。

### A Simple Scene

让我们创建一个简单的```Scene```，记住cocos2d-x实用右手坐标系。这意味着我们(0,0)在左下角。当你开始设置你游戏元素位置的时候，你应该从左下角开始计算。让我们创建一个简单的场景并在里面加一些元素。

{% highlight c++ %}

auto dirs = Director::getInstance();
Size visibleSize = dirs->getVisibleSize();

auto myScene = Scene::create();

auto label1 = Label::createWithTTF("My Game","Marker Felt.ttf",36);
label1->setPosition(Vec2(visibleSize.width/2,visibleSize.height/2));

myScene->addChild(label1);

auto sprite1 = Sprite::create("mysprite.png");
sprite1->setPosition(Vec2(100,100));

myScene->addChild(sprite1);

{% endhighlight %}

当我们运行这段代码的时候，我们应该看到一个简单的场景里有一个标签和一个精灵。

### Transitioning between Scenes

你可能需要在Scene对象之间切换。也许开始新的游戏，改变关卡甚至结束游戏，cocos2d-x提供许多方法去完成场景过渡。

#### Ways to transition between Scenes

有许多的方法去帮助你过渡场景。每一个都有指定的功能。

{% highlight c++ %}

auto myScene = Scene::create();

{% endhighlight %}

runWithScene()只用在第一个场景。这是让你开始你游戏里的第一个场景。

{% highlight c++ %}

Director::getInstance()->runWithScene(myScene);

{% endhighlight %}

replaceScene()-完全替换掉当前的场景

{% highlight c++ %}

Director::getInstance()->replaceScene(myScene);

{% endhighlight %}

pushScene()暂停当前运行的场景，把它放入暂停场景的堆栈中，只有当前运行的场景能调用。

{% highlight c++ %}

Director::getInstance()->pushScene(myScene);

{% endhighlight %}

popScene()场景会替换掉当前的场景。当前场景会被删除，只有当前活动的场景能调用。

#### Transition Scenes with effects

你可以添加视觉效果到你的游戏过渡。

{% highlight c++ %}

auto myScene = Scene::create();

// Transition Fade
Director::getInstance()->replaceScene(TransitionFade::create(0.5, myScene, Color3B(0,255,255)));

// FlipX
Director::getInstance()->replaceScene(TransitionFlipX::create(2, myScene));

// Transition Slide In
Director::getInstance()->replaceScene(TransitionSlideInT::create(1, myScene) );

{% endhighlight %}

# Chapter 6: UI

看看平常你所使用的app，我敢肯定你会看见UI挂件。不仅仅是游戏，任何应用都会有一些挂件。UI代表了什么？UI有什么用处？非常多的问题。

### Widgets, oh, my!

UI是一个缩写，它的全称是user interface。你知道，它们是在屏幕上的。这些对象包括：labels（标签），buttons（按钮），menus（菜单），sliders（滑块）和views（？）。Cocos2d-x提供了一套UI挂件让我们很容易地在我们的项目中使用UI。这看起来好像微不足道，但创建了一个核心类例如label。你能想象必须写你自己的挂件集吗？别担心，你的需求我们能满足。

### Label

cocos2d-x提供了一个```Label```对象，可以使用```true type```，```bitmap```或者系统字体。这个类能处理你对Label的所有需要。

#### Label BMFont

```BMFont```是一种使用bitmap的标签类型。bitmap字体的特点是点矩阵形成的。非常快和方便去使用，但不可扩展的，因为它需要对每个大小字需要单独的字体。每个字在标签里都是一个独立的精灵。这意味着每个字符都能被旋转，放大，染色和有不同的锚点或者其他一些属性的改变。

创建一个BMFont标签需要2个文件，1个.fnt文件和一张拥有每个.png格式字符的图片。如果你是用工具类似Glyph Designer，这些文件会自动创建。下面是用bitmap font创建一个Label对象：

{% highlight c++ %}

auto myLabel = Label::createWithBMFont("bitmapRed.fnt","Your Text");

{% endhighlight %}

![uix](http://www.cocos2d-x.org/docs/programmers-guide/6-img/LabelBMFont.png)

参数里的所有字符都必须再.fnt文件中存在，否则他们不会被渲染。如果你渲染一个标签对象，请确保所有的字符都能在.fnt文件中找到。

#### Label TTF

True Type Fonts和bitmap fonts不一样。true type fonts是渲染字体的轮廓的。这就是说你不需要为每一个你要用到的字体的各个大小和颜色准备一个独立的字体文件。创建一个```Label```对象，使用true type font。创建的时候你需要指定一个.ttf字体文件名，文字和字体大小。不像BMFont，TTF可以渲染不同大小的字体而不需要独立的字体文件。举个例子，使用true type font：

{% highlight c++ %}

auto myLabel = Label::createWithTTF("Your Text", "Marker Felt.ttf", 24);

{% endhighlight %}

![uix](http://www.cocos2d-x.org/docs/programmers-guide/6-img/LabelTTF.png)

尽管ttf比bitmap font灵活很多，但ttf在改变属性的时候的开销要大很多。

如果你需要多个ttf的Label对象，并且他们都有统一的属性，你可以创建一个TTFConfig去管理他们。TTFConfig对象允许你设置你的label所需要的属性。你可以把它看做食谱，并且你的label对象按照这个食谱来烹饪。

你可以为你的label创建一个TTFConfig对象：

{% highlight c++ %}

//create a TTFConfig files for labels to share
TTFConfig labelConfig;
labelConfig.fontFilePath = "myFont.ttf";
labelConfig.fontSize = 16;
labelConfig.glyphs = GlyphCollection::DYNAMIC;
labelConfig.outlineSize = 0;
labelConfig.customGlyphs = nullptr;
labelConfig.distanceFieldEnabled = false;

// create a TTF Label from the TTFConfig file.
auto myLabel = Label::createWithTTF(labelConfig, "My Label Text");

{% endhighlight %}

![uix](http://www.cocos2d-x.org/docs/programmers-guide/6-img/LabelTTFWithConfig.png)

TTFConfig也可以用来显示中文，日文，韩文。

#### Label SystemFont

系统字体是一种使用默认系统字体和字体大小的标签类型。这种字体不会改变自身属性。你可以认为系统字体，系统规则。创建一个系统字体标签：

{% highlight c++ %}

auto myLabel = Label::createWithSystemFont("My Label Text","Arial",16);

{% endhighlight %}

![uix](http://www.cocos2d-x.org/docs/programmers-guide/6-img/LabelWithSystemFont.png)

### Label Effects

当你在你的屏幕上有了一个标签后，你可能会想着让他更漂亮一些。可能它们看上去非常的平坦，幸运的，你不用为此特意去创建你自己的字体。Label对象可以使用效果应用到上面。不是所有的标签对象都支持应用效果。效果有：shadow（阴影），outline（轮廓），glow（发光）。你可以应用一个或者多个效果到你的标签上。

阴影效果：

{% highlight c++ %}

auto myLabel = Label::createWithTTF("myFont.ttf","My Label Text",16);

//shadow effect is supported by all Label types
myLabel->enableShadow();

{% endhighlight %}

![uix](http://www.cocos2d-x.org/docs/programmers-guide/6-img/LabelWithShadow.png)

使用轮廓效果：

{% highlight c++ %}

auto myLabel = Label::createWithTTF("myFont.ttf","My Label Text",16);

//outline effect is TTF only, specify the outline color desired
myLabel->enableOutline(Color4B::WHITE,1);

{% endhighlight %}

![uix](http://www.cocos2d-x.org/docs/programmers-guide/6-img/LabelWithOutline.png)

发光效果：

{% highlight c++ %}

auto myLabel = Label::createWithTTF("myFont.ttf","My Label Text",16);

//glow effect is TTF only, specify the glow color desired
myLabel->enalbleGlow(Color4B::YELLOW);

{% endhighlight %}

![uix](http://www.cocos2d-x.org/docs/programmers-guide/6-img/LabelWithGlow.png)

### Menu and Menu Items

我们应该非常熟悉菜单是什么了。我们可以看看我们所使用的app。在你的游戏里，通过使用菜单对象在各个游戏选项中跳转。菜单经常包含按钮，例如：PLAY,QUIT，SETTING,ABOUT。菜单也能包含菜单对象来组成嵌套的菜单系统。一个菜单对象是一种特殊的节点对象。你可以创建一个空的菜单对象来作为你的menu items的存放地点：

{% highlight c++ %}

auto myMenu = Menu::create();

{% endhighlight %}

像我们上面描述的PLAY,QUIT,SETTING,ABOUT，这些都是你的menu items。cocos2d-x提供了不同的方法去创建你的menu items，包括Label对象，制定图片。Menu items通常有2个可能的状态，一个正常状态和一个被选择状态。当你点击menu item的时候，一个callback就会触发。你可以想象成一个链状反应。你点击menu item然后运行你指定的代码。一个```Menu```可以有一个或者多个items。

{% highlight c++ %}

//creating a menu with a single item

//create a menu item by specifying images
auto closeItem = MenuItemImage::create("CloseNormal.png","CloseSelected.png", CC_CALLBACK_1(HelloWorld::menuCloseCallback,this));

auto menu = Menu::create(closeItem,NULL);
this->addChild(menu,1);

{% endhighlight %}

一个菜单也可以使用一个```MenuItem```的vector来创建：

{% highlight c++ %}

// creating a Menu from a Vector of items
Vector<MenuItem*> MenuItems;

auto closeItem = MenuItemImage::create("CloseNormal.png", "CloseSelected.png",
CC_CALLBACK_1(HelloWorld::menuCloseCallback, this));

MenuItems.pushBack(closeItem);

/* repeat for as many menu items as needed */

auto menu = Menu::createWithArray(MenuItems);
this->addChild(menu, 1);

{% endhighlight %}

效果大概如下：

![uix](http://www.cocos2d-x.org/docs/programmers-guide/6-img/menu.png)

#### Lambda functions as Menu callbacks

我们上面刚刚学习完你可以点击菜单项目来触发一个callback。C++11提供lambda函数，cocos2dx充分利用了这个特点。一个lambda函数是你可以写在你的源代码行间的函数。

一个简单的lambda：

{% highlight c++ %}

//create a simple Hello World lambda
auto func = [](){cout<<"Hello World"}

//now call it someplace in code
func();

{% endhighlight %}

使用lambda作为MenuItem callback：

{% highlight c++ %}

auto closeItem = MenuItemImage::create("CloseNormal.png","CloseSelected.png",[&](Ref* sender){
   //your code here 
});

{% endhighlight %}

### Buttons

我们都知道按钮是我们点击它就会在我们的游戏中触发一些事件。也许你使用按钮去切换场景，或者添加精灵对象到你的游戏中。当你点击按钮的时候，它拦截触摸事件，调用预定义的callback。按钮有normal状态和selected状态。Button的外表会基于他的状态而改变。创建一个按钮和定义它的callback非常简单：

{% highlight c++ %}

#include "ui/CocosGUI.h"

auto button = Button::create("normal_image.png", "selected_image.png", "disabled_image.png");

button->setTitleText("Button Text");

button->addTouchEventListener([&](Ref* sender, Widget::TouchEventType type){
        switch (type)
        {
                case ui::Widget::TouchEventType::BEGAN:
                        break;
                case ui::Widget::TouchEventType::ENDED:
                        std::cout << "Button 1 clicked" << std::endl;
                        break;
                default:
                        break;
        }
});

this->addChild(button);

{% endhighlight %}

你可以看到上面的例子，指定了.png图片来作为按钮的每个状态的图片。按钮的3个图片看起来像这样：

![ui1](http://www.cocos2d-x.org/docs/programmers-guide/6-img/Button_Normal.png)![ui2](http://www.cocos2d-x.org/docs/programmers-guide/6-img/Button_Press.png)![ui3](http://www.cocos2d-x.org/docs/programmers-guide/6-img/Button_Disable.png)

按钮在图片上看起来是这样的：

![uix](http://www.cocos2d-x.org/docs/programmers-guide/6-img/Button_example.png)

### CheckBox

我们经常填写checkboxes，例如工作申请和租借合同之类。你可以在游戏中使用checkboxes。你能让你的玩家在游戏中作出yes或者no的选择。你可能听说例如0或1的选择。checkbox允许使用者去作出这类选择。checkbox有5种不同的状态：normal,selected,disabled。创建一个checkbox非常简单：

{% highlight c++ %}

#include "ui/CocosGUI.h"

auto checkbox = CheckBox::create("check_box_normal.png",
                                 "check_box_normal_press.png",
                                 "check_box_active.png",
                                 "check_box_normal_disable.png",
                                 "check_box_active_disable.png");

checkbox->addTouchEventListener([&](Ref* sender, Widget::TouchEventType type){
        switch (type)
        {
                case ui::Widget::TouchEventType::BEGAN:
                        break;
                case ui::Widget::TouchEventType::ENDED:
                        std::cout << "checkbox 1 clicked" << std::endl;
                        break;
                default:
                        break;
        }
});

this->addChild(checkbox);

{% endhighlight %}

你可以看到上面指定了.png图片作为每个可能状态的图。那些图片如下：

![1](http://www.cocos2d-x.org/docs/programmers-guide/6-img/CheckBox_Normal.png)![2](http://www.cocos2d-x.org/docs/programmers-guide/6-img/CheckBox_Press.png)![3](http://www.cocos2d-x.org/docs/programmers-guide/6-img/CheckBox_Disable.png)![4](http://www.cocos2d-x.org/docs/programmers-guide/6-img/CheckBoxNode_Normal.png)![5](http://www.cocos2d-x.org/docs/programmers-guide/6-img/CheckBoxNode_Disable.png)

屏幕上看起来是这样的：

![uix](http://www.cocos2d-x.org/docs/programmers-guide/6-img/Checkbox_example.png)

### LoadingBar

你玩游戏的时候肯定遇见过游戏加载内容的时候，你要进行些许等待。游戏会给你展示进度条，填充的时候代表加载完成的进度。这就是进度条，状态条，或者说是加载条。创建一个加载条：

{% highlight c++ %}

#include "ui/CocosGUI.h"

auto loadingBar = LoadingBar::create("LoadingBarFile.png");

// set the direction of the loading bars progress
loadingBar->setDirection(LoadingBar::Direction::RIGHT);

this->addChild(loadingBar);

{% endhighlight %}

上面的例子中，我们设置了加载条填充的方向为右边。然而，你也许想设置百分比。这非常容易办到：

{% highlight c++ %}

#include "ui/CocosGUI.h"

auto loadingBar = LoadingBar::create("LoadingBarFile.png");
loadingBar->setDirection(LoadingBar::Direction::RIGHT);

// something happened, change the percentage of the loading bar
loadingBar->setPercent(25);

// more things happened, change the percentage again.
loadingBar->setPercent(35);

this->addChild(loadingBar);

{% endhighlight %}

我们看到的例子使用的png图片是这个：

![uix](http://www.cocos2d-x.org/docs/programmers-guide/6-img/LoadingBarFile.png)

在屏幕上看起来是这样的：

![uix](http://www.cocos2d-x.org/docs/programmers-guide/6-img/LoadingBar_example.png)

### Slider

有时候需要轻微的改变一些数值。也许你有一个角色，你允许玩家去调整力量来攻击敌人。Slider允许玩家移动一个指示器来设置数值。创建Slider的方法如下：

{% highlight c++ %}

#include "ui/CocosGUI.h"

auto slider = Slider::create();
slider->loadBarTexture("Slider_Back.png"); // what the slider looks like
slider->loadSlidBallTextures("SliderNode_Normal.png", "SliderNode_Press.png", "SliderNode_Disable.png");
slider->loadProgressBarTexture("Slider_PressBar.png");

slider->addTouchEventListener([&](Ref* sender, Widget::TouchEventType type){
        switch (type)
        {
                case ui::Widget::TouchEventType::BEGAN:
                        break;
                case ui::Widget::TouchEventType::ENDED:
                        std::cout << "slider moved" << std::endl;
                        break;
                default:
                        break;
        }
});

this->addChild(slider);

{% endhighlight %}

上面指定的.png图片为slider的每个状态提供了图片。

![1](http://www.cocos2d-x.org/docs/programmers-guide/6-img/Slider_Back.png)![2](http://www.cocos2d-x.org/docs/programmers-guide/6-img/Slider_PressBar.png)![3](http://www.cocos2d-x.org/docs/programmers-guide/6-img/SliderNode_Normal.png)![4](http://www.cocos2d-x.org/docs/programmers-guide/6-img/SliderNode_Press.png)![5](http://www.cocos2d-x.org/docs/programmers-guide/6-img/SliderNode_Disable.png)

在屏幕上看起来是这样的：

![uix](http://www.cocos2d-x.org/docs/programmers-guide/6-img/Slider_example.png)

### TextField

你想让你的玩家玩你的游戏的时候输入他所希望的名字来作为主要角色的名字吗？他们要在哪里输入呢？对的，textfield。TextField挂件用来输入文字的。它支持触摸事件，集中，百分比位置，百分比内容大小。创建一个TextField挂件：

{% highlight c++ %}

#include "ui/CocosGUI.h"

auto textField = TextField::create("","Arial",30);

textField->addTouchEventListener([&](Ref* sender, Widget::TouchEventType type){
                std::cout << "editing a TextField" << std::endl;
});

this->addChild(textField);

{% endhighlight %}

在上面的例子中，textfield创建和指定了一个callback。

TextField的功能很丰富，它能满足你所有的输入需求。你想让玩家输入密码？你想限制玩家输入字符的数目？TextField都能提供这些功能。让我们看看下面的例子：

{% highlight c++ %}

#include "ui/CocosGUI.h"

auto textField = TextField::create("","Arial",30);

// make this TextField password enabled
textField->setPasswordEnabled(true);

// set the maximum number of characters the user can enter for this TextField
textField->setMaxLength(10);

textField->addTouchEventListener([&](Ref* sender, Widget::TouchEventType type){
                std::cout << "editing a TextField" << std::endl;
});

this->addChild(textField);

{% endhighlight %}

在屏幕上看起来是这样的：

![uix](http://www.cocos2d-x.org/docs/programmers-guide/6-img/TextField_example.png)

当你编辑textField，键盘会出现：

![uix](http://www.cocos2d-x.org/docs/programmers-guide/6-img/TextField_example_keyboard.png)

# Chapter 7: Other Node Types

你在你的游戏中使用了精灵，标签，动作对象。除了这些在前面章节介绍到的基本类型之外，Coco2dx提供更多的节点来帮助你完成特殊的功能。也许你想做一个瓦片游戏？或者一个2d的scroller。又或者你想往你的游戏中添加粒子效果。cocos2dx提供这些对象帮你完成。

### TileMap

TileMaps是使用瓦片制作的地图。每一个瓦片都能有独立的行为。TileMaps以XML为基础的TML作为存储TileMaps的文件。TMX是设计为瓦片地图使用，但他也


{% highlight c++ %}

//reading in a tiled map
auto map = TMXTiledMap::create("TileMap.tmx");
addChild(map,0,99);//with a tag of '99'

{% endhighlight %}

瓦片地图有许多层，由z轴决定。你通过层的名字获取指定的层：

{% highlight c++ %}

//how to get a specific layer
auto map = TMXTiledMap::create("TileMap.tmx");
auto layer = map->getLayer("Layer0");
auto tile = layer->getTileAt(Vec2(1,63));

{% endhighlight %}

每一个瓦片都有独立的坐标和id，这让我们非常容易能获取到我们想要获取的瓦片。你可以通过id获取任意的瓦片：

{% highlight c++ %}

//to obtain a specific tiles id
unsigned int gid = layer->getTileGIDAt(Vec2(0,63));

{% endhighlight %}

一个瓦片地图层的例子：

![tile-map-example](http://www.cocos2d-x.org/docs/programmers-guide/7-img/tilemap1.png)
![tile-map-example-2](http://www.cocos2d-x.org/docs/programmers-guide/7-img/tilemap2.png)

### Particle System

也许你的游戏需要燃烧的火花的特效，法术释放的视觉效果，或者爆炸效果。那你要怎么制作这些这么复杂的效果呢？你能作出来吗？是的，使用粒子系统你就能轻松完成。粒子系统这个术语来源于电脑图形技术，使用大量非常细小的精灵或者图形对象来模拟某些模糊的现象，这个对于传统的渲染方式是非常难以实现的。一些真实的例子包括一些非常混乱的系统，自然现象，化学反应。下面是一些粒子特效：

![particle-effect](http://www.cocos2d-x.org/docs/programmers-guide/7-img/particle1.png)
![particle-effect2](http://www.cocos2d-x.org/docs/programmers-guide/7-img/particle3.png)

#### Tools for creating Particle Effects（略）Built-In Particle Effects (略)

使用ParticleFireworks（烟花）：

{% highlight c++ %}

auto emitter = ParticleFireworks::create();
addChild(emitter,10);

{% endhighlight %}

效果如下：

![particle-effect3](http://www.cocos2d-x.org/docs/programmers-guide/7-img/particle2.png)

但是如果你想要的例子效果不是你想要的呢？你可以手动的操纵它。让我们同样以烟火例子然后手动的操纵它的属性：

{% highlight c++ %}

auto emitter = ParticleFireworks::create();

//set the duration
emitter->setDuration(ParticleSystem::DURATION_INFINITY);

//radius mode
emitter->setEmitterMode(ParticleSystem::Mode::RADIUS);

//radius mode：100 pixels from center
emitter->setStartRadius(100);
emitter->setStartRadiusVar(0);
emitter->setEndRadius(ParticleSystem::START_RADIUS_EQUAL_TO_END_RADIUS);
emitter->setEndRadiusVar(0); //not used when start == end

addChild(emitter,10);

{% endhighlight %}

### Parallax(视差)

视差节点是一类特别的节点，是用于模仿视差卷轴的。parallax节点可以根据不同的位置让对象显示不同的位置和方向，形成特别的视差效果。举个日常生活中的例子，就像我们通过摄像机镜头看事物一样。超级马里奥是一个经典的例子。ParallaxNode可以通过Sequence四处移动或者通过鼠标，触摸，加速度，键盘来手动触发。

视差节点比一般的节点要复杂。因为他需要多个节点来触发他的功能。一个单独的parallaxnode是无法靠自身形成效果的。你需要最少2个额外的节点来完成parallaxnode。

{% highlight c++ %}

//create ParallaxNode
auto paraNode = ParallaxNode::create();

{% endhighlight %}

你需要多个节点来添加到视差节点中：

{% highlight c++ %}

// create ParallaxNode
auto paraNode = ParallaxNode::create();

// background image is moved at a ratio of 0.4x, 0.5y
paraNode->addChild(background, -1, Vec2(0.4f,0.5f), Vec2::ZERO);

// tiles are moved at a ratio of 2.2x, 1.0y
paraNode->addChild(middle_layer, 1, Vec2(2.2f,1.0f), Vec2(0,-200) );

// top image is moved at a ratio of 3.0x, 2.5y
paraNode->addChild(top layer, 2, Vec2(3.0f,2.5f), Vec2(200,800) );

{% endhighlight %}

这看起来非常熟悉不是吗？注意，每一个几点对象都以特定的z轴坐标添加，还有2个额外的Vec2类型参数，这是比例和偏移值。这些参数可以看作是相对于父节点的比例。

>资料均来源于<br/>[Chapter 2: Basic Cocos2d-x Concepts](http://www.cocos2d-x.org/docs/programmers-guide/2/index.html)<br/>[Chapter 3: Sprites](http://www.cocos2d-x.org/docs/programmers-guide/3/index.html#creating-sprites)<br/>[Chapter 4: Actions](http://www.cocos2d-x.org/docs/programmers-guide/4/index.html)<br/>[Chapter 5: Building and Transitioning Scenes](http://www.cocos2d-x.org/docs/programmers-guide/5/index.html)<br/>[Chapter 6: UI](http://www.cocos2d-x.org/docs/programmers-guide/6/index.html)<br/>[Chapter 7: Other Node Types](http://www.cocos2d-x.org/docs/programmers-guide/7/index.html)<br/>