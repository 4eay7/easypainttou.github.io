---
layout: post
title: 'nand2tetris ch2  布尔运算'
subtitle: 'ch2  布尔运算'
date: 2019-7-7
categories: 学习笔记
cover: /images/n2t_all_0.png
tags: 计算机系统
---

* contents
{:toc}

# 概述

这一章要使用之前制造的基本逻辑门，来制造出算术逻辑单元（ALU），这个芯片执行计算机的所有的算术和逻辑运算，对于理解CPU和计算机很重要。

首先，要造一个加法器（adder），在此之前需要了解以下知识。

# 二进制数

了解一下进制、进制转换，在此不总结。

# 二进制数加法

和十进制加法类似，从LSB开始对应的位相加，一直加到MSB，若某一位产生进位1，则加到下一位，MSB如果产生了进位，就是溢出（overflow）。可以发现能由全加器构建。

# 有符号二进制数

n位二进制系统可以产生$2^n$个不同的位模式。为了表示有符号二进制数，一个自然的方法是把空间分为2个大小相等的子集，一个子集表示正数，另一个子集表示负数。

有符号数的编码方式应当使得硬件实现尽量简单。现在常用的方法是二补数或补码（2’s complement，或基数为2的radix complement）。在n位二进制系统，一个数x的补码的定义如下：

$\overline{x}=\begin{cases}2^n-x,&x\ne0 \cr 0 ,& otherwise\end{cases}$

![2's complement representation](/images/n2t_2_0.png)

可以发现补码表示的性质：

- 一个系统共可以表示$2^n$个有符号数，范围是$[-2^{n-1},2^{n-1}-1]$。

- 正数的MSB为0，负数的MSB为1。

- 由x得到-x的方法是，除了最低一位的1以及比它低位的0（lowbit？），每一位取反。还有一种硬件实施更简单的方法，所有位取反，然后加1。

补码表示的好处是有符号数的加法不需要特殊的硬件。2个有符号数相加，直接用它们的补码表示相加。2个有符号数相减，如x-y，相当于x+(-y)，即x的补码表示和-y的补码表示相加。

# 加法器（Adders）

这里要知道3种

## 半加器（Half-adder）

2个1位的数相加

![half adder](/images/n2t_2_1.png)

## 全加器（Full-adder）

3个1位的数相加

![full adder](/images/n2t_2_2.png)

## 加法器（Adder）

2个n位的数相加，这里n=16。

![adder](/images/n2t_2_3.png)

## 其它

这里用的加法器的实现是模拟笔算，称为串行进位加法器，结构简单，然而运行速度慢，一般解决方法是使用超前进位加法器。

还有一种特殊目的的加法器，增量器（Incrementer），可以给1个数加1。

# ALU

这里介绍Hack使用的ALU，它计算$out=f_i(x,y)$，$f_i$是某种算术和逻辑运算，这里的$f_i$有18种。我们通过6个控制位（control bits）来控制使用的$f_i$。

![ALU](/images/n2t_2_4.png)

![ALU真值表](/images/n2t_2_5.png)

这里没有乘法或者除法以及其他一些运算，这些运算会在OS一章实现。详细的布尔运算和ALU设计可以在其他计算机体系结构的书中找到。

# 项目

project要求使用之前章节造好的芯片，实现一个ALU（我的代码见github

需要建造的芯片列表：
- HalfAdder
- FullAdder
- Add16
- Inc16
- ALU