---
layout:     post
title:      "Unity3d-模仿AnimationEvent编写StateEvent类"
subtitle:   "AnimationEvent"
date:       2016-04-24 23:55:00
categories: Unity3d
tags:       Unity3d
author:     "SixTeen"
header-img: "img/unity3d.jpg"
published:   true
future:      enable
---


### 编写一个事件组件。

1.模仿 AnimationEvent 编写一个 StateEvents 类 

2.用户可以创建一个指定时间、指定状态触发的事件类 

3.事件可以采用反射机制，调用调用客户的方法；或使用订阅发布方法调用客户的方法。 

4.在你的动画控制程序中使用 StateEvents 类 

简而言之，我们需要实现的是一个能存储状态机动画中各个状态中的事件帧。并在对应的时间里执行对应的事件。用户使用接口只需要提供状态的名字(string)和触发的时间(float)以及调用的函数。各个类之间的对应关系：

![uml](/img/unity3d/stateEvent/uml.png)

每一个Animator对应一个状态机，对应多个状态，每一个状态机对应一个StateEvent，每一个StateEvent里面包含多个MyEvent，在这里MyEvent代表的是某个状态下某个时间点下所有的事件帧。对于StateEvent，我们给定一个特定的状态，我们可以得到一个MyEvent的List，分别对应不同时间点的所有事件。每一个MyEvent包含一个StateTrigger，用来判断是否触发这个MyEvent里的事件。MyEvent使用观察者模式实现。

### 工厂类

因为每一个animator对应一个stateEvent，我们可以使用工厂来实现，防止一个animator对应多个stateEvent。工厂类的实现如下：

{% highlight c++ %}

//每一个对象最多只有一个stateEvent，因为stateEvent是根据animator来创建的，使用工厂来实现
public class stateEventFactory : System.Object {
    public static stateEventFactory sef = null;
    //int为animator组件的hash码，对应一个stateEvent
    public Dictionary<int, StateEvent> myStateEvent = new Dictionary<int, StateEvent>();
    public static stateEventFactory getInstance() {
        if(sef == null) {
            sef = new stateEventFactory();
        }
        return sef;
    }
    public StateEvent getStateEvent(Animator ani) {
        int i = ani.GetHashCode();
        //判断字典中是否有这个键，没有则新建一个stateevent
        if (!myStateEvent.ContainsKey(i)) {
            myStateEvent[i] = new StateEvent();
            //设置一个animator，每一个stateevent对应一个animator
            myStateEvent[i].set(ani);
        }
        return myStateEvent[i];
    }
}

{% endhighlight %}

### StateTrigger

接下来是MyEvent中使用的辅助类StateTrigger，触发器是一个辅助类，他用一个float来初始化他要执行的时间，根据时间大小的比较来判断是否触发，触发后更改状态，在下一次相同状态再次初始化状态。

{% highlight c++ %}

public class StateTrigger {
    private float t;
    private bool fired;
    //设置触发的时间
    public StateTrigger(float trigger_time) {
        this.t = trigger_time;
        this.fired = false;
    }
    //根据时间判断是否触发
    public bool triggerIt(float time) {
        if ((time > this.t) & !fired) {
            //设置为已触发状态
            fired = true;
            return true;
        } else if ((time <= this.t) & fired) {
            //新的一轮触发，将状态设置为未触发
            fired = false;
            return false;
        } else {
            return false;
        }
    }
}

{% endhighlight %}

### MyEvent

然后是辅助类MyEvent，这个类的作用是用来保存同一个状态下的同一时间下的所有事件帧，因此他有一个触发时间的属性，以及一个成员函数触发器，这个触发器用来判断这个事件是否到达要执行的时间。使用观察者模式来实现调用所有事件函数。

{% highlight c++ %}

//MyEvent的作用是记录某个时间点下需要触发的所有事件,
public class MyEvent : System.Object {
    public float triggerTime;
    public StateTrigger st;
    public delegate void EventFunc();
    public event EventFunc eventFunc;
    public MyEvent(float time) {
        triggerTime = time;
        st = new StateTrigger(time);
    }
    //运行所有注册的函数（也就是要执行的事件）
    public void run() {
        eventFunc();
    }
}

{% endhighlight %}

### stateEvent

