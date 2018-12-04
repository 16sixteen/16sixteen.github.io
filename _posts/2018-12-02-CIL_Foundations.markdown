---
layout:     post
title:      "CIL代码阅读入门"
subtitle:   "CIL"
date:       2018-12-03 00:23:00
categories: C#
tags:       C# CIL CLR
author:     "SixTeen"
---

* TOC
{:toc}

## CIL

CIL是学习C#本质重要的一环。因为C#经过编译器被编译成CIL，在VES上托管执行，VES负责CIL的执行，以及为CIL的执行提供额外的服务。本文主要是搜集网上的一些关于CIL的操作码的解释，帮助看懂CIL代码。

### CIL指令

### CIL特性

### CIL操作码

主要分为5类操作：ld入栈，st出栈，运算，转移，其他。（这里的入栈出栈是针对VES虚拟机的，出栈就是将栈里的内容赋值）

|缩写|含义|操作数范围|操作数|
|---|---|---|---|
|ld|load，入栈|arg，参数<br/>loc，局部变量<br/>c，常量<br/>fld，字段<br/>null，空值|.0 表示第几个参数|
|st|store, 出栈|与ld相同|
|conv|convert，值类型转换|
|b，br|branch，跳转|
|call|调用||virt,调用虚函数|
|newarr|将对新的从零开始的一维数组（其元素属于特定类型）的对象引用推送到计算堆栈上。|
|newobj|创建一个值类型的新对象或新实例，并将对象引用（O 类型）推送到计算堆栈上。|
|nop|no operation, 如果修补操作码，则填充空间。尽管可能消耗处理周期，但未执行任何有意义的操作|
|pop|移除当前位于计算堆栈顶部的值。|
|......|

#### 代码试读

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
  //----------------------

  IL_003c:  ldloc.1    //将v_1压入栈顶
  IL_003d:  ldfld      int32 ConsoleApp1.Program/'<>c__DisplayClass0_0'::i //将field压入栈顶，这里的field是lambda中使用的i
  IL_0042:  ldc.i4.5	//将常数5压入栈顶
  IL_0043:  clt         //毕竟栈顶2层数字，栈顶小就压0，否则压1
  IL_0045:  stloc.3     //将栈顶数字存到v_3中
  IL_0046:  ldloc.3		//将v_3压入栈顶
  IL_0047:  brtrue.s   IL_0012//如果为true，非零非空就跳转到对应指令
  IL_0049:  ldloc.0    //将v_0放到栈顶
  IL_004a:  callvirt   instance void ConsoleApp1.Program/func::Invoke() //调用虚函数Invoke
  IL_004f:  nop
  IL_0050:  call       string [mscorlib]System.Console::ReadLine()
  IL_0055:  pop        
  IL_0056:  ret
} // end of method Program::Main

```

所有有用的指令的注释都标明了，再次说明需要注意的是，指令使用到的值和对象都是从栈中取出，调用函数需要的参数也是从栈中取出的，ldfld这种也是需要从栈中取出对象才能执行的。这里的lambda闭包可能会给你造成一些阅读的困扰，这个例子作为CIL代码阅读非常不合适，不过因为这个例子是我在C#本质论，lambda闭包变量，都用到了，所以直接搬过来使用。这里最后的结果是打印了5个5。

>FIN 2018.12.03/00.23