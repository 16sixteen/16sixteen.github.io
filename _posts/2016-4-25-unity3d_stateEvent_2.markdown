---
layout:     post
title:      "Unity3d-使用组合模式编写StateEvent组件"
subtitle:   "组合模式"
date:       2016-04-25 23:25:00
categories: Unity3d
tags:       Unity3d
author:     "SixTeen"
header-img: "img/unity3d.jpg"
published:   true
---

[非组合模式的实现](/unity3d/unity3d_stateEvent)

### 使用组合模式现实MyComponent组件

![](/img/unity3d/stateEvent/com.png)

组合模式的优点是分级，从图里可以看出StateEvent和MyEvent是以一种递归的结构组织的，也就是组件里包含一个StateEvent（记录所有状态对应的List<MyEvent>），一个StateEvent里包含多个MyEvent(记录某个状态下某个时间点需要调用的所有函数)。使用文件系统作类比，StateEvent就是目录，MyEvent就是文件，目录和文件都实现了打开这个接口函数 ，而StateEvent和MyEvent都实现了Trigger这个接口函数，StateEvent的Trigger就是执行当前状态下所有的MyEvent的Trigger，而MyEvent的Trigger就是执行自己所存储的所有事件帧（回调函数）。因此我们只需要在MyComponent的Update中不断让StateEvent根据状态机所处的状态调用Trigger即可。


IStateEvent接口：

{% highlight c++ %}

    public interface IStateEvent {
        void trigger(int sidHash, float time);
    }

{% endhighlight %}

StateEvent类：

{% highlight c++ %}

    public class StateEvent : System.Object, IStateEvent {
        public float time;
        //int是对应状态的fullpathhash，每个状态对应一个事件帧列表
        private Dictionary<int, List<MyEvent> > dict_myEvent = new Dictionary<int, List<MyEvent> >();
        public Animator anim;
        public AnimatorStateInfo pre_state;
        public AnimatorStateInfo now_state;
        public StateEvent(Animator a) {
            time = 0f;
            anim = a;
        }
        //根据状态的全名和要设定的时间点获取MyEvent
        public MyEvent getMyEvent(string sid, float time) {
            int hash = Animator.StringToHash(sid);
            if (!dict_myEvent.ContainsKey(hash)) {
                dict_myEvent[hash] = new List<MyEvent>();
            }
            //同一个时间点只需要一个MyEvent
            foreach(MyEvent item in dict_myEvent[hash]) {
                if (item.sameTime(time)) {
                    return item;
                }
            }
            MyEvent me = new MyEvent(hash, time);
            dict_myEvent[hash].Add(me);
            return me;
        }
        public void UpdateState() {
            time += Time.deltaTime;
            now_state = anim.GetCurrentAnimatorStateInfo(0);
            if(now_state.fullPathHash == pre_state.fullPathHash) {
                trigger(now_state.fullPathHash,now_state.normalizedTime);
            } else {
                EnterState();
            }
        }
        public void EnterState() {
            pre_state = now_state;
            time = 0f;
        }
        //实现trigger接口，调用状态机当前状态下所有MyEvent的trigger
        public void trigger(int sidHash, float time) {
            if (dict_myEvent.ContainsKey(sidHash)) {
                foreach(MyEvent item in dict_myEvent[sidHash]) {
                    item.trigger(sidHash,time);
                }
            }
        }
    }


{% endhighlight %}

MyEvent类：

{% highlight c++ %}

    public class MyEvent : System.Object, IStateEvent {
        public int stateNameHash;
        public float triggerTime;
        public StateTrigger st;
        //使用委托（观察者模式）实现
        public delegate void EventFrame();
        public EventFrame eventFrame;
        public bool sameTime(float time) {
            if(time == triggerTime) {
                return true;
            } else {
                return false;
            }
        }
        public MyEvent(int hash, float time) {
            stateNameHash = hash;
            triggerTime = time;
            st = new StateTrigger(time);
        }
        //实现trigger接口，执行所有的回调函数
        public void trigger(int sidHash, float time) {
            if (st.triggerIt(time)) {
                eventFrame();
            }
        }
        //添加回调函数的接口
        public void AddEvent(EventFrame func) {
            eventFrame += func;
        }
        public void removeEvent(EventFrame func) {
            eventFrame -= func;
        }
    }


{% endhighlight %}

StateTrigger类(辅助类)：

{% highlight c++ %}

    public class StateTrigger:System.Object {
        public float time;
        public bool fire;
        public StateTrigger(float t) {
            time = t;
            fire = false;
        }
        public bool triggerIt(float t) {
            if (t > time && !fire) {
                fire = true;
                return true;
            }else if (t < time) {
                fire = false;
            }
            return false;
        }
    }

{% endhighlight %}

MyComponent脚本：之所以生成一个StateEvent的原因是，我们实现的是一个组件，这个组件的功能是存储状态机动画中各个状态中的事件帧。并在对应的时间里执行对应的事件。用户只需要获取我们这个组件中的se，并使用里面的getMyEvent(StateName,Time).Add(callBackFunc)，就可以实现这个功能。

{% highlight c++ %}

public class MyComponent : MonoBehaviour {
    public Animator ani;
    public com.MyStateEvent.StateEvent se;
    void Awake () {
        //根据ani生成一个StateEvent
        se = new com.MyStateEvent.StateEvent(ani);
    }
    void Update () {
        se.UpdateState();
    }
}

{% endhighlight %}

测试脚本：

{% highlight c++ %}

//test2.cs
using UnityEngine;
using System.Collections;

public class test2 : MonoBehaviour {

    // Use this for initialization
    void Start () {
        //获取MyComponent中的se属性
        com.MyStateEvent.StateEvent se = GetComponent<MyComponent>().se;
        se.getMyEvent("Base Layer.idle", 0.5f).AddEvent(HelloWorld);
        se.getMyEvent("Base Layer.idle", 0.3f).AddEvent(HelloWorld2);
        se.getMyEvent("Base Layer.idle", 0.5f).AddEvent(HelloWorld2);
    }
    
    // Update is called once per frame
    void Update () {
    
    }

    void HelloWorld() {
        print("HelloWorld");
    }
    void HelloWorld2() {
        print("HelloWorld2");
    }

}

{% endhighlight %}

结果：

![](/img/unity3d/stateEvent/result.png)

    FIN 4.26/00.00