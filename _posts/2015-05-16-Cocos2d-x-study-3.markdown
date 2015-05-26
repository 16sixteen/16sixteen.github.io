---
layout:     post
title:      "Cocos2d-x 学习笔记（3）"
subtitle:   "cocos2d-x scene sprite layout 场景切换"
date:       2015-05-16 23:26:00
author:     "SixTeen"
header-img: "img/post-bg-06.jpg"
---

# Cocos2d-x 学习笔记（3）

标签（空格分隔）： cocos2d-x scene sprite layout 场景切换 
---

###**scene，sprite和layout的关系**
在Cocos2d-x-3.x引擎中，采用节点树形结构来管理游戏对象

首先整个游戏只能有一个导演，导演可以控制场景的切换
```
//场景切换
    //方法一
    auto scene = HelloWorld::createScene();               //创建一个新场景，因为原场景已删除
    Director::getInstance()->replaceScene(scene);
    //方法二
    Director::getInstance()->popScene();        //原场景依然存在，直接出栈
```
以导演为父节点，存在着很多的scene，场景下又有很多的层，层里又有很多的精灵，这样，一个游戏的基本框架已经有了，精灵是作为一个游戏里的一个对象，元素的存在，通常是运动的，层则是存放这一些精灵的地方，我们可以把不同种类的精灵放到不同的层上，而场景，则是精灵们运动的范围。场景，是cocos2dx中必不可少的元素，游戏中通常我们需要构建不同的场景（至少一个），游戏里关卡、版块的切换也就是一个一个场景的切换，就像在电影中变换舞台和场地一样。场景的一个重要的作用就是流程控制的作用，我们可以通过Director的一系列方法控制游戏中不同的场景的自由切换。
###**下面是Director控制场景的常用方法：**
<font color = "blue">
runWithScene( Scene *scene ) 启动游戏，并运行scene场景。本方法在主程序第一次启动主场景的时候调用。如果已有正在运行的场景则不能调用该方法；会调用pushScene-->startAnimation。
pushScene( Scene *scene ) 将当前运行中的场景暂停并压入到代码执行场景栈中，再将传入的scene设置为当前运行场景，只有存在正在运行的场景时才调用该方法；
replaceScene( Scene *scene ) 直接使用传入的scene替换当前场景来切换画面，当前场景被释放。这是切换场景时最常用的方法。
popScene() 释放当前场景，再从代码执行场景中弹出栈顶的场景，并将其设置为当前运行场景。如果栈为空，直接结束应用。和PushScene结对使用
end() 释放和终止执行场景，同时退出应用
pause() 暂停当前运行场景中的所有计时器和动作，场景仍然会显示在屏幕上
resume () 恢复当前运行场景的所有计时器和动作，场景仍然会显示在屏幕上</font>

###**层（Layer）**

Layer是处理玩家事件响应的Node子类。与场景不同，层通常包含的是直接在屏幕上呈现的内容，并且可以接受用户的输入事件，包括触摸，加速度计和键盘输入等。我们需要在层中加入精灵，文本标签或者其他游戏元素，并设置游戏元素的属性，比如位置，方向和大小；设置游戏元素的动作等。通常，层中的对象功能类似，耦合较紧，与层中游戏内容相关的逻辑代码也编写在层中，在组织好层后，只需要把层按照顺序添加到场景中就可以显示出来了。要向场景添加层，我们可以使用addChild方法。
```
addChild( Node child ) 
addChild( Node child, int zOrder ) 
addChild( Node *child, int zOrder, int tag )
```
```
//一个例子
auto layer = LayerColor::create(Color4B(0, 128, 128, 255));
//设置层的大小
layer->setContentSize(CCSizeMake(120, 80));
layer->setPosition(Point(50, 50));
addChild(layer, 10);
```
对于场景而言，通常我们添加的节点就是层。先添加的层会被置于后添加的层之下。如果需要为它们指定先后次序，可以使用不同的zOrder值。tag是元素的标识号码，如果为子节点设置了tag值，就可以在它的父节点中利用tag值就可以找到它了。






创建一个场景的时候，create会自动调用init函数，init函数里通常是这个场景里的层，精灵的初始化，以及一些监听器的安装。
```
//HelloWorldScene.cpp
Scene* HelloWorld::createScene()
{
    //创建一个场景
    auto scene = Scene::create();
    //调用的是CREATE_FUNC（HellowWorld）
    auto layer = HelloWorld::create();
    //加到场景中
    scene->addChild(layer);
    return scene;
}
```
```
//HelloWorldScene.cpp
bool HelloWorld::init()
{
    if ( !Layer::init() )
    {
        return false;
    }
    //获得世界坐标
    Size visibleSize = Director::getInstance()->getVisibleSize();
    //获得向量
    Vec2 origin = Director::getInstance()->getVisibleOrigin();

	//add background
	Sprite* background = Sprite::create("background.jpg");
	//放置屏幕中间
	background->setPosition(visibleSize.width / 2, visibleSize.height / 2);
	//加到HelloWorld这个场景中
	addChild(background,0);

	//create a fish sprite and run animation
	m_fish = Sprite::createWithSpriteFrameName("fish13_01.png");
	//创建一个动画效果
	Animate* fishAnimate = Animate::create(AnimationCache::getInstance()->getAnimation("fishAnimation"));
	//不停的重复这个动画
	m_fish->runAction(RepeatForever::create(fishAnimate));
	m_fish->setPosition(visibleSize.width / 2, visibleSize.height / 2);
	old_vec.set(visibleSize.width / 2, visibleSize.height / 2);
	addChild(m_fish,1);

	//add touch listener
	EventListenerTouchOneByOne* listener = EventListenerTouchOneByOne::create();
	listener->setSwallowTouches(true);
	listener->onTouchBegan = CC_CALLBACK_2(HelloWorld::onTouchBegan, this);
	Director::getInstance()->getEventDispatcher()->addEventListenerWithSceneGraphPriority(listener, this);
	
	//add menuItem
	auto nextItem = MenuItemFont::create("Shoot", CC_CALLBACK_1(HelloWorld::menuCallback, this));
	nextItem->setPosition(Vec2(origin.x + visibleSize.width - nextItem->getContentSize().width / 2,origin.y + nextItem->getContentSize().height / 2));
	auto menu = Menu::create(nextItem, NULL);
	menu->setPosition(Point::ZERO);
	this->addChild(menu, 3);
    return true;
}
```







参考文档：
1.TA的pdf
2.[基础概念介绍——导演、场景、层、精灵](http://cn.cocos2d-x.org/article/index?type=cocos2d-x&url=/doc/cocos-docs-master/manual/framework/native/v3/basic-concepts/zh.md)
3.[Cocos2d-x3.2场景切换](http://my.oschina.net/Jacedy/blog/301236)


