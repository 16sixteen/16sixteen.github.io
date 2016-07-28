---
layout:     post
title:      "node.js服务器学习笔记(2)"
subtitle:   "node.js 路由选择 url模块"
date:       2016-07-19 11:17:00
categories: WEB
tags:       WEB
author:     "SixTeen"
header-img: "img/contact-bg.jpg"
published:   true
---

* TOC
{:toc}


## 路由选择

我们的服务器需要能对不同的url作出响应，我们可以在监听的函数里面做选择，也可以建立一个特定的模块来完成这个工作。

## 路由选择模块

路由选择是对不同的url执行不同的处理函数，我们要对url进行选择，因此引入url模块，用于获取url路径。这里我们先构建一个简单的route模块来输出不同的路径，之后再针对不同的路径进行不同的处理。

### url

对于这样一个url```http://localhost:8888/start?foo=bar&hello=world```，有```url.parse(request.url).pathname ===> "/start"```,```url.parse(request.url).query ===> "foo=bar&hello=world"```,还有一个querystring的模块后面会用到，其作用是将url中的参数以键值的形式保存起来，例如：```querystring(request.url)["foo"] ===> "bar"```。

### 代码

{% highlight javascript %}

//route.js
function route(pathname){
    console.log(pathname);
}

exports.route = route;

{% endhighlight %}

{% highlight javascript %}

var http = require("http");
var url = require("url");

function start(route){
    function handleFunction(request,response){
        console.log("call function");

        var path = url.parse(request.url).pathname;
        route.route(path);

        response.writeHead(200,{"Content-Type":"text/plain"});
        response.write("Hello World");
        response.end();
    }

    http.createServer(handleFunction).listen(8888);

    console.log("server build");
}

exports.start = start;

{% endhighlight %}

{% highlight javascript %}

var server = require("./helloworld");
var route = require("./route")

server.start(route);

{% endhighlight %}

效果:
![](/img/unity3d/daylearning/7.19/result1.png)

### 让路由选择模块执行不同的处理函数

这里我们只是把请求的路径丢给了route来处理，route的作用是根据不同的路径，执行不同的处理函数，这里我们使用javascript中的json形式的键-值对应的数据结构来完成这个需求。因此，我们新建一个模块，专门用于编写处理函数。

{% highlight javascript %}

//requestHandlers.js
function start(){
    console.log("start function");
}

function foo(){
    console.log("foo");
}

function error(){
    console.log("url error");
}

exports.start = start;
exports.foo = foo;
exports.error = error;

{% endhighlight %}

这个处理模块有3个接口，分布是start，foo，error.

{% highlight javascript %}

/*
 * start(): "/","/start"
 * foo() :"/foo"
 * error:"/error_url"
 */

var server = require("./helloworld");
var route = require("./route")
var requestHandler = require("./requestHandlers");

var handler = {};
handler["/"] = requestHandler.start;
handler["/start"] = requestHandler.start;
handler["/foo"] = requestHandler.foo;
handler["/error"] = requestHandler.error;

server.start(route,handler);

{% endhighlight %}

{% highlight javascript %}

//route.js
function route(pathname,handler){
    if(typeof handler[pathname] == 'function'){
        handler[pathname]();
    }else{
        handler["/error"]();
    }
}

exports.route = route;

{% endhighlight %}

![](/img/unity3d/daylearning/7.19/result2.png)

    FIN 2016.7.28/13.56