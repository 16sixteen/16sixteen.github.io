---
layout:     post
title:      "字符串匹配-Horspool"
subtitle:   "Horspool算法"
date:       2015-10-18 21:51:00
categories: Algorithm
tags:       Algorithm
author:     "SixTeen"
header-img: "img/solvingReport.jpg"
---

# <a href="#001">Horspool</a>

---

## <a name = "001"></a>Horspool算法

Horspool算法是将Boyer-Moore算法的坏字符匹配改良的一种算法。Boyer-Moore算法是将匹配过程中失效的字符当做坏字符，而Horspool则是将最末尾的当做坏字符。Horspool算法非常容易理解。

BM算法中的坏字符:
![BM的坏字符](/img/algorithm/string_matching_1.png)

Horspool算法中的坏字符:
![Horspool的坏字符](/img/algorithm/string_matching_2.png)

Horspool算法和BM算法一样，匹配失效之后，从坏字符开始，向左寻找有没有和坏字符一样的字符，如果有，就将该字符对齐坏字符，从新的末尾开始匹配，如果没有，则将pattern右移到坏字符的下一位。
![移动](/img/algorithm/string_matching_3.png)

为了实现pattern的移动，我们需要对pattern进行预处理，处理的方法是记录每一个字符在模式串中距离最右边的距离

以ABABCBA为例子:
![转移表](/img/algorithm/string_matching_4.png)
首先pattern中没有的字符都赋予字符串的长度的值,其次，记录出现过的字符在模式串距离最右边一个字符的距离。

最后附上自己实现的一个Horspool
{% highlight c++ %}
#include<iostream>
#include<stdio.h>
#include<string.h>
using namespace std;

int table[256];//char 8 bits
char pattern[1000000];//模式串
char target[1000000];//目标串

void shifttable(){
    //important
    int n = strlen(pattern);
    for (int i = 0; i < 256; i++){
        table[i] = n;
    }
    for (int i = 0; i < n - 1; i++){
        table[pattern[i]] = n - i - 1;
    }
}

//String Matching --Horspool Algorithm
void Boyer_Moore(){
    shifttable();
    int n = strlen(pattern);
    int m = strlen(target);
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
    while (cin >> target){
        cin >> pattern;
        Boyer_Moore();
    }
    return 0;
}
{% endhighlight %}

随手测试输入输出:
![转移表](/img/algorithm/string_matching_5.png)

[sicily1282使用Horspool算法](/sloving-report/slovingreport-2_1/)

如果target的长度为n,pattern的长度为m，那么Horspool算法最坏情况下的时间复杂度是O(mn)，但平均情况下它的时间复杂度是O(n)。


参考文档：


1.[字符串模式匹配算法——BM、Horspool、Sunday、KMP、KR、AC算法一网打尽](http://dsqiu.iteye.com/blog/1700312)

2.[Horspool算法的C++实现](http://blog.csdn.net/seafoodge/article/details/8219165)

