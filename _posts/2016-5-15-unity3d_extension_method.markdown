---
layout:     post
title:      "Unity3d-C#扩展方法"
subtitle:   "C#扩展方法"
date:       2016-05-15 22:49:00
categories: Unity3d
tags:       Unity3d
author:     "SixTeen"
header-img: "img/unity3d.jpg"
published:   true
---

### 扩展方法

所谓的扩展方法，其实就是无需继承就可以改写类，让类中增加方法。直接通过实例调用的静态方法，因此即使实例是NULL也不会抛出nullreferenceexception。

>扩展方法使你能够向现有类型“添加”方法，而无需创建新的派生类型、重新编译或以其他方式修改原始类型。扩展方法是一种特殊的静态方法，但可以像扩展类型上的实例方法一样进行调用。

{% highlight c++ %}

//扩展方法的格式
public static 返回值 函数名(this TypeName class, args){
    //do somesthing
}

{% endhighlight %}


>参考资料：[扩展方法（C# 编程指南）](https://msdn.microsoft.com/zh-cn/library/bb383977.aspx)<br/>[c# 扩展方法奇思妙用](http://www.cnblogs.com/ldp615/archive/2009/08/07/1541404.html)