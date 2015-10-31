---
layout:     post
title:      "SlovingReport(2.2)"
subtitle:   "1282"
date:       2015-10-24 10:55:00
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

这题是字符串匹配问题，用了[kmp算法](/algorithm/2015/10/24/Algorithm-String-Matching-2/)
{% highlight c++ %}
#include<iostream>
#include<string.h>
#include<stdio.h>
using namespace std;

int nextarray[1000000];
int pattern[1000000];
int target[1000000];

void setNextarray(int n){
    nextarray[0] = 0;
    int k = 0;
    for (int i = 1; i < n; i++){
        k = nextarray[i-1];
        while (pattern[i] != pattern[k] && k != 0){
            k = nextarray[k - 1];
        }
        if (pattern[i] == pattern[k]){
            nextarray[i] = k + 1;
        }
        else{
            nextarray[i] = 0;
        }
    }
}

void kmp(int m,int n){
    setNextarray(n);
    int i = 0;
    int k = 0;
    int pos = 0;
    while (i < m){
        while (pattern[pos] == target[i + k]){
            pos++;
            k++;
            if (k == n){
                cout << i << endl;
                return;
            }
        }
        if (k != 0){
            i = i + (k - nextarray[k - 1]);
            k = nextarray[k - 1];
            pos = k;
        }
        else{
            i++;
            k = 0;
            pos = 0;
        }
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
        kmp(m,n);
    }
    return 0;
}
{% endhighlight %}
