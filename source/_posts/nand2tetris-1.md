---
title: nand2tetris ch1 布尔逻辑
date: 2019-7-7 0:0:1
categories: 课程
tags:
	- 计算机系统
	- nand2tetris
	- 本科补全
---

# 概述

基于物理学制造出晶体管，晶体管和电路构造出与非门（Nand），与非门（或者 或非门，它们是primitive gates，其它门都是composite gates）可以构造出与门、或门、非门，于是可构造出各种数字电路（任何布尔表达式都可以用与、或、非表示）。
所以要构建计算机（课程中名为Hack，一个16位计算机），首先需要了解的是Nand相关的知识。

学习Nand前，先学习布尔代数、真值表、布尔表达式、范式，这些在此不做总结。

这里我们只关注一个芯片的外部接口（输入、输出），不关注具体实现。

# 基本逻辑门

## 与门、或门、非门

![and or not](/images/n2t_1_0.png)

## 异或门（Exclusive or）

![xor](/images/n2t_1_1.png)

## 数据选择器（Multiplexor）

![mux](/images/n2t_1_2.png)

![mux真值表](/images/n2t_1_3.png)

## 数据分配器（Demultiplexor）

![dmux](/images/n2t_1_4.png)

![dmux真值表](/images/n2t_1_5.png)

# 多位（Multi-bit）版本的基本门电路

输入输出可能是多于1位的二进制数，按位做运算。

# 多路（Multi-way）版本的基本门电路

有更多的输入输出针脚，实现类似操作。

# 硬件描述语言（HDL）

现实生活中要生产芯片，需要电路要达到预期的功能，最快的速度，最少的花费，大多数人不会直接制造，而是会先在软件上模拟电路的运行。

课程提供了一个硬件模拟器。

[下载](https://www.nand2tetris.org/software)

[使用说明](https://docs.wixstatic.com/ugd/44046b_bfd91435260748439493a60a8044ade6.pdf)

HDL的编写和测试脚本的编写见书附录A、B。

# 项目

project要求使用内建的Nand，编写HDL代码构造其他芯片（我的代码见github

需要运行硬件模拟器，查看其使用说明的1-3部分，以及附录A1-6。

需要建造的芯片列表：
- Not
- Not16
- And
- And16
- Or
- Or8Way
- Or16
- DMux
- DMux4Way
- DMux8Way
- Mux
- Mux16
- Mux4Way16
- Mux8Way16
- Xor
