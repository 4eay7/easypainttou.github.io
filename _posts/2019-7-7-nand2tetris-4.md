---
layout: post
title: 'nand2tetris ch4 机器语言'
subtitle: 'ch4 机器语言'
date: 2019-7-7
categories: 学习笔记
cover: /images/n2t_all_0.png
tags: 计算机系统
---

* contents
{:toc}

# 概述

机器语言处于软件和硬件之间，既是一个编程工具，也是硬件平台的组成部分。它旨在编写低级程序当作一系列的机器指令，这些指令命令处理器执行算术和逻辑运算、从内存中存取值、将一个寄存器的值移动到另一个寄存器、测试布尔条件等。和高级语言不同，机器语言的要求是直接执行和完全控制特定的硬件平台。

了解用机器语言编写的低级程序，可以方便我们熟悉计算机系统。这不仅帮助我们理解如何编程，而且可以知道硬件为何这样设计。

下面会先大致介绍机器语言，然后给出Hack的机器语言的具体细节，包括二进制版本（Binary）以及符号汇编版本（Symbolic assembly）。注意，我们在这里抽象底层硬件的具体实现，只需要关注一个处理器（Processor）、一个内存（Memory）、一些寄存器（Registers）。

# 机器（Machines）

## 内存（Memory）

这里指存储数据和指令的设备。一个连续的固定字宽的单元的数组（Words or Locations），每一个单元都有唯一的地址（Address）。

下面我们将一个word简称为Memory[address]，RAM[address]，或者 M[address]。

## 处理器（Processor）

也叫做中央处理单元（Central Processing Unit or CPU），指能够执行一些固定的基本操作的设备，包括算术和逻辑操作，内存访问操作，控制（也被称作分支，Branching）操作。操作数（Operands）是来自寄存器或内存的二进制值，同样，操作结果也存放在寄存器或者内存中。

## 寄存器（Register）

内存访问是一个相对较慢的操作，需要长指令格式（比如可能需要32位的地址）。于是，大多数处理器都配备有寄存器，每个寄存器能存一个值。寄存器相当于一个位于处理器的高速本地内存，可以快速操纵数据和指令。它使程序减少内存访问，以提高程序执行效率。

# 语言（Languages）

机器语言是一系列的被编码的指令，它的含义取决于硬件细节和机器语言语法。

由于二进制码的含义相当晦涩，通常机器语言会指定它的二进制码（Binary codes）和符号助记符（Symbolic mnemonics）。

比如，假设有某种add操作，可以直接写成1010001100011001，或者用符号写成ADD R3,R1,R9。

更进一步，我们不仅可以借助符号读程序，也可以这样写程序，而不是使用二进制。我们可以使用一个文本处理程序，将符号解析为二进制，汇编为二进制机器指令。这个符号就叫做汇编语言（Assembly language, or simply assembly），而这个程序叫做汇编器（Assembler）。

不同的计算机的CPU的操作、寄存器的种类和数量、汇编语言的语法都有可能不同（a Tower of Babel of machine languages）。

尽管不同，它们却都有一些相似的通用的命令。

# 命令（Commands）

## 算术和逻辑运算（Arithmetic and Logic Operations）

每一个计算机都要进行基本的算术和逻辑运算。

![算术和逻辑运算](/images/n2t_4_0.png)

## 内存访问（Memory Access）

内存访问指令有2类，一是算术和逻辑运算命令，可能不仅操纵寄存器，还有内存，二是存取（load and store）命令，会在寄存器和内存之间移动数据。内存访问命令有不同的寻址方式（Addressing modes），也就是获取内存的字的具体的方法。通常，不同的计算机提供不同的可能方式和表达，但是以下3种一般都会支持。

### 直接寻址（Direct addressing）

直接给出具体地址或者用符号表示一个具体的地址。

![直接寻址](/images/n2t_4_1.png)

### 立即数寻址（Immediate addressing）

加载常数（出现在指令编码中的值）到寄存器。不是这个常数所对应的地址上的值，而是这个常数本身。

![立即数寻址](/images/n2t_4_2.png)

### 间接寻址（Indirect addressing）

地址没有被硬编码到指令中，而是指定了一个保存所需地址的内存位置。这种方式被用于处理指针（Pointers）。

比如，高级指令x=foo[j]，foo是一个数组变量，x、j是整型变量。在声明和初始化foo后，编译器分配一个内存段来保存数组数据，使foo指代一个段的基地址（Base address）。foo[j]就是在foo上偏移了一个j的地址上的值。

![间接寻址](/images/n2t_4_3.png)

## 控制流（Flow of Control）

当一个程序线性地执行命令时，可能会出现分支跳转到一个地址，而不是继续执行下一个命令。

分支的目的有很多，比如重复（Repetition，循环。。。），条件执行（Conditional execution），子程序调用（Subroutine calling）。跳转的位置可以是一个给定的标记（Label）。

跳转有无条件跳转（Unconditional jump）和有条件跳转（Conditional jump，可以有明确的布尔条件或者是上一个命令的作用）。

以上为机器语言的介绍，接下来是Hack的机器语言的描述。

# Hack的机器语言

## 概观

注意：Hack的机器语言是一种很简单的机器语言，现在大多数计算机都不像Hack，它们有更多的指令、数据类型、寄存器、指令格式、寻址方式。（当然Hack可以用软件来实现它们）。

