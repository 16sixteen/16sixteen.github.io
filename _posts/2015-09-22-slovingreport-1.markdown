---
layout:     post
title:      "SlovingReport(1)"
subtitle:   "1020 1021 1027 1035 1046 1051 1198 1176"
date:       2015-09-28 00:41:00
categories: sloving-report
tags:       sloving-report
author:     "SixTeen"
header-img: "img/solvingReport.jpg"
---

#<a href="#01">1020</a> <a href="#02">1021</a> <a href="#03">1027</a> <a href="#04">1035</a> <a href="#05">1046</a> <a href="#06">1051</a> <a href="#07">1198</a> <a href="#08">1176</a>

---

太久没写算法题了。。。写个水题都卡。。。
几个月没有更新博客。。。假期除了给中学生辅导辅导之外，其他的时光基本都喂狗了。。之前想着给博客加画廊和加入生活随笔的构思也没有实现。。。(QAQ。)对jekyll框架不算太了解令这些想法一直没有实现的大致思路（fork一个简单主题之后又想加各种自己喜欢东西的难处啊！），改出一个侧栏遇到各种困难的时候就应该想到未来的我会遇到这些困难。。。

用了几天时间才写完这几道课堂布置的题目。。。还是要加油啊！


###<a name="01"></a>Soj-1020

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

###<a name="02"></a>Soj-1021

题意就是很多对夫妇围圈坐，如果一对夫妇坐在相邻的位置，就可以移出，问最后到底能不能全部移出。
![p](/img/slovingReport/1021.png)
其实和括号匹配是同一类题，所以使用栈模拟。如果最后每对夫妇都匹配上的话，栈应该是空的。

因为n的规模不大，完全可以用下标作为这个人的编号，里面的值代表是第几对couple。
{% highlight c++ %}
#include<stdio.h>
#include<stack>
using namespace std;
int a[200000];
int main(){
    int n, c, d, i, j;
    while (scanf("%d", &n), n != 0){
        stack<int> b;
        a[0] = 0;
        for (i = 1; i <= n; i++){
            scanf("%d%d", &c, &d);
            a[c] = i;
            a[d] = i;
        }
        for (i = 1; i <= 2 * n; i++){
            if (b.empty()){
                b.push(a[i]);
            }
            else{
                if (b.top() == a[i]){
                    b.pop();
                }
                else{
                    b.push(a[i]);
                }
            }
        }
        if (b.empty()) printf("Yes\n");
        else printf("No\n");
    }
    return 0;
}
{% endhighlight %}

###<a name="03"></a>Soj-1027

给n个人和其对应的ip地址，同一个ip地址只会出现2次，第一次出现的是本人，第二次出现的是马甲，现在要求我们找出马甲对应的本人
![p](/img/slovingReport/1027.png)
set,map的应用吧，利用set可以将本人的名字按字典序排序，从而达到题目要求的输出顺序，map用来记录人对应的ip
要注意的是getline的使用
{% highlight c++ %}
getline(cin, string, ' ');//以空格为分隔符
getline(cin, string);//默认以换行为分隔符
{% endhighlight %}


{% highlight c++ %}
#include<iostream>
#include<string>
#include<stdio.h>
#include<map>
#include<set>
using namespace std;
int main(){
    int n;
    char c;
    while (cin >> n, n != 0){
        scanf("%c", &c);//接收掉第一个换行符
        string s[50][2];
        map<string, string> id_majia;
        set<string> id;
        for (int i = 0; i < n; i++){
            getline(cin, s[i][0], ' ');
            getline(cin, s[i][1]);
            for (int j = 0; j < i; j++){
                if (s[j][1] == s[i][1]){
                    id_majia[s[j][0]] = s[i][0];
                    id.insert(s[j][0]);
                }
            }
        }
        set<string>::iterator it;
        for (it = id.begin(); it != id.end(); it++){
            cout << id_majia[*it] << " is the MaJia of " << (*it) << endl;
        }
        cout << endl;
    }
    return 0;
}
{% endhighlight %}

###<a name="04"></a>Soj-1035

