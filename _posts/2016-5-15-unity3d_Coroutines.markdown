---
layout:     post
title:      "Unity3d-Coroutines协程"
subtitle:   "Coroutines 协程"
date:       2016-05-15 17:49:00
categories: Unity3d
tags:       Unity3d
author:     "SixTeen"
header-img: "img/unity3d.jpg"
published:   true
---

### 协程

一般我们调用函数的时候，这个函数就会在一帧的时间内完成。如果我们希望我们的函数不在一帧里完全完成，每次只执行一部分，这时候就要用到协程。协程通过yield，可以在执行的途中退出，下次再次执行的时候在yield的下一句执行。协程通过保存一个栈记录执行的顺序，就像操作系统中的保存上下文一样。


协程的格式：返回值是IEnumerator和拥有yield返回语句。

{% highlight c++ %}

IEnumerator Fade() {
    for (float f = 1f; f >= 0; f -= 0.1f) {
        Color c = renderer.material.color;
        c.a = f;
        renderer.material.color = c;
        yield return null;
    }
}

//run the coroutine
void Update() {
    if (Input.GetKeyDown("f")) {
        StartCoroutine("Fade");
    }
}


{% endhighlight %}

WaitForSeconds允许在t秒以后再恢复

{% highlight c++ %}

using UnityEngine;
using System.Collections;

public class WaitForSecondsExample : MonoBehaviour {
    
    void Start() {
        StartCoroutine(Example());
    }
    
    IEnumerator Example() {
        print(Time.time);
        yield return new WaitForSeconds(5);
        print(Time.time);
    }
    
}

{% endhighlight %}


>参考资料：[Unity Manual/Scripting/Scripting Overview/Coroutines](http://docs.unity3d.com/Manual/Coroutines.html)

    FIN 5.15/19.49