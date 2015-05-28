# Cocos2d-x 学习笔记（4.2）

标签（空格分隔）： cocos2d-x 帧动画 调度器

---

###**帧动画SpriteFrame**

通过连续播放形成动画的效果，使游戏具有很灵动的效果。不过这个极大的加重了制作的负担而且最终输出的文件量也很大。

```C#
auto s = Director::getInstance()->getWinSize();
// 创建一张贴图
auto texture = Director::getInstance()->getTextureCache()->addImage("dragon_animation.png");
// 从贴图中切割范围，以此来创建动画的关键帧
//参数：切割的纹理的x轴坐标，切割的纹理的y轴坐标，切割的纹理的宽度,切割的纹理的高度。
auto frame0 = SpriteFrame::createWithTexture(texture, Rect(132*0, 132*0, 132, 132));
auto frame1 = SpriteFrame::createWithTexture(texture, Rect(132*1, 132*0, 132, 132));
auto frame2 = SpriteFrame::createWithTexture(texture, Rect(132*2, 132*0, 132, 132));
auto frame3 = SpriteFrame::createWithTexture(texture, Rect(132*3, 132*0, 132, 132));
auto frame4 = SpriteFrame::createWithTexture(texture, Rect(132*0, 132*1, 132, 132));
auto frame5 = SpriteFrame::createWithTexture(texture, Rect(132*1, 132*1, 132, 132));
// 从第一针开始创建一个精灵
auto sprite = Sprite::createWithSpriteFrame(frame0);
sprite->setPosition( Point( s.width/2-80, s.height/2) );
addChild(sprite);
// 创建一个容器，用来存储关键帧
Vector<SpriteFrame*> animFrames(6);
animFrames.pushBack(frame0);
animFrames.pushBack(frame1);
animFrames.pushBack(frame2);
animFrames.pushBack(frame3);
animFrames.pushBack(frame4);
animFrames.pushBack(frame5);
// 通过关键怎创建一个Animation，参数：SpriteFrame类的Vector容器，每一帧之间的间隔
auto animation = Animation::createWithSpriteFrames(animFrames, 0.2f);
// 通过animation创建好的动画来创建一个Animate，Animate继承自ActionInterval，它能被当做动作特效播放
auto animate = Animate::create(animation);
// 创建一个序列动作特效，播放刚才创建的动画，当播放完毕的时候翻转精灵，再次播放一次
auto seq= Sequence::create(animate, FlipX::create(true), animate->clone(), FlipX::create(false), NULL);
// 播放序列动作特效
sprite->runAction(RepeatForever::create( seq) );
```

###**调度器Scheduler,Update**

常用的3种调度器
<font color="blue">
默认调度器:schedulerUpdate()
自定义调度器:schedule(SEL_SCHEDULE selector, float interval, unsigned int repeat, float delay)
单次调度器:scheduleOnce(SEL_SCHEDULE selector, float delay)
</font>

```C#
//默认调度器(schedulerUpdate)
//通过执行schedulerUpdate()调度器每帧执行update方法，如果需要停止这个调度器，可以使用unschedulerUpdate()方法。

//HelloWorldScene.h
void update(float dt) override;

//HelloWorldScene.cpp
bool HelloWorld::init()
{    ...
    scheduleUpdate();
    return true;
}
//重写update
void HelloWorld::update(float dt)
{
    log("update");
}
```

```C#
//自定义调度器(scheduler)，最常用的调度器
//取消该调度器可以用unschedule(SEL_SCHEDULE selector, float delay)

bool HelloWorld::init()
{
    ...
    //第一个参数selector即为你要添加的事件函数
    //第二个参数interval为事件触发时间间隔
    //第三个参数repeat为触发一次事件后还会触发的次数，默认值为kRepeatForever，表示无限触发次数
    //第四个参数delay表示第一次触发之前的延时
    schedule(schedule_selector(HelloWorld::updateCustom), 1.0f, kRepeatForever, 0);
    return true;
}
 void HelloWorld::updateCustom(float dt)
{
    log("Custom");
}
```

```C#
//单次调度器(schedulerOnce)
//用unschedule(SEL_SCHEDULE selector, float delay)来取消该触发器。

bool HelloWorld::init()
{
    ...
    scheduleOnce(schedule_selector(HelloWorld::updateOnce), 0.1f);
    return true;
}
void HelloWorld::updateOnce(float dt)
{
    log("Once");
}
```

参考文档：
1.TA的pdf
2.[调度器(scheduler)](http://cn.cocos2d-x.org/article/index?type=cocos2d-x&url=/doc/cocos-docs-master/manual/framework/native/v3/scheduler/zh.md)