给n条DNA单链，问按照碱基互补配对元(A-T,C-G),能组合出几个双螺旋结构
![p](/img/slovingReport/1035.png)
字符串的匹配可以用map来进行(A-T,C-G),这样可以少写很多的if语句，还有每一条单链只能用一次，用一个数组记录即可

{% highlight c++ %}
#include<iostream>
#include<string>
#include<map>
using namespace std;
int main(){
    int n;
    cin >> n;
    map<char, char> m;
    m['A'] = 'T';
    m['T'] = 'A';
    m['C'] = 'G';
    m['G'] = 'C';
    for (int i = 0; i < n; i++){
        int x;
        int count = 0;
        cin >> x;
        string s[120];
        int past[120];
        for (int j = 0; j < x; j++){
            cin >> s[j];
            past[j] = 0;
            for (int k = 0; k < j; k++){
                if (past[k] == 0 && (s[j].length()==s[k].length())){
                    int flag = 0;
                    for (int l = 0; l < s[j].length(); l++){
                        if (s[k][l] != m[s[j][l]]){
                            flag = 1;
                        }
                    }
                    if (flag == 0){
                        count++;
                        past[j] = 1;
                        past[k] = 1;
                        break;
                    }
                }
            }
        }
        cout << count << endl;
    }
    return 0;
}
{% endhighlight %}

###<a name="05"></a>Soj-1046

题意 给n个样例，每个样例的格式是时间段的个数，需要的方案个数，最短的区间
![p](/img/slovingReport/1046.png)
把所有符合长度的区间取出来存到一个vector里面，然后用algorithm里的sort将这些结构体排序，然后按顺序输出。个数不够的话就把容器里的全输出。

{% highlight c++ %}
#include<iostream>
#include<vector>
#include<algorithm>
using namespace std;

struct record{
    double aver;
    int begin;
    int end;
    int period;
    record(double a,int b,int e){
        aver = a;
        begin = b;
        end = e;
        period = e - b + 1;
    }
    bool operator<(const record &r)const{
        if (aver == r.aver){
            if (period == r.period){
                if (end > r.end){
                    return false;
                }
            }
            else if (period < r.period){
                return false;
            }
        }
        else if (aver < r.aver){
            return false;
        }
        return true;
    }
};


int main() {
    int n;
    cin >> n;
    for (int i = 0; i < n; i++){
        int m, p, q;
        int temp;
        int sum[10000];
        vector<record> output;
        sum[0] = 0;
        cin >> m >> p >> q;
        for (int j = 0; j < m; j++){
            cin >> temp;
            sum[j + 1] = temp + sum[j];
        }

        for (int j = q; j <= m; j++){
            for (int k = 1; k <= m; k++){
                if (k - 1 + j <= m){
                    int s = sum[k - 1 + j] - sum[k - 1];
                    double d = s / (j*1.0);
                    record newrecord(d, k, k + j - 1);
                    output.push_back(newrecord);
                }
                else{
                    break;
                }
            }
        }
        sort(output.begin(), output.end());
        cout << "Result for run "<< i+1 <<":" << endl;
        for (int j = 0; j < p&&j<output.size(); j++){
            cout << output[j].begin << "-" << output[j].end << endl;
        }
    }
    return 0;
}              
{% endhighlight %}


###<a name="06"></a>Soj-1051

输入的这几个数字的关系是第一个是直径，这段时间里转了多少次，最后一个是运动的时间

输出的是运动的距离，运动距离就是直径和转的次数之间的关系，然后速度才用到时间这个数字
![p](/img/slovingReport/1051.png)

{% highlight c++ %}
#include<iostream>
#include<stdio.h>
#define pi 3.1415927
using namespace std;

int main() {
    double d;
    double r, t;
    int count=0;
    while (cin >> d >> r >> t){
        if (r == 0){
            break;
        }
        count++;
        cout << "Trip #" << count << ": ";
        printf("%0.2lf", pi*d*r / 12 / 5280);
        printf(" %0.2lf", pi*d*r / 12 / 5280 * 3600 / t);
        cout << endl;
    }
    return 0;
}      
{% endhighlight %}

