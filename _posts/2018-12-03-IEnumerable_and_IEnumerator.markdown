---
layout:     post
title:      "IEnumerable，IEnumerator以及yield return的本质"
subtitle:   "集合,迭代器,yield return"
date:       2018-12-04 22:18:00
categories: IEnumerable
tags:       IEnumerable IEnumerator
author:     "SixTeen"
---

* TOC
{:toc}

### IEnumerable 和 IEnumerator

IEnumerable（可枚举的）描述的是一个集合。我们知道接口描述的是一个东西can do。所以，IEnumerable能做的是他可以迭代（枚举）。因此IEnumerable需要实现一个`public IEnumerator GetEnumerator()`，作用是获取一个迭代器，由迭代器提供遍历的功能。

IEnumerator描述的是一个迭代器。IEnumerator需要我们实现的接口`public object Current{get;};bool MoveNext();void Reset();`，迭代器是一个每一次调用返回一个集合中的值的浮标。他的三个方法也是对应了他的这个职责（can do），current就是当前指向的集合元素，movenext就是指向下一个元素，reset就是重置迭代器指向的元素（第一个元素的前一个元素）。

### 使用yield return来生成迭代器

当一个迭代器方法运行到yield return后，方法执行到的位置会被记下来，下次重新执行的时候会从该位置重启。

让我们来看一下迭代器方法在CIL中的构造：

{% highlight C# lineons %}
public IEnumerator GetEnumerator()
{
    for(int i = 0; i < m_values.Length; i++)
    {
        yield return m_values[i];
    }
}
{% endhighlight %}

```
.method public hidebysig newslot virtual final 
        instance class [mscorlib]System.Collections.IEnumerator 
        GetEnumerator() cil managed
{
  .custom instance void [mscorlib]System.Runtime.CompilerServices.IteratorStateMachineAttribute::.ctor(class [mscorlib]System.Type) = ( 01 00 2E 43 6F 6E 73 6F 6C 65 41 70 70 31 2E 50   // ...ConsoleApp1.P                 
                            72 6F 67 72 61 6D 2B 53 61 6D 70 6C 65 2B 3C 47   // rogram+Sample+<G
                            65 74 45 6E 75 6D 65 72 61 74 6F 72 3E 64 5F 5F   // etEnumerator>d__
                            33 00 00 )                                        // 3..
  // 代码大小       14 (0xe)
  .maxstack  8
  IL_0000:  ldc.i4.0
  IL_0001:  newobj     instance void ConsoleApp1.Program/Sample/'<GetEnumerator>d__3'::.ctor(int32)
  IL_0006:  dup        //Duplicate the value on the top of the stack.
  IL_0007:  ldarg.0	   //this入栈
  IL_0008:  stfld      class ConsoleApp1.Program/Sample ConsoleApp1.Program/Sample/'<GetEnumerator>d__3'::'<>4__this'
  IL_000d:  ret
} // end of method Sample::GetEnumerator
```

特性标记的是该方法是一个迭代器方法，为方法添加一个iterator的修饰符。同时，会为这个迭代方法实现一个实现了`IEnumerator`的内部类（`d_3`）,这个类里有一个迭代器所实现的`Current`，`MoveNext`，`Reset`这些方法。

可以看到CIL代码生成的`GetEnumerator`每次都生成一个新的内部迭代器，因此我们多次调用`GetEnumerator`返回的都是从头开始的新迭代器。

最后附上一个例子：

{% highlight C# lineons %}
static void Main(string[] args)
{
    Sample s = new Sample(new int[]{1, 2, 3});
    IEnumerator i1 = s.GetEnumerator();
    IEnumerator i2 = s.GetEnumerator();
    IEnumerator i3 = s.GetEnumerator();

    i1.MoveNext();
    i2.MoveNext();
    i3.MoveNext();

    foreach(object i in s)
    {

    }


    Console.ReadLine();
}

//输出
/*
GetEnumeator
1
GetEnumeator
1
GetEnumeator
1
2
3
*/

public class Sample : IEnumerable
{
    int[] m_values;

    public Sample(int[] values)
    {
        m_values = values;
    }

    public IEnumerator GetEnumerator()
    {
        Console.WriteLine("GetEnumeator");
        for(int i = 0; i < m_values.Length; i++)
        {
            Console.WriteLine(m_values[i]);
            yield return m_values[i];
        }
    }
}
{% endhighlight %}

CIL将迭代器方法的代码移到了迭代器的MoveNext中进行执行，所以代码会在第一次执行MoveNext一路执行到第一个yield return。

>参考资料：<br>[C# IEnumerator的使用](https://www.cnblogs.com/w-wfy/p/7418459.html)

>FIN 2018.12.04/22.17