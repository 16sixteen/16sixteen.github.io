---
layout:     post
title:      "www安卓上的问题和调试"
subtitle:   "Unity3d学习 脚本使用gui在画面输出调试信息"
date:       2016-07-16 13:48:00
categories: Unity3d
tags:       Unity3d
author:     "SixTeen"
header-img: "img/unity3d.jpg"
published:   true
---

* TOC
{:toc}

## 安卓真机上的调试

在安卓真机上调试往往可能会出现在pc上没有出现的错误，例如我在pc上使用www类的时候，是支持所有协议的，但是在安卓上的话有一些协议就不支持，而且有时候在改变脚本代码后，脚本的public属性在场景中可能还是原来的值，必须要reset才能更新为脚本中的值，而在导出之前可能没有发现，最后在安卓上调试的时候又出现问题。因此学习如何能在安卓上使用GUI输出错误信息有一定的使用价值。

## 脚本

这个脚本是有一天在烦恼如何在安卓上输出log信息的时候在百度知道找到的一个答案，详细的地址找不到了，在这里贴上代码并分析。

{% highlight c# %}

using UnityEngine;
using System.Collections;
using System.Collections.Generic;

public class DeBUG : MonoBehaviour {
    
    //在启动的时候注册事件
    //logCallback就是每次log之后的回调函数
    internal void OnEnable() {
        Application.RegisterLogCallback(HandleLog);
    }
    //在失效的时候取消注册事件
    internal void OnDisable() {
        Application.RegisterLogCallback(null);
    }

    private string m_logs;

    /// <summary>
    /// 
    /// </summary>
    /// <param name="logString">错误信息</param>
    /// <param name="stackTrace">跟踪堆栈</param>
    /// <param name="type">错误类型</param>
    void HandleLog(string logString, string stackTrace, LogType type) {
        m_logs += logString + "\n";
    }

    public bool Log;
    private Vector2 m_scroll;
    internal void OnGUI() {
        if (!Log)
            return;
        m_scroll = GUILayout.BeginScrollView(m_scroll);
        GUILayout.Label(m_logs);
        GUILayout.EndScrollView();
    }
}

{% endhighlight %}


    FIN 2016.7.24/14.28