---
layout:     post
title:      "Cocos2d-x 学习笔记（5.1）"
subtitle:   "cocos2d-x 容器 简单的本地数据存取"
date:       2015-05-30 16:18:00
categories: cocos2d-x
tags:       cocos2d-x
author:     "SixTeen"
header-img: "img/cocos2dx.png"
---

# Cocos2d-x 学习笔记（5.1）

标签（空格分隔）： cocos2d-x 容器 简单的本地数据存取
---

## **容器**

容器就是我们存放数据的容器。适当的使用容器能给我们带来很多的方便。

比较常用的容器有vector和map。这2个容器和c++stl里的容器用法基本上是一样的。

### **Vector**

cocos2dx里的vector<T>的模板T必须是<font color = "red">继承自cocos2dx::Ref的指针类型</font>

**Vector的一些常用操作**

{%highlight c++%}
//在栈上申请Vector：
cocos2d::Vector<Sprite*> container;
//在数组的最后插入一个对象指针
container.pushback(sprite);
// 在数组位置1插入一个对象指针
container.insert(1,sprite);
// 判断对象是否在容器内 返回bool值
bool isHere = container.contains(sprite);
//获取位置为1的对象指针
Sprite* pSprite = container.at(1);
//简单的遍历，使用c++11 的auto语法
for(auto sp : container)
{
//do something
}
//利用迭代器遍历，这和c++的语法是一样的
cocos2d:Vector<Sprite*>::iterator it = container.begin();
for(;it != container.end();)
{
    //(*it)->function();
}
cocos2d:Vector<Sprite*>::iterator it = container.begin();
for(;it != container.end();){
    if (sprite_1==(*it)){
        //erase()执行后会返回指向下一个元素的迭代器
        it = container.erase(it);
    }
    else{
        it++;
        //do something
    }
}
{%endhighlight%}

### **Map**

cocos2dx里的Map<K,V>的模板V必须是<font color = "red">继承自cocos2dx::Ref的指针类型</font>

**Map的一些常用操作**

{%highlight c++%}
//在栈上申请Map：
cocos2d::Map<std::string,Sprite*> map;
//插入键值对
map.insert("monster",sprite);
//返回map中key映射的元素的值
map.at("monster");
{%endhighlight%}

## **本地数据存取**

### **UserDefault**

适合用于基础数据类型的存取，数据将以xml的文件格式存储

使用UserDefault的时候，存取的路径是在CCUserDefault.cpp中的
{%highlight c++%}
void UserDefault::initXMLFilePath()
{
    if (! _isFilePathInitialized)
    {
        //设定路径，这里设置为D盘根目录，保存的文件名字是Score.xml
        _filePath += "D:\Score.xml";
        _isFilePathInitialized = true;
    }
}
{%endhighlight%}
{%highlight c++%}
//检测xml文件是否存在
    if (!UserDefault::getInstance()->getBoolForKey("isExist"))
    {
        UserDefault::getInstance()->setBoolForKey("isExist", true);
    }
    //获取键值为value的数据
    int value =UserDefault::getInstance()->getIntegerForKey("value");
    //将score写入
    UserDefault::getInstance()->setIntegerForKey("value", score);
    //其他的一些获取方法
    string value=UserDefault::getInstance()->getStringForKey("str");
{%endhighlight%}

