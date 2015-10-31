---
layout:     post
title:      "SlovingReport(2.1)"
subtitle:   "1282"
date:       2015-10-19 22:07:00
categories: sloving-report
tags:       sloving-report
author:     "SixTeen"
header-img: "img/solvingReport.jpg"
published:   true
---

#<a href="#01">1282</a>

---






###<a name="01"></a>Soj-1282

![p](/img/slovingReport/1282.png)

这题是字符串匹配问题，用了[Horspool算法](/algorithm/2015/10/19/Algorithm-String-Matching/)
{% highlight c++ %}
#include<iostream>
#include<stdio.h>
#include<string.h>
using namespace std;

int table[256];//char 8 bits
int pattern[1000000];//模式串
int target[1000000];//目标串

void shifttable(int n){
    //important
    for (int i = 0; i < 256; i++){
        table[i] = n;
    }
    for (int i = 0; i < n-1; i++){
        table[pattern[i]] = n - i - 1;
    }
}

//String Matching --Horspool Algorithm
void Horspool(int m,int n){
    shifttable(n);
    int i = n - 1;
    int k = n - 1;
    while (i < m){
        int pos = i;
        while (target[pos] == pattern[k]){
            if (k == 0){
                cout << pos << endl;
                return;
            }
            pos--;
            k--;
        }
        i += table[target[i]];
        k = n - 1;
    }
    cout << "no solution" << endl;
}

int main(){
    int n;
    int m;
    while (scanf("%d", &n) != EOF){
        for (int i = 0; i < n; i++){
            cin >> pattern[i];
        }
        cin >> m;
        for (int i = 0; i < m; i++){
            cin >> target[i];
        }
        Horspool(m,n);
    }
    return 0;
}
{% endhighlight %}