Hack是一个冯诺依曼结构的计算机，16位，组成部分有CPU、2个分立的存储模块（一个指令存储器、一个数据存储器）、2个内存映射I/O设备（一个屏幕、一个键盘）。

## 内存地址空间

Hack程序员关注2个地址空间：指令存储器和数据存储器。2个存储器都是16位字宽，15位的地址空间，即每个存储器最大可寻址大小是32K（即2^15） 16位的字。

CPU只执行指令存储器中的指令。指令存储器是一个只读的设备，程序是由一些外部方法写入的。比如，一个预先烧录好的ROM芯片可以实现指令存储器。如果要一个新的程序需要换一个ROM，类似更换游戏机里的盒式磁带。

## 寄存器

关注2个16位的寄存器，D寄存器和A寄存器，它们可以被算术和逻辑指令操纵。D寄存器用来存数据，A寄存器当作数据以及地址（指令存储器或数据存储器的地址）的寄存器。

A寄存器可以加快直接访问存储器。Hack的指令是16位的，地址是15位的，所以不可能把操作和地址放在一条指令中。于是Hack的内存访问指令会操纵一个隐式的地址，标记为M。

比如，对于数据存储器，要实现的是D=Memory[516]-1，我们就先用一条指令设置A寄存器为516，再用一条D=M-1指令。

比如，对于指令存储器，要实现的是goto 35，先用一条指令设置A寄存器为35，再使用goto指令，计算机会在下一个时钟周期取InstructionMemory[35]的指令。


## 指令

Hack的2种基础指令：
- 地址指令 A指令
- 计算指令 C指令


### A指令

设置A寄存器为一个15位的值。

![A指令](/images/n2t_4_4.png)

A指令的3种用处：
- 设置常数
- 设置后续C指令阶段要操纵的数据存储器的地址
- 设置后续C指令阶段要操纵的指令存储器的地址



### C指令

决定计算机的所有可能操作。指令有3个部分，解决3个问题，计算什么（comp），在哪里存储被计算的值（dest），下一步做什么（jump）。

![C指令](/images/n2t_4_5.png)

#### comp部分

这里出现的+、-等符号应作为助记符的一部分，而不是一种操作符（比如D+A可以当作ADD。。。？）

![comp](/images/n2t_4_6.png)


#### dest部分

![dest](/images/n2t_4_7.png)

#### jump部分

![jump](/images/n2t_4_8.png)

#### A寄存器的冲突使用
C指令中，A寄存器既可以用于访问数据存储器的M，也可以访问指令存储器。为了避免冲突，一条指令中A寄存器不能同时使用这2种用法。

## 符号（Symbols）

在汇编语言中，访问一个内存地址，可以使用常数或者符号。

符号有以下3种：

### 预定义符号（Predefined symbols）
RAM的一部分地址可以用预定义符号指定。
- 虚拟寄存器（Virtual registers）：用R0~R15指定RAM[0]~RAM[15]。
- 预定义指针（Predefined pointers）：用SP、LCL、ARG、THIS、THAT指定RAM[0]~RAM[4]。
- I/O指针（I/O pointers）：用SCREEN、KBD指定RAM[16384]（RAM[0x4000]）和RAM[24576]（RAM[0x6000]），这是屏幕和键盘的内存映射的基地址。

### 标记符号（Label symbols）

用户定义的用于表示goto的目的地址的符号，用伪命令“(Xxx)”来声明。

### 变量符号（Variable symbols）

用户定义的符号Xxx，Xxx不是预定义符号和标记符号，那么就是一个变量，汇编器会分配一个唯一的内存地址，从RAM[0x0010]开始。

## 输入/输出处理（Input/Output Handling）

Hack有2个外设（peripheral devices），屏幕和键盘。它们通过内存映射（memory maps）来交互。物理I/O设备和内存映射通过持续的刷新循环来同步。

### 屏幕
256行、每行512个像素的黑白的屏幕。从RAM[0x4000]开始有8K words的内存映射，从左上角开始一个位代表一个像素，1=black，0=white。第r行第c列的像素被映射到RAM[0x4000+r*32+c/16]个word的第c%16位（从LSB到MSB）。


### 键盘

只有RAM[0x6000]一个word的内存映射，某按键被按下，这里存的就是ASCII码，没有按键按下就是0。

其他的按键对应的数字：

![键码](/images/n2t_4_9.png)

## 语法约定和文件格式（Syntax Conventions and File Format）

### 二进制文件（Binary code files）

扩展名为hack。一行一个16位01串，指定一个机器语言指令。

### 汇编文件（Assembly language files）

扩展名为asm。

每行为一个Instruction或者 (Symbol)。

常量非负且为10进制，用户定义的符号可以是字母、数字、下划线（_）、点（.）、美元符号（$）、冒号（:），不能以数字开头。

//后为单行注释。

约定标记名大写，变量名小写。


# 项目

这次使用2个新的工具，汇编器和CPU模拟器。

[下载](https://www.nand2tetris.org/software)

[汇编器的使用说明](https://docs.wixstatic.com/ugd/44046b_759f4f811ad14e12ac45bc60dd679fa3.pdf)

[CPU模拟器的使用说明](https://docs.wixstatic.com/ugd/44046b_f63aba2611944e82974c9c5d5a3821fe.pdf)

project要求用汇编语言编写2个程序。

乘法程序（Mult.asm）：实现R0乘R1，结果放在R2。

I/O处理程序（Fill.asm）：按下任意按键屏幕变黑，否则变白。

（我的代码见github