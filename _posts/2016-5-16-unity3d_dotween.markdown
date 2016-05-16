---
layout:     post
title:      "Unity3d-仿写简单dotween"
subtitle:   "C#扩展方法 协程 单例工厂"
date:       2016-05-16 22:16:00
categories: Unity3d
tags:       Unity3d
author:     "SixTeen"
header-img: "img/unity3d.jpg"
published:   true
---

[dotween官网](http://dotween.demigiant.com/getstarted.php)

先验知识：[Unity3d-Coroutines协程](/unity3d/unity3d_Coroutines)<br/>[Unity3d-C#扩展方法](/unity3d/unity3d_extension_method)

### dotween

在我的理解，dotween是一个扩展方法库，帮我们扩展了transform的方法，使得我们能够通过如```transform.DoMove(vec3,time)```这样简单的函数调用，实现在time时间内移动到vec3位置的动画效果。这次的仿写简单dotween本质上是使用了扩展方法和协程来实现。

这次实现的功能是dotween对象的管理以及使用dotween管理pause，play和kill对应动作，实现回调。dotween和动作对应的协程是一一对应的关系。

![uml](/img/unity3d/Dotween/uml.png)

### dotween对象

dotween对象对应的是对应动作的协程。通过改变成员变量中的isPause和autoKill可以决定对应的协程是否停止，dotween对象是否在执行完之后自动销毁。在扩展方法中，每次调用transform的扩展方法，就会产生一个对应的dotween，和一个协程。dotween在构造的时候会自动添加到Dotween工厂的列表中管理。

{% highlight c++ %}

public class dotween{
    public string name;
    public Coroutine coroutine = null;
    public Transform transform = null;
    public Vector3 target;
    public float time;
    public bool isPause = false;
    public bool autoKill = true;
    public delegate void OnComplete();
    public OnComplete onComplete = null;
    public dotween(string n, Transform trans,Vector3 tar, float t, Coroutine co) {
        name = n;
        transform = trans;
        target = tar;
        time = t;
        //添加到工厂中管理
        Dotween.getInstance().Add(this);
    }
    public void setCoroutine(Coroutine co) {
        coroutine = co;
    }
    public void Kill() {
        MonoBehaviour mono = transform.GetComponent<MonoBehaviour>();
        mono.StopCoroutine(coroutine);
        //生命周期结束，从工厂中删除
        Dotween.getInstance().Remove(this);
    }
    public void Play() {
        isPause = false;
    }
    public void Pause() {
        isPause = true;
    }
    //设置回调函数，可以设置多个回调函数
    public void setOnComplete(OnComplete fun) {
        onComplete += fun;
    }
    public void runOnComplete() {
        if (onComplete != null) {
            onComplete();
        }
        if (autoKill) {
            Kill();
        }
    }
    //设置执行完动作后是否自动销毁
    public void setAutoKill(bool b) {
        autoKill = b;
    }
}

{% endhighlight %}

### Dotween工厂

Dotween工厂的作用是管理所有的dotween，将他们保存在列表中，可以通过工厂让同一名字的动作一起暂停，播放，销毁。又或者让一个对象的所有动作停止，播放，销毁。

{% highlight c++ %}

public class Dotween{
    private static List<dotween> dotList = null;
    private static Dotween dot = null;
    public static Dotween getInstance() {
        if(dot == null) {
            dot = new Dotween();
            dotList = new List<dotween>();
        }
        return dot;
    }
    //添加一个dotween对象到列表中
    public void Add(dotween d) {
        dotList.Add(d);
    }
    //从列表中删除一个dotween对象
    public void Remove(dotween d) {
        dotList.Remove(d);
    }
    //暂停
    public static void PauseAll() {
        for(int i = dotList.Count - 1; i >= 0; i--) {
            dotList[i].Pause();
        }
    }
    public static void Pause(string name) {
        for (int i = dotList.Count - 1; i >= 0; i--) {
            if(dotList[i].name == name) {
                dotList[i].Pause();
            }
        }
    }
    public static void Pause(Transform transform) {
        for (int i = dotList.Count - 1; i >= 0; i--) {
            if (dotList[i].transform == transform) {
                dotList[i].Pause();
            }
        }
    }
    //播放
    public static void PlayAll() {
        foreach (dotween d in dotList) {
            d.Play();
        }
    }
    public static void Play(string name) {
        foreach(dotween d in dotList) {
            if(d.name == name) {
                d.Play();
            }
        }
    }
    public static void Play(Transform transform) {
        foreach(dotween d in dotList) {
            if(d.transform == transform) {
                d.Play();
            }
        }
    }
    //销毁
    public static void KillAll() {
        for (int i = dotList.Count - 1; i >= 0; i--) {
            dotList[i].Kill();
        }
    }
    public static void Kill(string name) {
        for (int i = dotList.Count - 1; i >= 0; i--) {
            if (dotList[i].name == name) {
                dotList[i].Kill();
            }
        }
    }
    public static void Kill(Transform transform) {
        for (int i = dotList.Count - 1; i >= 0; i--) {
            if (dotList[i].transform == transform) {
                dotList[i].Pause();
            }
        }
    }
}

{% endhighlight %}

### 扩展方法

有了dotween和Dotween来管理我们的动作后，我们可以编写对应的扩展函数库了。

{% highlight c# %}

public static class extension_method {
    //DoMove扩展方法(MonoBehaviour,Transform)
    //通过time秒移动到指定的位置target
    public static IEnumerator DoMove(this MonoBehaviour mono, dotween dot) {
        //根据目标和现在的位置，按时间长度算出速度
        Vector3 distance = (dot.target - dot.transform.position)/dot.time;
        for (float f = dot.time; f >= 0.0f; f -= Time.deltaTime) {
            dot.transform.Translate(distance * Time.deltaTime);
            yield return null;
            //根据dotween确认状态
            while (dot.isPause == true) {
                yield return null;
            }
        }
        //动作结束调用回调函数
        dot.runOnComplete();
    }

    public static dotween DoMove(this Transform transform,Vector3 target,float time) {
        MonoBehaviour mono = transform.GetComponents<MonoBehaviour>()[0];
        //新建dotween
        dotween dot = new dotween("DoMove",transform, target, time,null);
        //创建协程
        Coroutine coroutine = mono.StartCoroutine(mono.DoMove(dot));
        //给dotween设置对应协程
        dot.setCoroutine(coroutine);
        return dot;
    }
}

{% endhighlight %}

这就成功扩展了一个DoMove函数，要扩展更多的函数，只要记得同样的步骤（创建dotween对象，创建协程，给dotween设置协程，在协程中确认状态，调用回调函数）就可以写出更多的扩展方法。例如扩展一个DoScale：

{% highlight c# %}

    //DoScale扩展方法(MonoBehaviour,Transform)
    //通过time秒放大到特定倍数target
    public static IEnumerator DoScale(this MonoBehaviour mono, dotween dot) {
        Vector3 distance = (dot.target - dot.transform.localScale)/dot.time;
        for (float f = dot.time; f >= 0.0f; f -= Time.deltaTime) {
            dot.transform.localScale = dot.transform.localScale + distance * Time.deltaTime;
            yield return null;
            while (dot.isPause == true) {
                yield return null;
            }
        }
        dot.runOnComplete();
    }
    public static dotween DoScale(this Transform transform, Vector3 target, float time) {
        MonoBehaviour mono = transform.GetComponents<MonoBehaviour>()[0];
        dotween dot = new dotween("DoScale", transform, target, time, null);
        Coroutine coroutine = mono.StartCoroutine(mono.DoScale(dot));
        dot.setCoroutine(coroutine);
        return dot;
    }

{% endhighlight %}

### 使用

完成了扩展之后，我们就可以在我们的脚本中调用对应的扩展函数，轻松实现运动效果。

{% highlight c# %}

public class test : MonoBehaviour {
    dotween dot;
    // Use this for initialization
    void Start () {
        dot = transform.DoMove(new Vector3(1, 1, 1), 10.0f);
        transform.DoScale(new Vector3(2, 2, 2), 10.0f);
        transform.DoPause(5.0f);
        dot.setOnComplete(fun);
    }
    
    void fun() {
        Debug.Log("onComplete");
    }
    // Update is called once per frame
    void Update () {}
}

{% endhighlight %}

### 效果：

![result](/img/unity3d/Dotween/result.gif)

    FIN 5.16/22.50