主类stateEvent，根据状态机的状态string来获取哈希值，来建立一个状态对应List<MyEvent>的字典。当要添加一个新的事件帧时，先判断这个状态下是否有事件帧，如果没有，先创建一个新的list容器，如果有，那么遍历这个容器，看看相同时间下是否已经有对应的MyEvent，如果有，那么直接使用，否则新建一个。

{% highlight c++ %}

    private Animator ani;
    //记录上一个状态
    private AnimatorStateInfo pre_state;
    //第一个int对应的是对应的状态名字的hash值，在对应的状态下，对应一个MyEvent
    private readonly Dictionary<int, List<MyEvent> > myEvent = new Dictionary<int, List<MyEvent> >();
    //记录当前状态的运行时间
    private float time = 0f;

    public MyEvent AddEvent(string name, float time) {
        int hash = Animator.StringToHash(name);
        //这一步很关键，如果这个键值不存在，要初始化一个列表，如果没有这一步，那么会出现编译错误
        if (!myEvent.ContainsKey(hash)) {
            myEvent[hash] = new List<MyEvent>();
        }
        MyEvent me = null;
        foreach (MyEvent item in myEvent[hash]) {
            if(item.triggerTime == time) {
                me = item;
            }
        }
        if (me == null) {
            me = new MyEvent(time);
            myEvent[hash].Add(me);
        }
        return me;
    }

    public void set(Animator a) {
        ani = a;
    }

{% endhighlight %}

状态更新函数：如果当前状态和久状态不同，那么调用enterstate表示进入新状态，如果相同，那么根据时间的运行状况，判断是否有事件帧需要执行。

{% highlight c++ %}

    //根据状态的变化来执行相应的函数
    public void update() {
        AnimatorStateInfo asi = ani.GetCurrentAnimatorStateInfo(0);
        if (asi.fullPathHash == pre_state.fullPathHash) {
            UpdateState(asi);
        } else {
            EnterState(asi);
            pre_state = asi;
        }
    }

    //进入新的状态，因此初始化状态的执行时间
    void EnterState(AnimatorStateInfo asi) {
        time = 0f;
    }

    void UpdateState(AnimatorStateInfo asi) {
        //更新当前状态的运行时间
        time += Time.deltaTime;
        int index = asi.fullPathHash;
        //如果当前状态存在用户添加的事件列表
        if (myEvent.ContainsKey(index)) {
            foreach (MyEvent i in myEvent[index]) {
                //根据事件触发时间来判断是否执行事件
                if (i.st.triggerIt(asi.normalizedTime)) {
                    i.run();
                }
            }
        }
    }

{% endhighlight %}

### 脚本类

最后是脚本类：将挂载该脚本的对象的anmiator对象获取工厂中对应的StateEvent对象，执行他的update函数。

{% highlight c++ %}

public class stateEvent : MonoBehaviour {
    stateEventFactory sef;
    Animator ani;
    void Start() {
        //通过工厂根据当前对象的animator组件来获取相应的stateevent
        ani = GetComponent<Animator>();
        sef = stateEventFactory.getInstance();
    }

    void Update() {
        //执行对于stateevent的更新
        sef.getStateEvent(ani).update();
    }
}

{% endhighlight %}

### 测试脚本：

{% highlight c++ %}

public class test : MonoBehaviour {
    stateEventFactory sef;
    Animator ani;
    void Start() {
        ani = GetComponent<Animator>();
        sef = stateEventFactory.getInstance();
        sef.getStateEvent(ani).AddEvent("Base Layer.idle", 0.5f).eventFunc += Hello;
        sef.getStateEvent(ani).AddEvent("Base Layer.idle", 0.5f).eventFunc += Hello2;
        sef.getStateEvent(ani).AddEvent("Base Layer.idle", 0.8f).eventFunc += Hello3;
    }

    void Update() {
    }
    void Hello() {
        print("helloworld");
    }
    void Hello2() {
        print("helloworld2");
    }
    void Hello3() {
        print("helloworld3");
    }
}

{% endhighlight %}

![uml](/img/unity3d/stateEvent/e.png)


### 完整代码

{% highlight c++ %}

//stateEvent.cs
using UnityEngine;
using System.Collections;
using System.Collections.Generic;

