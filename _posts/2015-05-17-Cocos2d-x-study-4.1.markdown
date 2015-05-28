# Cocos2d-x 学习笔记（4.1）

标签（空格分隔）： cocos2d-x 文字 菜单 进度条 动作
---

###**文字Label**
```C#
auto newlabel1 = Label::createWithSystemFont("New Label","Arial",30);
//第一个参数是文字的内容，第二个参数是字体，第三个内容是字体大小
auto newLabel2 = Label::createWithBMFont("bitmapFontTest.fnt", "New Label");
//使用位图字体，字体的格式是fnt形式，当使用的字体不是系统自带的时候，默认根目录是Resources，创建完的label用addChild加到对应的地方即可
TTFConfig ttfConfig; 
ttfConfig.fontSize = 30;
ttfConfig.fontFilePath = "Paint Boy.ttf";
auto label2 = Label::createWithTTF(ttfConfig, "New Label");
label2->setPosition(Point(visibleSize.width / 2+ origin.x, visibleSize.height / 2+ origin.y + 100));
addChild(label2);
```
###**菜单Menu,MenuItem**
Menu继承Layout，是一个专门用来存放与用户交互的各种按钮的图层。菜单图层中主要存放着MenuItem类，经常用作游戏中用户选择界面，比如游戏开始时的主菜单或者关卡选择菜单。
```C#
	auto item1 = MenuItemLabel::create(Label::createWithSystemFont("Label1", "Cobel", 10), CC_CALLBACK_1(function, this));

	auto item2 = MenuItemLabel::create(Label::createWithTTF("Label2", "fonts/Marker Felt.ttf", 10), CC_CALLBACK_1(function, this));
	auto item3 = MenuItemLabel::create(Label::createWithTTF("Label3", "fonts/Marker Felt.ttf", 10), CC_CALLBACK_1(function, this));
    //把item都加到menu里面，最后要以NULL结尾
	auto menu = Menu::create(item1, item2, item3, NULL);
```

一些MenuItem的方法：
```C#
//MenuItemLabel
/** creates a MenuItemLabelwith a Label and a callback */
static MenuItemLabel* create(Node*label, constccMenuCallback& callback);
/** creates a MenuItemLabelwith a Label. Target and selector will be nil */
static MenuItemLabel* create(Node *label);
```
```C#
//MenuItemImage
//根据按钮状态不同的需求来创建MenuItem
static MenuItemImage* create();
static MenuItemImage* create(conststd::string& normalImage, conststd::string& selectedImage);
//第一个参数是正常状态下的样式，第二个是点击选择之后的样式，第三个是该按钮不可用的样式
static MenuItemImage* create(conststd::string& normalImage, conststd::string& selectedImage, conststd::string&disabledImage);
//带回调函数的版本
static MenuItemImage* create(conststd::string&normalImage, conststd::string&selectedImage, constccMenuCallback& callback);
static MenuItemImage* create(conststd::string&normalImage, conststd::string&selectedImage, conststd::string&disabledImage, constccMenuCallback& callback);
```
```C#
//MenuItemSprite
//用精灵才创建MenuItem
static MenuItemSprite * create(Node* normalSprite, Node* selectedSprite, Node* disabledSprite= nullptr);
static MenuItemSprite * create(Node* normalSprite, Node* selectedSprite, constccMenuCallback& callback);
static MenuItemSprite * create(Node* normalSprite, Node* selectedSprite, Node* disabledSprite, constccMenuCallback& callback);
```
###**进度条ProgressTimer**

```C#
	//progressBar
	auto progressSprite = Sprite::create("image/picture.png");
	//使用这个精灵作为进度条
	auto pT = ProgressTimer::create(progressSprite);
	//类型为条形进度条
	pT->setType(ProgressTimer::Type::BAR);
	//设置旋转点
	pT->setMidpoint(Point(0, 0));
	//设置变化样式
	pT->setBarChangeRate(Point(1, 0));
	/*
	setBarChangeRate()进度条变化样式，参数为Point(x,y)
    条形进度条：
                Point(1,0)x轴变化
                Point(0,1)y轴变化
                Point(1,1)/x,y轴都变化
    当计时条样式为X轴变化时设置setMidpoint()：
                Point(1,0)从右到左
                Point(0.5,0)中间到两边
                Point(0,0)从左到右
    当计时条样式为y轴变化时：
                Point(0,1)从上到下
                Point(0,0.5)中间到两边
                Point(0,0)从下到上
    */
	pT->runAction(RepeatForever::create(ProgressFromTo::create(5, 0, 100)));
	pT->setPosition(Point(200, 200));
	this->addChild(pT);
```

###**动作Action**
Action：动作特效。引擎内部封装了很多Action的子类，它们分别实现了各种各样的特效，如移动、旋转、跳动、缩放、闪烁等等。每一个Node都可以通过runAction(Action* action)来播放一个特效。

