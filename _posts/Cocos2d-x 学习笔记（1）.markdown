---
layout:     post
title:      "Cocos2d-x 学习笔记（1）"
subtitle:   "cocos2d-x 配置环境 新建项目 初始认识"
date:       2015-05-10 17:51:00
author:     "Start Bootstrap"
header-img: "img/post-bg-06.jpg"
---

# Cocos2d-x 学习笔记（1）

标签（空格分隔）： cocos2d-x 配置环境 新建项目 初始认识

---

###**配置环境**

[如何在Windows 8 Metro平台搭建Cocos2d-x Android开发环境](http://cn.cocos2d-x.org/article/index?type=cocos2d-x&url=/doc/cocos-docs-master/manual/framework/native/v2/getting-started/setting-up-development-environments/windows-8-metro-environment-setup/setup-the-cocos2d-x-for-android-in-windows-8-metro/zh.md)

如果不移植到android的话，前4步都可以省略。

###**新建项目**

使用cmd作为你的shell，然后执行类似的命令：
``` python
//在指定目录下新建一个项目
cocos.py new YourGameTitle -p com.yourcompany.gametitle -l cpp -d C:\YourGameTitle
//在当前目录下新建一个项目
cocos.py new YourGameTitle -p com.yourcompany.gametitle -l cpp
```

Classes文件夹是需要完成的程序代码
proj.win8.1-universal是项目工程文件
Resources是引用的资源

###**COCOS2DX结构初识**
首先从新建的项目里的2个文件开始了解cocos2dx：

###AppDelegate.cpp

``` C++

//游戏启动完成
bool AppDelegate::applicationDidFinishLaunching() {
    // initialize director
    //创建导演
    auto director = Director::getInstance();
    auto glview = director->getOpenGLView();
    if(!glview) {
        glview = GLViewImpl::create("My Game");
        director->setOpenGLView(glview);
    }
    // turn on display FPS
    //展示当前帧率
    director->setDisplayStats(true);

    // set FPS. the default value is 1.0/60 if you don't call this
    //设置帧率
    director->setAnimationInterval(1.0 / 60);

    // create a scene. it's an autorelease object
    //创建并设置初始场景
    auto scene = HelloWorld::createScene();

    // run
    director->runWithScene(scene);

    return true;
}
```


```C++
//应用程序进入后台
// This function will be called when the app is inactive. When comes a phone call,it's be invoked too
void AppDelegate::applicationDidEnterBackground() {
    Director::getInstance()->stopAnimation();

    // if you use SimpleAudioEngine, it must be pause
    // SimpleAudioEngine::getInstance()->pauseBackgroundMusic();
}
```


```C++
//游戏从后台恢复
// this function will be called when the app is active again
void AppDelegate::applicationWillEnterForeground() {
    Director::getInstance()->startAnimation();

    // if you use SimpleAudioEngine, it must resume here
    // SimpleAudioEngine::getInstance()->resumeBackgroundMusic();
}
```
###HelloWorldScene.h
```C++
class HelloWorld : public cocos2d::Layer
{
public:
    // there's no 'id' in cpp, so we recommend returning the class instance pointer
    //场景
    static cocos2d::Scene* createScene();

    // Here's a difference. Method 'init' in cocos2d-x returns bool, instead of returning 'id' in cocos2d-iphone
    //初始化helloworld
    virtual bool init();
    
    // a selector callback
    //退出按钮的函数
    void menuCloseCallback(cocos2d::Ref* pSender);
    
    // implement the "static create()" method manually
    //宏，创建helloworld对象，注意HelloWorld是单例的
    CREATE_FUNC(HelloWorld);
};
```

###HelloWorldScene.cpp
```C++
Scene* HelloWorld::createScene()
{
    // 'scene' is an autorelease object
    auto scene = Scene::create();
    
    // 'layer' is an autorelease object
    auto layer = HelloWorld::create();

    // add layer as a child to scene
    scene->addChild(layer);

    // return the scene
    return scene;
}
```
在这段代码中，首先利用Scene::create方法创建了一个空场景，然后利用Helloworld::create方法创建一个HelloWorld层的实例，最后调用scene对象的addChild方法来把创建的层添加到场景之中。


```C++
  //HelloWorld::init()
    auto closeItem = MenuItemImage::create(                                                                "CloseNormal.png",
                                           "CloseSelected.png",      CC_CALLBACK_1(HelloWorld::menuCloseCallback, this));
  //png来自Resource  
	closeItem->setPosition(Vec2(origin.x + visibleSize.width - closeItem->getContentSize().width/2 , origin.y + closeItem->getContentSize().height/2));

    // create menu, it's an autorelease object
    auto menu = Menu::create(closeItem, NULL);
    menu->setPosition(Vec2::ZERO);
    this->addChild(menu, 1);//第二个参数表示z轴，数字越大越靠前


    //创建一个label
    auto label = Label::createWithTTF("Hello World", "fonts/Marker Felt.ttf", 24);
    
    // position the label on the center of the screen
    label->setPosition(Vec2(origin.x + visibleSize.width/2,
                            origin.y + visibleSize.height - label->getContentSize().height));

    // add the label as a child to this layer
    this->addChild(label, 1);

    // add "HelloWorld" splash screen"
    //创建一个精灵
    auto sprite = Sprite::create("HelloWorld.png");

    // position the sprite on the center of the screen
    sprite->setPosition(Vec2(visibleSize.width/2 + origin.x, visibleSize.height/2 + origin.y));

    // add the sprite as a child to this layer
    this->addChild(sprite, 0);
    
    return true;
```





参考文档：
1.TA的pdf
2.http://cn.cocos2d-x.org/



