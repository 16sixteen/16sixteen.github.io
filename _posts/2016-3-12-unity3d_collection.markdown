---
layout:     post
title:      "C# Collection使用"
subtitle:   "Unity3d"
date:       2016-3-12 21:18:00
categories: Unity3d
tags:       Unity3d
author:     "SixTeen"
header-img: "img/solvingReport.jpg"
published:   true
---

集合基本都存在于System.Collections,System.Collections.Generic,System.Collections.Generic专门是负责泛型集合(xxx<T>).

## List<T>:线性表

初始化：```List<T> list = new List<T>()```

添加元素：```list.Add(t)```

添加数组：```list.AddRange(new T[]{t1,t2,t3,t4})```

索引: ```list[i]```

遍历：```foreach(T t in list){ print(t);}```

删除：```list.RemoveAt(int)```

清空：```list.clear()```

## Queue：队列

创建：```Queue<T> queue = new Queue<T>()```

入队：```queue.Enqueue(t)```

出队：```queue.Dequeue()```

遍历：```foreach(T t in queue){print(t);}```

## Stack：栈

创建：```Stack<T> stack = new Stack<T>()```

入栈：```stack.Push(t)```

出栈：```stack.Pop()```

遍历：```foreach(T t in stack){print(t);}```

## Dictionary字典（map）

创建：```SortedDictionary<string,int> dict = new SortedDictionary<string,int>()```

插入：```dict.Add(s,i)```

遍历：```foreach(KeyValuePair<s,i> t in dict){t.Key;t.Value;}```

>参考资料：<br/>[C#常用集合的使用](http://blog.csdn.net/ceclar123/article/details/8655853)

    FIN 3.18/13.38