```C#
//常用的特效

//MoveTo/MoveBy：移动动作。继承自ActionInterval
//参数: 持续时间，移动到的坐标
auto actionTo = MoveTo::create(2, Point(80,80));
//参数: 持续时间，移动的位移
auto actionBy = MoveBy::create(2, Point(80,80));

//RotateTo/RotateBy：旋转动作。继承自ActionInterval。
//参数: 持续时间，x和y轴上旋转到的角度
auto actionTo = RotateTo::create( 2, 45);
//参数: 持续时间，x和y轴上旋转的角度
auto actionBy = RotateBy::create(2 , 360);
//创建一个由actionBy特效逆转的动作
auto actionByBack = actionBy->reverse();

//ScaleTo/ScaleBy：缩放特效。继承自ActionInterval。
//参数: 持续时间，缩放到的倍数
auto actionTo = ScaleTo::create(2.0f, 0.5f);
//参数: 持续时间，缩放的倍数
auto actionBy= ScaleBy::create(2.0f, 1.0f, 10.0f);

//SkewTo/SkewBy：倾斜动作。继承自ActionInterval。
//参数: 持续时间，x轴上倾斜到的角度，y轴上倾斜到的角度
auto actionTo = SkewTo::create(2, 37.2f, -37.2f);
//参数: 持续时间，x轴上倾斜的角度，y轴上倾斜的角度
auto actionBy = SkewBy::create(2, 0.0f, -90.0f);

//JumpTo/JumpBy：弹跳动作。继承自ActionInterval。
//参数: 持续时间，跳跃的目的坐标，跳跃的高度，跳跃的次数
auto actionTo = JumpTo::create(2, Point(300,300), 50, 4);
//参数: 持续时间，跳跃的位移，跳跃的高度，跳跃的次数
auto actionBy = JumpBy::create(2, Point(300,0), 50, 4);

//Blink：闪烁特效。继承自ActionInterval。
//参数: 持续时间，闪烁次数
auto action1 = Blink::create(2, 10);

//FadeIn/FadeOut：淡入淡出特效。继承自ActionInterval。
//参数: 淡入淡出持续时间
auto action1 = FadeIn::create(1.0f);
auto action2 = FadeOut::create(1.0f);

//TintTo/TintBy：染色特效。继承自ActionInterval。
//参数: 着色时间，红色、绿色、蓝色的着色目的值
auto action1 = TintTo::create(2, 255, 0, 255);
//参数: 着色时间，红色、绿色、蓝色的着色改变值
auto action2 = TintBy::create(2, -127, -255, -127);
```

```C#
//常用的特效组合

//Spawn：同时进行。继承自ActionInterval。
//创建一个组合特效，使这些特效同时进行，执行时间以最长的特效为准
//参数: FiniteTimeAction类的动作1，FiniteTimeAction类的动作2……… FiniteTimeAction类的动作N，NULL
auto action = Spawn::create(JumpBy::create(2, Point(300,0), 50, 4), RotateBy::create(2, 720), NULL);

//Sequence：顺序执行动作序列。继承自ActionInterval。
//创建一个组合特效，使这些特效顺序进行
//参数: 动作特效1，动作特效2………动作特效N，NULL
auto action = Sequence::create(MoveBy::create(2, Point(240,0)),RotateBy::create(2, 540), NULL);

//Repeat/RepeatForever：重复和永久重复特效。继承自ActionInterval。
auto act1 = RotateTo::create(1, 90);
auto act2 = RotateTo::create(1, 0);
auto seq= Sequence::create(act1, act2, NULL);
//参数: 需要永久重复执行的ActionInterval动作特效
auto rep1 = RepeatForever::create(seq);
//参数: 需要重复执行的ActionInterval动作特效，重复执行的次数
auto rep2 = Repeat::create( seq->clone(), 10);
```

```C#
//变速动作

//Speed：线性变速动作。继承自Action。用于线性的改变某个动作的速度。参考ActionEaseTest。
//参数: 要改变速度的ActionInterval类的目标动作，改变的速度倍率
auto action = Speed::create(RepeatForever::create(spawn), 1.0f);

//EaseExponentialIn：指数缓冲动作。继承自Action。用于指数曲线（如下图）的变换某个动作的速度。参考ActionEaseTest。
//参数: 要改变速度的ActionInterval类的目标动作
//先慢后快，凹型
auto move_ease_in = EaseExponentialIn::create(move->clone());
//先快后慢，凸型
auto move_ease_out = EaseExponentialOut::create(move->clone());
//如名IN OUT
auto move_ease = EaseExponentialInOut::create(move->clone() );
```


参考文档：
1.TA的pdf