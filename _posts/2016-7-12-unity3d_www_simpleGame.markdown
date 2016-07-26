---
layout:     post
title:      "www制作简单的网络同步"
subtitle:   "UNET学习 WWW"
date:       2016-07-12 17:03:00
categories: Unity3d
tags:       Unity3d
author:     "SixTeen"
header-img: "img/unity3d.jpg"
published:   true
---

* TOC
{:toc}

## WWW

www是一个简单的网页访问类，可以实现get和post请求，并获取网页返回的对象。默认使用的是get请求，如果附加一个postData，那么将使用post请求。一般来说，www都放在协程中使用，因为www一般都是用于下载数据，更新数据，使用协程就能将下载放在后台中执行

### 例子：

{% highlight c# %}

using UnityEngine;
using System.Collections;
using System.Collections.Generic;
using System.Text;

//协程,get请求
IEnumerator functionn(param){
    WWW www = new WWW(url);
    yield return www;
    if(www.error != null){
        Debug.LogError(www.error);
    }
    else{
        do something...
    }
}

//协程，post请求
IEnumerator function(param){
    //这里的p应该是序列化类，通过JsonUtility进行序列化
    string postData = JsonUtility.ToJson(p);
    byte[] rawData = Encoding.UTF8.GetBytes(postData);

    WWWForm form = new WWWForm();
    //传输数据的头部
    Dictionary<string,string> headers = form.headers;
    headers["Content-Type"] = "application/json";
    headers["Accept"] = "application/json";

    WWW www = new WWW(url,rawData,headers);
    yield return www;
    if(www.error!=null){
        Debug.LogError(www.error);
    }else{
        //获取返回的json数据
        string json = www.text.ToString();
        p = JsonUtility.FormJson<p_class>();
        ...
    }
}

{% endhighlight %}

## 设计简单的同步

服务器：服务器负责存储已经登录的用户的位置和角度信息，并将用户提交的用户位置更新，并给用户发送最新的所用用户的位置和角度信息。

客户端：每一秒给服务器发送一个post请求，提交自己的用户位置和角度，并获取所有用户的位置和角度信息。


### 服务端

服务端使用nodeJS的express框架

{% highlight c# %}
var express = require('express');
var bodyParser = require('body-parser');
var app = express();
//app.use(express.bodyParser());
app.use(bodyParser.json());

//存放用户信息的json
var usermessage = {};

//用户信息的类
function user (user_data) {
    this.user_id = user_data.user_id;
    this.user_position = {
        x:user_data.user_position.x,
        y:user_data.user_position.y,
        z:user_data.user_position.z
    };
    this.user_rotation = {
        x:user_data.user_rotation.x,
        y:user_data.user_rotation.y,
        z:user_data.user_rotation.z,
        w:user_data.user_rotation.w
    };
}

//处理post请求
app.post('/',function(req,res){
    console.log("get post req");
    //将用户提交的位置
    var u = new user(req.body);
    usermessage[u.user_id] = u;
    var user_array = [];
    for(var i in usermessage){
        user_array.push(usermessage[i]);
    }

    res.send(
        {user_array:user_array}
        );
});

var server = app.listen(3333, function() {
    initUserArray();
    console.log('Listening on port %d', server.address().port);
});

{% endhighlight %}

### 客户端

{% highlight c# %}

    //服务器发送的用户信息json反序列化的基础类
    [System.Serializable]
    public class UserMsg {
        public int user_id;
        public Vector3 user_position;
        public Quaternion user_rotation;
        public UserMsg(int id, Vector3 postion, Quaternion rotation) {
            user_id = id;
            user_position = postion;
            user_rotation = rotation;
        }
    }

    [System.Serializable]
    public class PosMsg {
        public UserMsg[] user_array;
    }

{% endhighlight %}


### 平滑

因为请求的间隔是1s，因此延迟会非常的大，如果单纯将位置设置为新位置那么会出现人物每一秒瞬移一次的情况，因此使用插值进行平滑处理。

{% highlight c# %}

//插值更新位置和角度
player[userId].transform.position = Vector3.Lerp(player[userId].transform.position, playerPosition, Time.fixedDeltaTime*playerSpeed);
player[userId].transform.rotation = Quaternion.Slerp(player[userId].transform.rotation, playerRotation, Time.fixedDeltaTime * playerSpeed);

{% endhighlight %}

    FIN 2016.7.24/10.23