###<a name="07"></a>Soj-1198

要求把这些字符组合之后字典序最小的组合输出
![p](/img/slovingReport/1198.png)
这题的规模很小，可以用8!的暴力枚举，更简单的方法是把所有的子串按大小排好，不过比较的时候要注意substrA < subsrtB iff substrA+substrB < substrB+substrA

{% highlight c++ %}
#include<iostream>
#include<string>
#include<algorithm>
using namespace std;

bool cmp(string a, string b) {
    return a + b < b + a;
}

int main() {
    int n;
    cin >> n;
    string s[1000];
    for (int i = 0; i < n; i++){
        int x;
        cin >> x;
        for (int j = 0; j < x; j++){
            cin >> s[j];
        }
        sort(s, s + x, cmp);
        for (int j = 0; j < x; j++){
            cout << s[j];
        }
        cout << endl;
    }
    return 0;
}              
{% endhighlight %}

###<a name="08"></a>Soj-1176

题意 2个人轮流取数，第一个人可以选择取最左边的或者最右边的，而第二个人取最左边和最右边中最大的一个，我们的任务是求出第一个人最多可以赢第二个人多少分
![p](/img/slovingReport/1176.png)
这题是很明显的dp，虽然知道是用dp，但是当时太生疏。。于是在想写dp的情况下写了个贪心。。后面又傻乎乎的去看了很多dp的资料，算是重新学习了一次

这一题的话，状态就是某个起点和某个终点之间的数得出的最大分差，在第一个人选完一边之后，另一个人的选择是可以确定的，于是在一次选择之后，剩下的数的起点和终点我们是可以知道的，例如开始的起点是s，终点是e，假如第一个人选了左边num[s]，剩下的数就是(s+1,e)了，由于第二个人的选择是确定的，所以我们这次选择之后，分差肯定是num[s]-num[s+1]+best(s+2,e)(假如第二个人选了左边)，这样层层推导下去之后，当最后best(x,y)里的x和y只相差1的时候，也就是只剩下2个数的时候，怎么可以取到最优我们是显然知道的。其实，这样写，就是从所有2个数字的集合出发，一直扩展到我们最原始的数组，因此可以确定这个结果是最优的。

关于什么是dp，贪心，递推，搜索，我在知乎上看到一个非常赞的答案，摘录到我的这一个[博文](http://localhost:4000/algorithm/2015/09/23/Algorithm/)里

{% highlight c++ %}
#include<iostream>
#include<cstdlib>
using namespace std;

int dp[1020][1020];
int num[2000];

int bestvalue(int s, int e){
    if (dp[s][e] != -9999999){
        return dp[s][e];
    }
    if (e-s==1){
        if (num[s] >= num[e]){
            return num[s] - num[e];
        }
        else{
            return num[e] - num[s];
        }
    }
    else{
        int sleft;
        int sright;
        if (num[s + 1] >= num[e]){
            sleft = num[s] - num[s + 1] + bestvalue(s + 2, e);
        }
        else{
            sleft = num[s] - num[e] + bestvalue(s + 1, e - 1);
        }
        if (num[s] >= num[e - 1]){
            sright = num[e] - num[s] + bestvalue(s + 1, e - 1);
        }
        else{
            sright = num[e] - num[e - 1] + bestvalue(s, e - 2);
        }
        if (sleft >= sright){
            dp[s][e] = sleft;
            return sleft;
        }
        else{
            dp[s][e] = sright;
            return sright;
        }
    }
}

int main(){
    int n;
    int count = 0;
    while (cin >> n, n != 0){
        count++;
        for (int i = 1; i <= n; i++){
            for (int j = 1; j <= n; j++){
                dp[i][j] = -9999999;
            }
        }
        for (int i = 1; i <= n; i++){
            cin >> num[i];
        }
        cout << "In game " << count << ", the greedy strategy might lose by as many as ";
        cout << bestvalue(1,n) << " points." << endl;
    }
    return 0;
}
{% endhighlight %}