public class StateTrigger {
    private float t;
    private bool fired;
    //设置触发的时间
    public StateTrigger(float trigger_time) {
        this.t = trigger_time;
        this.fired = false;
    }
    //根据时间判断是否触发
    public bool triggerIt(float time) {
        if ((time > this.t) & !fired) {
            //设置为已触发状态
            fired = true;
            return true;
        } else if ((time <= this.t) & fired) {
            //新的一轮触发，将状态设置为未触发
            fired = false;
            return false;
        } else {
            return false;
        }
    }
}


//MyEvent的作用是记录某个时间点下需要触发的所有事件,
public class MyEvent : System.Object {
    public float triggerTime;
    public StateTrigger st;
    public delegate void EventFunc();
    public event EventFunc eventFunc;
    public MyEvent(float time) {
        triggerTime = time;
        st = new StateTrigger(time);
    }
    //运行所有注册的函数（也就是要执行的事件）
    public void run() {
        eventFunc();
    }
}

//每一个对象最多只有一个stateEvent，因为stateEvent是根据animator来创建的，使用工厂来实现
public class stateEventFactory : System.Object {
    public static stateEventFactory sef = null;
    //int为animator组件的hash码，对应一个stateEvent
    public Dictionary<int, StateEvent> myStateEvent = new Dictionary<int, StateEvent>();
    public static stateEventFactory getInstance() {
        if(sef == null) {
            sef = new stateEventFactory();
        }
        return sef;
    }
    public StateEvent getStateEvent(Animator ani) {
        int i = ani.GetHashCode();
        //判断字典中是否有这个键，没有则新建一个stateevent
        if (!myStateEvent.ContainsKey(i)) {
            myStateEvent[i] = new StateEvent();
            //设置一个animator，每一个stateevent对应一个animator
            myStateEvent[i].set(ani);
        }
        return myStateEvent[i];
    }
}


public class StateEvent : System.Object {

    private Animator ani;
    //记录上一个状态
    private AnimatorStateInfo pre_state;
    //第一个int对应的是对应的状态名字的hash值，在对应的状态下，对应一个MyEvent
    private readonly Dictionary<int, List<MyEvent> > myEvent = new Dictionary<int, List<MyEvent> >();
    //记录当前状态的运行时间
    private float time = 0f;

    public MyEvent AddEvent(string name, float time) {
        int hash = Animator.StringToHash(name);
        //这一步很关键，如果这个键值不存在，要初始化一个列表，如果没有这一步，那么会出现编译错误
        if (!myEvent.ContainsKey(hash)) {
            myEvent[hash] = new List<MyEvent>();
        }
        MyEvent me = null;
        foreach (MyEvent item in myEvent[hash]) {
            if(item.triggerTime == time) {
                me = item;
            }
        }
        if (me == null) {
            me = new MyEvent(time);
            myEvent[hash].Add(me);
        }
        return me;
    }

    public void set(Animator a) {
        ani = a;
    }

    //根据状态的变化来执行相应的函数
    public void update() {
        AnimatorStateInfo asi = ani.GetCurrentAnimatorStateInfo(0);
        if (asi.fullPathHash == pre_state.fullPathHash) {
            UpdateState(asi);
        } else {
            EnterState(asi);
            pre_state = asi;
        }
    }

    //进入新的状态，因此初始化状态的执行时间
    void EnterState(AnimatorStateInfo asi) {
        time = 0f;
    }

    void UpdateState(AnimatorStateInfo asi) {
        //更新当前状态的运行时间
        time += Time.deltaTime;
        int index = asi.fullPathHash;
        //如果当前状态存在用户添加的事件列表
        if (myEvent.ContainsKey(index)) {
            foreach (MyEvent i in myEvent[index]) {
                //根据事件触发时间来判断是否执行事件
                if (i.st.triggerIt(asi.normalizedTime)) {
                    i.run();
                }
            }
        }
    }
}



public class stateEvent : MonoBehaviour {
    stateEventFactory sef;
    Animator ani;
    void Start() {
        //通过工厂根据当前对象的animator组件来获取相应的stateevent
        ani = GetComponent<Animator>();
        sef = stateEventFactory.getInstance();
    }

    void Update() {
        //执行对于stateevent的更新
        sef.getStateEvent(ani).update();
    }
}


{% endhighlight %}


    FIN 4.25/1.05