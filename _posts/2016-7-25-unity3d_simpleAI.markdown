---
layout:     post
title:      "Unity3d 简单状态模式实现简单AI"
subtitle:   "简单AI 简单状态模式"
date:       2016-07-25 10:17:00
categories: Unity3d
tags:       Unity3d
author:     "SixTeen"
header-img: "img/unity3d.jpg"
published:   true
---

* TOC
{:toc}

### 简单状态模式实现简单AI

使用静态整型来判断状态，update函数中判断是进行状态转换还是执行状态动作。这里是每过一段时间进行一次状态变化。写怪物的简单ai也可以这样写，当进入战斗状态之后，根据参数进行状态转换，出不同的招。下面是一个简单的例子。

{% highlight c# %}

using UnityEngine;
using System.Collections;

public class simpleAI : MonoBehaviour {
    public GameObject person_object;
    //状态类型
    private const int state_num = 3;
    private const int idle = 0;
    private const int run = 1;
    private const int rotate = 2;
    //状态
    private int state = 0;
    //每过一段时间随机更新自身状态
    public float changeTime = 3.0f;
    //上一次变化的时间
    private float lastChangeTime = 0f;
    //旋转状态下的旋转幅度
    private int roation_state = 0;
    //记录动作
    private string aniString = "idle";
    public Animation anim;

    void Start() {
        anim = person_object.GetComponent<Animation>();
        anim.wrapMode = WrapMode.Loop;
        //第一次随机更新状态
        AIChangeState(state_num);
    }


    // Update is called once per frame
    void Update() {
        if (isAIChange()) {
            AIChangeState(state_num);
        } else {
            updateState();
        }
    }
    //判断是否已经经过一段时间，状态需要发生改变
    bool isAIChange() {
        if (Time.time - lastChangeTime >= changeTime) {
            lastChangeTime = Time.time;
            return true;
        }
        return false;
    }
    //随机更新自身状态
    void AIChangeState(int count) {
        //随机状态
        int s = Random.Range(0, count);
        //Debug.Log(s);
        setState(s);
    }
    //设置状态并播放相应的动画
    void setState(int newState) {
        if (state == newState) {
            return;
        }
        state = newState;
        aniString = "idle";
        switch (state) {
            case idle:
                aniString = "idle";
                break;
            case run:
                aniString = "walk_inplace";
                break;
            case rotate:
                roation_state = Random.Range(0, 4);
                break;
        }

        if (!anim.IsPlaying(aniString)) {
            anim.Play(aniString);
        }
    }
    //根据状态更新
    void updateState() {
        switch (state) {
            case rotate:
                person_object.transform.rotation = Quaternion.Lerp(person_object.transform.rotation, Quaternion.Euler(0, roation_state * 90, 0), Time.deltaTime);
                break;
            case run:
                transform.Translate(person_object.transform.forward * 0.02f);
                break;
        }
        if (!anim.IsPlaying(aniString)) {
            anim.Play(aniString);
        }
    }
}

{% endhighlight %}

> 参考资料:<br/>[Unity3D研究院之游戏开发中的人工智能AI（三十八）](http://www.xuanyusong.com/archives/1840)

    FIN 2016.7.31/11.18