---
layout:     post
title:      "归结算法"
subtitle:   "CNF Skolem化 归结"
date:       2015-11-24 15:40:00
categories: AI
tags:       人工智能
author:     "SixTeen"
header-img: "img/solvingReport.jpg"
published:   true
---

要使用归结算法就得先把一阶逻辑转换成CNF，因为一阶逻辑中有量词，因此需要Skolem化。

Skolem化就是消除量词：

1.消除蕴含: $$ A \Rightarrow B \equiv \lnot A \lor B $$ 

2.消除量词: 

$$ \exists x \forall y \exists z \forall m \exists n f(x,y,z,m,n) $$

$$ \forall y \exists z \forall m \exists n f(A,y,z,m,n) \{x/A\} $$

$$ \forall y \forall m \exists n f(A,y,F(y),m,n) \{x/A, z/F(y)\} $$

$$ \forall y \forall m f(A,y,F(y),m,G(y,m)) \{x/A,z/F(y),n/G(y,m)\} $$

归结：

前提：$$ \forall Horse(x) \Rightarrow Animal(x) $$

结论: $$ \forall x,h Horse(x) \land HeadOf(h,x) \Rightarrow \exists y Animal(y) \land HeadOf(h,y) $$

证明根据前提能推导出结论，其实就是要证明$$ KB \lor \lnot A $$是恒真式，因为如果是恒真，证明KB蕴含着A，所以$$ \lnot A \lor A = true $$

先将前提Skolem化：

式子1：$$ \lnot Horse(x) \lor Animal(x) $$

式子2：$$ Horse(x) \land HeadOf(h,x) $$

式子3：结论的非：$$ \lnot Animal(y) \lor \lnot HeadOf(h,y) $$

归结1：$$ 式子3 \lor 式子2 = \lnot Animal(x) \lor Horse(x) \{y/x\} $$

归结2：$$ \lor 式子1 = true $$ 

    FIN 12.12/17.35
