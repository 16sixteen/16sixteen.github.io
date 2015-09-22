---
layout:     post
title:      "SlovingReport(1)"
subtitle:   "1020"
date:       2015-09-22 21:51:00
categories: sloving-report
tags:       sloving-report
author:     "SixTeen"
header-img: "img/solvingReport.jpg"
---

#1020 

---

太久没写算法题了。。。写个水题都卡。。。
几个月没有更新博客。。。假期除了给小朋友辅导辅导之外，其他的时光基本都喂狗了。。之前想着给博客加画廊和加入生活随笔的构思也没有实现。。。(QAQ。)对jekyll框架不算太了解令这些想法一直没有实现的大致思路（fork一个简单主题之后又想加各种自己喜欢东西的难处啊！），改出一个侧栏遇到各种困难的时候就应该想到未来的我会遇到这些困难。。。



###Soj-1020 sloving report
题意：输入一串质数，以及一个极大的数字(长度小于400),将这个数字分别对这串质数取模的结果以(r1,r2...rn)的形式输出
![p](/img/slovingReport/1020.png)
数据结构：栈，数组
我使用的方法是使用栈来存这个大数字，然后用像笔算那样的方法一位位的除下去
{% highlight c++ %}
#include<iostream>
#include<string>
#include<stack>
using namespace std;

int main(){
    int t;
    int m;
    string s;
    stack<int> x;
    stack<int> y;
    int big;
    int a[200];
    int b[200];
    cin >> t;
    for (int i = 0; i < t; i++) {
        cin >> m;
        for (int j = 0; j < m; j++) {
            cin >> a[j];
        }
        cin >> s;
        for (int j = s.length() - 1; j >= 0; j--) {
            x.push((s[j] - '0'));
        }
        for (int j = 0; j < m; j++){
            y = x;
            big = 0;
            while (!y.empty()){
                big = big * 10 + y.top();
                y.pop();
                big = big%a[j];
            }
            b[j] = big;
        }
        cout << "(";
        for (int j = 0; j < m-1; j++){
            cout << b[j];
            cout << ",";
        }
        cout << b[m - 1] << ")" << endl;
        x = y;
    }
    return 0;
}
{% endhighlight %}