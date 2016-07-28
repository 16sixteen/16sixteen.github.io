---
layout:     post
title:      "node.js服务器学习笔记(1)"
subtitle:   "node.js http模块 模块化"
date:       2016-07-10 15:27:00
categories: WEB
tags:       WEB
author:     "SixTeen"
header-img: "img/contact-bg.jpg"
published:   true
---

* TOC
{:toc}

## Node.js

nodeJS就是使用javascript编写服务端，是服务端的javascript。([非常优秀的一篇Node入门](http://www.nodebeginner.org/index-zh-cn.html))

## 服务端

在我看来，服务端要做的事情很多，其中最重要的就是处理请求，与数据库交换数据。

### 处理请求

以前端的角度看，请求大致可以分成get和post请求，其他类型的请求我在写web前端的时候几乎没有用过。get和post请求实际上都是一个请求，并且都是发送到一个url。（[GET和POST的真正区别](http://www.nowamagic.net/librarys/veda/detail/1919)）服务器要做的就是处理这些请求。

## 构建服务器

### 创建一个最基础的http服务器

创建一个helloworld.js文件，输入下面的代码，在cmd窗口中,```node helloworld.js```

{% highlight javascript %}

var http = require("http");

http.createServer(function(request, response) {
  response.writeHead(200, {"Content-Type": "text/plain"});
  response.write("Hello World");
  response.end();
}).listen(8888);

{% endhighlight %}

用浏览打开```localhost:8888```就可以看到这个基础http服务器的效果了

### 分析http服务器

```var http = require("http")```从依赖库中获取http module，nodeJS自带http模块，http模块中的createServer返回一个服务器对象，调用```listen(port)```就可以让该服务器负责监听对应端口。

#### 基于事件驱动的nodeJS(EDP)

在我的理解里，事件驱动就是一个服务器对象负责监听，当遇到请求的时候，就将处理请求的工作交给负责处理工作的人，这样的好处首先是处理工作的人不需要分心2用去时时留意有没有需要服务，只需要专心做好自己的事情即可。

在```http.createServer()```中传入的函数正是负责处理工作的专业人员，而服务器对象则是专职监听的。看下面一个例子：

{% highlight javascript %}

var http = require("http");

function handleFunction(request,response){
    console.log("call function");
    response.writeHead(200,{"Content-Type":"text/plain"});
    response.write("Hello World");
    response.end();
}

http.createServer(handleFunction).listen(8888);

console.log("server build");

{% endhighlight %}

我们将匿名函数写成一个处理函数，并增加几个```console.log()```来观察效果，当我们执行```node helloworld.js```的时候，只输出了```server build```,因为没有处理工作，所以```handleFunction```没有被调用，当我们打开浏览器输入```localhost:8888```，我们可以看到控制台出现了2个```call function```(1个请求页面，1个是请求页面的icon)，证明当有工作需要处理的时候，把工作丢给专业人员处理，服务器对象继续负责监控端口的任务。

### 模块化

require就是获取模块，创建一个模块也非常简单，让我们把我们的服务器封装成一个模块。

{% highlight javascript %}

var http = require("http");


function start(){
    function handleFunction(request,response){
        console.log("call function");
        response.writeHead(200,{"Content-Type":"text/plain"});
        response.write("Hello World");
        response.end();
    }

    http.createServer(handleFunction).listen(8888);

    console.log("server build");
}

exports.start = start;

{% endhighlight %}

exports的作用就是为这个模块创建接口

{% highlight javascript %}

var server = require("./helloworld");

server.start();

{% endhighlight %}

通过require获取helloworld模块，调用start接口。通过模块化，我们可以将服务器的功能分散到各个js文件中。


> [Node入门](http://www.nodebeginner.org/index-zh-cn.html)<br/>[事件驱动](http://blog.csdn.net/huagong_adu/article/details/6526221)


    FIN 2016.7.18/17.04