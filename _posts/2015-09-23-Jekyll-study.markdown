---
layout:     post
title:      "Jekyll-study"
subtitle:   "本地配置环境 本地预览"
date:       2015-09-24 14:08:00
categories: Jekyll-study
tags:       Jekyll-study
author:     "SixTeen"
header-img: "img/solvingReport.jpg"
published:  true
---

# jekyll本地配置环境 githubpage本地预览

---
直到今天以前。。一直都没有配置jekyll的本地环境。。一直是用git上去再在线看效果。。一个组件一点点的样式可以git10-20次。。。问题就是如果自己出了点小问题，git上去之后，还不知道到底现在刷新出来的这个页面是不是已经改过的。。毕竟git上去之后，能不能立马响应也是看rp的。。有时候可能还要1-2分钟的响应时间。。
这是之前一直线上调试的commit次数.....
![contribution](/img/jekyllstudy/contribution.png)

要在本地搭建Jekyll的本地测试环境，首先要安装ruby和devkit，这2个的安装包都可以在[这里下载](http://rubyinstaller.org/downloads/),(可能需要使用vpn来翻墙)。
![ruby](/img/jekyllstudy/ruby.png)![devkit](/img/jekyllstudy/devkit.png)

##安装步骤
1.安装ruby的时候，有一个多选，全选上即可

2.安装好ruby之后，把devkit解压到一个文件夹中(文件路径最好不要有中文和空格)，在该文件夹中打开cmd，输入
{% highlight c %}
ruby dk.rb init;
ruby dk.rb install;
{% endhighlight %}
3.安装jekyll(这一步也需要翻墙，不翻墙的话会报错找不到jekyll的repository)
{% highlight c %}
gem install jekyll
{% endhighlight %}
安装成功后可以输入
{% highlight c %}
jekyll -version
{% endhighlight %}
出现版本信息就表示安装成功了

4.现在我们可以在本地预览我们的github page了
在io的目录下，输入jekyll serve --watch就可以在本地http://localhost:4000/搭建起jekyll项目了
![servewatch](/img/jekyllstudy/servewatch.png)




参考文档：

1.[百度文库里的一篇在windows上搭建Jekyll本地测试环境](http://wenku.baidu.com/link?url=8JPw8Tz_XCW5rEU7WtYdlbZLGpmPHxHxv4jXny4p5D372jj6x-hHC6kRkFMYsrC-uxsrmy130b9S7V65aq6N7jbM7EVj2X8kJtGZ7iZIreK)

2.[在Windows中配置Jekyll](http://fangge-sun.blog.163.com/blog/static/4895625720142315473777/)
