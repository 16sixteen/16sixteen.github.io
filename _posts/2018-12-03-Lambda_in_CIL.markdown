---
layout:     post
title:      "C# lambda中的变量"
subtitle:   "lambda表达式在CIL中的本质"
date:       2018-12-03 21:16:00
categories: C#
tags:       C# CIL CLR Lambda 闭包
author:     "SixTeen"
---

这里编写一段会出问题的for循环中带lambda表达式的错误实例(没有使用闭包)，并分析问题出在哪里：

{% highlight C# linenos %}
static void Main(string[] args)
{
    func f = null;
    for(int i = 0; i < 5; i++)
    {
        f += () =>
        {
            Console.WriteLine(i);
        };
    }

    f();
    //55555

    Console.ReadLine();
}

delegate void func();
{% endhighlight %}

生成的CIL，阅读CIL代码时候，先画出两个堆栈，一个堆栈就是ld和st使用的堆栈，另一个堆栈就是进入函数的时候通过.locals init生成的固定大小的用于直接通过index索引的调用帧，调用帧存储的类型是固定的。然后根据每一个指令进行入栈出栈，就可以模拟CIL的执行。需要注意的是，指令使用到的值和对象都是从栈中取出。

```
.method private hidebysig static void  Main(string[] args) cil managed
{
  .entrypoint //程序入口
  // 代码大小       87 (0x57)
  .maxstack  3 //使用的堆栈最大高度是3
  .locals init ([0] class ConsoleApp1.Program/func f,
           [1] class ConsoleApp1.Program/'<>c__DisplayClass0_0' 'CS$<>8__locals0',
           [2] int32 V_2,
           [3] bool V_3)
  //新建一个调用帧
  IL_0000:  nop
  IL_0001:  ldnull //将null压栈
  IL_0002:  stloc.0	//将null出栈赋值给调用栈中的V_0
  IL_0003:  newobj     instance void ConsoleApp1.Program/'<>c__DisplayClass0_0'::.ctor()
  //生成一个新的对象并压入栈顶
  IL_0008:  stloc.1 //将栈顶元素赋值给V_1
  IL_0009:  ldloc.1 //将V_1压入栈顶
  IL_000a:  ldc.i4.0 //将常数0压入栈顶
  IL_000b:  stfld      int32 ConsoleApp1.Program/'<>c__DisplayClass0_0'::i //将栈顶的值给lambda表达式中的field中的i
  IL_0010:  br.s       IL_003c //无条件跳转到对应的指令

  //-----------------这里是循环部分
  IL_0012:  nop
  IL_0013:  ldloc.0    //将v_0压入栈顶
  IL_0014:  ldloc.1    //将v_1压入栈顶
  IL_0015:  ldftn      instance void ConsoleApp1.Program/'<>c__DisplayClass0_0'::'<Main>b__0'()
  //将lambda的函数执行体放入栈顶
  IL_001b:  newobj     instance void ConsoleApp1.Program/func::.ctor(object,native int)
  //生成一个新的委托对象
  IL_0020:  call       class [mscorlib]System.Delegate [mscorlib]System.Delegate::Combine(class [mscorlib]System.Delegate, class [mscorlib]System.Delegate)
  //合并委托生成新的多播委托
  IL_0025:  castclass  ConsoleApp1.Program/func
  //转换成对应的委托类型
  IL_002a:  stloc.0
  //将栈顶赋值到v_0
  IL_002b:  nop
  IL_002c:  ldloc.1 //将v_1放到栈顶
  IL_002d:  ldfld      int32 ConsoleApp1.Program/'<>c__DisplayClass0_0'::i
  //将field i放到栈顶
  IL_0032:  stloc.2
  //赋值到v_2
  IL_0033:  ldloc.1
  //将v_1放到栈顶
  IL_0034:  ldloc.2
  //将v_2放到栈顶
  IL_0035:  ldc.i4.1
  //将常数1放到栈顶
  IL_0036:  add
  //将栈顶2个数字相加再放到栈顶
  IL_0037:  stfld      int32 ConsoleApp1.Program/'<>c__DisplayClass0_0'::i
  //设置到field i
  IL_003c:  ldloc.1    //将v_1压入栈顶
  IL_003d:  ldfld      int32 ConsoleApp1.Program/'<>c__DisplayClass0_0'::i //将field压入栈顶，这里的field是lambda中使用的i
  IL_0042:  ldc.i4.5	//将常数5压入栈顶
  IL_0043:  clt         //毕竟栈顶2层数字，栈顶小就压0，否则压1
  IL_0045:  stloc.3     //将栈顶数字存到v_3中
  IL_0046:  ldloc.3		//将v_3压入栈顶
  IL_0047:  brtrue.s   IL_0012//如果为true，非零非空就跳转到对应指令
  //----------------------

  IL_0049:  ldloc.0    //将v_0放到栈顶
  IL_004a:  callvirt   instance void ConsoleApp1.Program/func::Invoke() //调用虚函数Invoke
  IL_004f:  nop
  IL_0050:  call       string [mscorlib]System.Console::ReadLine()
  IL_0055:  pop        
  IL_0056:  ret
} // end of method Program::Main

```

这里可以看到的是，经过中间代码的处理，lambda表达式中的i，就是循环体使用的i。作为lambda这个匿名类中的一个field，它更像是i的别名，它像i一样被循环代码递增的同时，保存在lambda类中延长了生命周期。所以，lambda将外部变量保存在field中使用。它和这个外部变量总是同一个变量。

下面看一段和我们预想一样的代码，以及其由编译器生成的CIL代码。

{% highlight C# lineons %}
static void Main(string[] args)
{
    func f = null;
    for(int i = 0; i < 5; i++)
    {
        int b = i;
        f += () =>
        {
            Console.WriteLine(b);
        };
    }

    f();//01234

    Console.ReadLine();
}
{% endhighlight %}

```
  IL_0005:  br.s       IL_0032
  IL_0007:  newobj     instance void ConsoleApp1.Program/'<>c__DisplayClass0_0'::.ctor()
  IL_000c:  stloc.2
  IL_000d:  nop
  IL_000e:  ldloc.2
  IL_000f:  ldloc.1
  IL_0010:  stfld      int32 ConsoleApp1.Program/'<>c__DisplayClass0_0'::b
  IL_0015:  ldloc.0
  IL_0016:  ldloc.2
  IL_0017:  ldftn      instance void ConsoleApp1.Program/'<>c__DisplayClass0_0'::'<Main>b__0'()
  IL_001d:  newobj     instance void ConsoleApp1.Program/func::.ctor(object,
                                                                     native int)
  IL_0022:  call       class [mscorlib]System.Delegate [mscorlib]System.Delegate::Combine(class [mscorlib]System.Delegate,
                                                                                          class [mscorlib]System.Delegate)
  IL_0027:  castclass  ConsoleApp1.Program/func
  IL_002c:  stloc.0
  IL_002d:  nop
  IL_002e:  ldloc.1
  IL_002f:  ldc.i4.1
  IL_0030:  add
  IL_0031:  stloc.1
  IL_0032:  ldloc.1
  IL_0033:  ldc.i4.5
  IL_0034:  clt
  IL_0036:  stloc.3
  IL_0037:  ldloc.3
  IL_0038:  brtrue.s   IL_0007
```

我将循环体截取了出来。看到不一样了吗？这次，每次循环都会使用匿名类生成一个新的类来保存这个每次都"不一样"的变量b。思考一样，对于上一次的5轮循环中，i其实是同一个变量，因此对于CIL代码来说，其实循环体中的生成的lambda表达式都是一样的，所以和我们编写的时候思考的不一样，最终CIL代码只生成了一个匿名类实例，因为对于CIL来说，循环中的表达式是一样的。但对于变量b，因为是每次循环中的局部变量，因此它是不一样的，因此每一轮循环的lambda表达式在CIL看来都是不一样的表达式，所以必须每次都生成一个新的匿名类来表示。这也就是为什么两次输出结果不一样的原因。

同样的，我们也从这个例子中了解到了闭包的本质，通过生成一个匿名类，用field来延长局部变量的生命期，同时，匿名函数是这个匿名类里的一个方法，它使用匿名类保存的field，看起来就像使用外部变量一样。

>FIN 2018.12.03/21.16