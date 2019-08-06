---
title: sicp ch1-3 笔记
date: 2019-8-5
categories: 课程
tags:
	- 程序设计
	- sicp
	- 本科补全
---

# 介绍

sicp是编程方面的入门课程。

有关sicp的资源和评价很多，在此不多介绍。

一些相关资源链接：

[书](https://mitpress.mit.edu/sites/default/files/sicp/full-text/book/book.html)

[SICP 解题集](https://sicp.readthedocs.io/en/latest/)

[scheme tutorial](https://deathking.github.io/yast-cn/)

# ch1 用过程构建抽象

## 概述

### 计算过程（computational process）

过程是计算机中抽象的存在，能够产生其他抽象的东西——数据（data）。过程的演变被程序（program）指导，程序由编程语言（programming languages）中的符号表达组成。程序可能有错误，需要debug。

### 使用lisp编程

lisp（LISt Processing的缩写）发明于20世纪50年代后期，被用于计算模型的某种逻辑表达式（称为递归方程（recursion equations））的使用的推理的形式。lisp有很多方言，这里使用scheme。

lisp不是主流语言，使用的原因是，它拥有独特的特点，使它成为学习重要的编程结构和数据结构以及和支持它们的语言特点相关联的极好的媒介。最重要的是，lisp的过程的描述（称为procedure）本身可以被作为lisp数据来表达或操作。


## 编程的要素

强大的编程语言不仅是指导计算机执行任务的方法，而且能作为组织思想和过程的框架。

强大的语言的3个机制：
- 基础表达式（primitive expressions）：最简单的实体
- 组合的方法（means of combination）：使用简单的元素构造复合元素
- 抽象的方法（means of abstraction）：组合元素被作为单元命名和操作

在编程中，我们处理2类元素：过程和数据。数据是我们想要操作的东西。过程是操作数据的规则的描述。任何强大的程序设计语言必须能表述基本的数据和过程，还需要提供对过程和数据组合和抽象的方法。

第1章只处理最简单的数值数据，以关注构建过程的规则。

以下大概是scheme的简单使用，有的不展开。

### 表达式

一些与scheme解释器交互的实例，输入表达式以求值（evaluate）。包括基础表达式、组合式（combinations）。

### 命名和环境

程序设计语言需要提供一种通过名字去使用计算对象的方式。名字标识符被称为变量，它的值是对应的对象。

好处是，每次使用计算对象时，不需要重复写出细节，能逐步创建名字-对象关联，采用递增的方式开发和调试程序。

解释器实现关联后又能提取这些值的存储被称为环境（environment）。

### 组合式的求值

组合式求值的规则（不能处理特殊形式（special
forms））：
1. 对子表达式求值
2. 将最左子表达式的值（过程，运算符）应用于相应的实际参数，实际参数是其它子表达式的值（运算对象）

这一规则的应用是递归的。可以简洁地描述嵌套的情况。可以采用树的形式表示组合式求值过程，一个带分支的节点表示某个组合式，由它出发的分支对应于组合式的运算符和运算对象，叶子节点表示基本运算符或数值，运算对象的值向上穿行。值向上穿行的求值形式是更一般的计算过程——树形积累（tree accumulation）的例子。

```lisp
(* (+ 2 (* 4 6))
   (+ 3 5 7))
```
![表示每个子组合式的值的树](/images/sicp_1_0.png)

### 复合过程

我们已经看到了lisp里的某些元素，它们必然也会出现在任何一种强大的程序设计语言里。包括：
- 数和算术运算是基本的数据和过程。
- 组合式的嵌套提供了一种组织起多个操作的方法。
- 定义是一种受限的抽象手段，它为名字关联相应的值。

过程定义为复合操作提供名字，以后可以将其作为一个单元使用。

### 过程应用的代换模型（substitution model）

求值一个组合式，解释器完全按照上面的组合式求值的规则求值。

对于基本运算符，解释器已经做好。对于复合过程，计算过程是，用实参取代形参，对过程求值。这种计算过程被称为过程应用的代换模型。

置换的目的是为了帮助我们理解程序应用，并不是提供解译器如何真实工作的描述。典型的解译器并不通过操作程序的文本来置换形式参数的值执行程序应用。之后会介绍一系列逐渐复杂的模型来解释解译器如何工作的。

#### 应用序（applicative order）

先对运算符和各个运算对象求值，然后将得到的过程应用于得到的实参。

lisp采用应用序。

#### 正则序（normal order）

先用运算对象表达式代换形参，直到一个只包含基本运算符的表达式，然后再求值。

### 条件表达式和谓词（predicates）

lisp有分情况分析的特殊形式。

谓词指返回真或假的过程，或求真或假的表达式。

### 当作黑盒抽象的过程

对于一个问题，可以自然地分解为若干子问题。分解的重要性在于每一个过程完成了一件清楚表明的工作，使它们可被用作定义其它过程的模块。模块可以看作黑箱，只关注结果，无需关注细节实现。它是一个过程抽象。

## 过程（procedures）和他们产生的过程（processes）

看清不同种类的过程会产生什么计算过程，才能构造可靠的程序。

以下给出一些过程演化的典型模式。

### 线性递归（linear recursion）

过程调用完自身（一次调用）后调用了其它过程(会被推迟执行形成链条)。

展开阶段，计算过程构造了推迟进行的操作所形成的链条。收缩阶段，运算实际执行。推迟进行需要保存和链条长度成正比的信息(有一些隐含信息)。

```lisp
(define (factorial n)
  (if (= n 1)
      1
      (* n (factorial (- n 1)))))
```

![线性递归](/images/sicp_1_1.png)

### 线性迭代（linear iteration）

过程调用完自身后没有调用其它过程，被称为尾递归（tail-recursive）。

没有展开和收缩的过程。计算过程的状态可用固定数量状态变量描述。

```lisp
(define (factorial n)
  (fact-iter 1 1 n))

(define (fact-iter product counter max-count)
  (if (> counter max-count)
      product
      (fact-iter (* counter product)
                 (+ counter 1)
                 max-count)))
```
![线性迭代](/images/sicp_1_2.png)

在大部分其他语言中(比如C)，只要是语法是递归的，计算过程就是递归的，循环需要使用循环结构(for/while)实现。而lisp中，尾递归计算过程是迭代。

### 树形递归（tree recursion）

过程调用完自身（多次调用）后调用了其它过程。

```lisp
(define (fib n)
  (cond ((= n 0) 0)
        ((= n 1) 1)
        (else (+ (fib (- n 1))
                 (fib (- n 2))))))
```
![树形递归](/images/sicp_1_3.png)

### 增长的阶

讲了时空复杂度。。

举了很多例子。。。

## 使用高阶过程（higher-order procedures）制定抽象

高阶过程是过程作为过程的参数或返回值的过程。

模式作为一个概念表达时，高阶过程是有力的抽象机制。

### 过程作为参数（arguments）

比如，要实现sigma的方法（或概念？）。

$$\sum_{n=a}^{b}f(n)=f(a)+...+f(b)$$

```lisp
(define (sum term a next b)
  (if (> a b)
      0
      (+ (term a)
         (sum term (next a) next b))))
```

### 使用lambda构建过程

lambda用于生成匿名函数。用于定义一些简单的不需要名字的过程。

比如，实现积分：

```lisp
(define (integral f a b dx)
  (* (sum f
          (+ a (/ dx 2.0))
          (lambda (x) (+ x dx))
          b)
     dx))
```

### 作为通用方法的过程

讲了几个例子。。。

### 作为返回值（returned values）的过程

比如，表达平均阻尼的思想：

```lisp
(define (average-damp f)
  (lambda (x) (average x (f x))))
```

### 抽象和第一级过程（first-class procedures）

程序设计语言会对计算元素可能使用方式加限制，带有最少限制的元素被称为具有第一级状态。第一级元素的某些权力或者特权包括：
- 可以用变量命名
- 可以提供给过程作为参数
- 可以由过程作为结果返回
- 可以包含在数据结构中

lisp给了过程完全第一级状态。

# ch2 使用数据构建抽象

## 概述

我们只有简单的数值数据，许多问题不够处理。因此要构造一些对象（复合数据（compound data），程序设计语言需要提供胶水（glue）以构造），以模拟复杂现象。复合数据能提高程序的模块性，提高程序设计语言的表达能力。

## 介绍数据抽象

数据抽象将数据的使用和实现分离。使用时主要用到选择函数（selectors）和构造函数（constructors），不需要了解实现细节。

### 例子：有理数算术操作

```lisp
(define (make-rat n d) (cons n d))

(define (numer x) (car x))

(define (denom x) (cdr x))
```

### 抽象屏障（abstraction barriers）

水平线是一层抽象屏障(过程)，上面使用，下面实现。

![抽象屏障](/images/sicp_2_0.png)

优点是使程序便于维护和修改。

###  数据是什么？
数据可以是过程。。。

序对（pairs）的过程性表示

```lisp
(define (cons x y)
  (define (dispatch m)
    (cond ((= m 0) x)
          ((= m 1) y)
          (else (error "Argument not 0 or 1 -- CONS" m))))
  dispatch)

(define (car z) (z 0))

(define (cdr z) (z 1))
```

说明过程可以作为对象。有关的程序设计风格被称为消息传递（message passing）。

#### Church数

提到了lambda演算。。。

[一个易懂的系列文章](http://cgnail.github.io/academic/lambda-index/)


## 层次性数据和闭包（closure）性质

之前构造复合数据的胶水是序对。

序对的盒子和指针表示

```lisp
(cons 1 2)
```
![盒子和指针表示](/images/sicp_2_1.png)

可以建立元素本身是序对的序对，这一性质叫做cons的闭包性质。

组合数据对象具有闭包性质是说，组合起来的数据本身可以通过同样的操作再次组合。

### 序列的表示

数据对象的有序集被称为序列（sequence）。

通过cons嵌套形成的序列称为表（list）。

```lisp
(cons 1
      (cons 2
            (cons 3
                  (cons 4 nil))))
```

![盒子和指针表示](/images/sicp_2_2.png)

#### 表操作
讲了有关序列的操作list-ref、length、append、map 的具体实现。

### 层次性结构
序列的序列，也可以认为是树

```lisp
(cons (list 1 2) (list 3 4))
```
![盒子和指针表示](/images/sicp_2_3.png)

![树](/images/sicp_2_4.png)

### 序列作为约定的界面

一种强有力的设计原理。

#### 序列操作

对于2个过程的信号流图

![信号流图](/images/sicp_2_5.png)

```lisp
(define (sum-odd-squares tree)
  (accumulate +
              0
              (map square
                   (filter odd?
                           (enumerate-tree tree)))))
```
```lisp
(define (even-fibs n)
  (accumulate cons
              nil
              (filter even?
                      (map fib
                           (enumerate-interval 0 n)))))
```
表示成序列操作能帮我们得到模块化的程序设计。

## 符号数据

为了扩充语言的表述能力，引进任意符号作为数据的功能。

## 抽象数据的多重表示

数据对象可能有多种表示方法。

比如复数，可以表示成直角坐标形式和极坐标形式。设计一个程序的人在数据表示的选择上不能达成一致。所以除了需要表示和使用的抽象屏障，还要隔离互不相同的设计选择的抽象屏障。需要方法以将模块组合成大型系统。

### 复数表示

复数的加法减法使用直角坐标更方便，乘法除法使用极坐标更方便。所以
```lisp
(define (add-complex z1 z2)
  (make-from-real-imag (+ (real-part z1) (real-part z2))
                       (+ (imag-part z1) (imag-part z2))))
(define (sub-complex z1 z2)
  (make-from-real-imag (- (real-part z1) (real-part z2))
                       (- (imag-part z1) (imag-part z2))))
(define (mul-complex z1 z2)
  (make-from-mag-ang (* (magnitude z1) (magnitude z2))
                     (+ (angle z1) (angle z2))))
(define (div-complex z1 z2)
  (make-from-mag-ang (/ (magnitude z1) (magnitude z2))
                     (- (angle z1) (angle z2))))
```
但是复数的实现有2种
```lisp
;;直角坐标

(define (real-part z) (car z))
(define (imag-part z) (cdr z))
(define (magnitude z)
  (sqrt (+ (square (real-part z)) (square (imag-part z)))))
(define (angle z)
  (atan (imag-part z) (real-part z)))
(define (make-from-real-imag x y) (cons x y))
(define (make-from-mag-ang r a) 
  (cons (* r (cos a)) (* r (sin a))))

;;极坐标
(define (real-part z)
  (* (magnitude z) (cos (angle z))))
(define (imag-part z)
  (* (magnitude z) (sin (angle z))))
(define (magnitude z) (car z))
(define (angle z) (cdr z))
(define (make-from-real-imag x y) 
  (cons (sqrt (+ (square x) (square y)))
        (atan y x)))
(define (make-from-mag-ang r a) (cons r a))

```
### 带标志数据

最小允诺原则（principle of least commitment）:由选择函数和构造函数形成的抽象屏障，把选择数据的具体表示形式的事情向后延迟。
在同一个系统中若要有多种表示方式，需要使用类型标志以区分。

```lisp
(define (attach-tag type-tag contents)
  (cons type-tag contents))
(define (type-tag datum)
  (if (pair? datum)
      (car datum)
      (error "Bad tagged datum -- TYPE-TAG" datum)))
(define (contents datum)
  (if (pair? datum)
      (cdr datum)
      (error "Bad tagged datum -- CONTENTS" datum)))
```
以此区分↓
```lisp
(define (rectangular? z)
  (eq? (type-tag z) 'rectangular))
(define (polar? z)
  (eq? (type-tag z) 'polar))
```

```lisp
;;直角坐标表示的修改版

(define (real-part-rectangular z) (car z))
(define (imag-part-rectangular z) (cdr z))
(define (magnitude-rectangular z)
  (sqrt (+ (square (real-part-rectangular z))
           (square (imag-part-rectangular z)))))
(define (angle-rectangular z)
  (atan (imag-part-rectangular z)
        (real-part-rectangular z)))
(define (make-from-real-imag-rectangular x y)
  (attach-tag 'rectangular (cons x y)))
(define (make-from-mag-ang-rectangular r a) 
  (attach-tag 'rectangular
              (cons (* r (cos a)) (* r (sin a)))))

;;极坐标表示的修改版
(define (real-part-polar z)
  (* (magnitude-polar z) (cos (angle-polar z))))
(define (imag-part-polar z)
  (* (magnitude-polar z) (sin (angle-polar z))))
(define (magnitude-polar z) (car z))
(define (angle-polar z) (cdr z))
(define (make-from-real-imag-polar x y) 
  (attach-tag 'polar
               (cons (sqrt (+ (square x) (square y)))
                     (atan y x))))
(define (make-from-mag-ang-polar r a)
  (attach-tag 'polar (cons r a)))
```

通用型选择函数实现这样的过程，检查参数的标志，然后调用相应过程
```lisp
(define (real-part z)
  (cond ((rectangular? z) 
         (real-part-rectangular (contents z)))
        ((polar? z)
         (real-part-polar (contents z)))
        (else (error "Unknown type -- REAL-PART" z))))
(define (imag-part z)
  (cond ((rectangular? z)
         (imag-part-rectangular (contents z)))
        ((polar? z)
         (imag-part-polar (contents z)))
        (else (error "Unknown type -- IMAG-PART" z))))
(define (magnitude z)
  (cond ((rectangular? z)
         (magnitude-rectangular (contents z)))
        ((polar? z)
         (magnitude-polar (contents z)))
        (else (error "Unknown type -- MAGNITUDE" z))))
(define (angle z)
  (cond ((rectangular? z)
         (angle-rectangular (contents z)))
        ((polar? z)
         (angle-polar (contents z)))
        (else (error "Unknown type -- ANGLE" z))))
```

构造函数
```lisp
(define (make-from-real-imag x y)
  (make-from-real-imag-rectangular x y))
(define (make-from-mag-ang r a)
  (make-from-mag-ang-polar r a))
```

于是运算的过程不需要做修改

![通用复数运算系统结构](/images/sicp_2_6.png)

### 数据导向（data-directed）程序设计和可加性

检查数据项的类型以此调用某个适当过程叫做基于类型的分派。优点是有模块性。缺点，一是通用型界面过程必须知道所有不同的表示，增加一个类型时必须对所有通用型界面过程增加子句以检查新类型，二是独立设计时需要保证加到系统时不会出现名字相同的过程。

以上通用型界面技术不具有可加性。大型系统需要解决这个问题。

一种称为数据导向的程序设计编程技术解决了这个问题。

需要一个表格

![表格](/images/sicp_2_7.png)

需要2个对操作-类型表进行操作的过程
```lisp
;;安装
(put <op> <type> <item>)

;;查找获取
(get <op> <type>)
```

```lisp
;;直角坐标表示
(define (install-rectangular-package)
  ;; internal procedures
  (define (real-part z) (car z))
  (define (imag-part z) (cdr z))
  (define (make-from-real-imag x y) (cons x y))
  (define (magnitude z)
    (sqrt (+ (square (real-part z))
             (square (imag-part z)))))
  (define (angle z)
    (atan (imag-part z) (real-part z)))
  (define (make-from-mag-ang r a) 
    (cons (* r (cos a)) (* r (sin a))))
  ;; interface to the rest of the system
  (define (tag x) (attach-tag 'rectangular x))
  (put 'real-part '(rectangular) real-part)
  (put 'imag-part '(rectangular) imag-part)
  (put 'magnitude '(rectangular) magnitude)
  (put 'angle '(rectangular) angle)
  (put 'make-from-real-imag 'rectangular 
       (lambda (x y) (tag (make-from-real-imag x y))))
  (put 'make-from-mag-ang 'rectangular 
       (lambda (r a) (tag (make-from-mag-ang r a))))
  'done)
;;极坐标同理
(define (install-polar-package)
  ;; internal procedures
  (define (magnitude z) (car z))
  (define (angle z) (cdr z))
  (define (make-from-mag-ang r a) (cons r a))
  (define (real-part z)
    (* (magnitude z) (cos (angle z))))
  (define (imag-part z)
    (* (magnitude z) (sin (angle z))))
  (define (make-from-real-imag x y) 
    (cons (sqrt (+ (square x) (square y)))
          (atan y x)))
  ;; interface to the rest of the system
  (define (tag x) (attach-tag 'polar x))
  (put 'real-part '(polar) real-part)
  (put 'imag-part '(polar) imag-part)
  (put 'magnitude '(polar) magnitude)
  (put 'angle '(polar) angle)
  (put 'make-from-real-imag 'polar
       (lambda (x y) (tag (make-from-real-imag x y))))
  (put 'make-from-mag-ang 'polar 
       (lambda (r a) (tag (make-from-mag-ang r a))))
  'done)
```
通过这个过程访问表
```lisp
(define (apply-generic op . args)
  (let ((type-tags (map type-tag args)))
    (let ((proc (get op type-tags)))
      (if proc
          (apply proc (map contents args))
          (error
            "No method for these types -- APPLY-GENERIC"
            (list op type-tags))))))
```
定义通用型选择函数
```lisp
(define (real-part z) (apply-generic 'real-part z))
(define (imag-part z) (apply-generic 'imag-part z))
(define (magnitude z) (apply-generic 'magnitude z))
(define (angle z) (apply-generic 'angle z))
```
定义构造函数
```lisp
(define (make-from-real-imag x y)
  ((get 'make-from-real-imag 'rectangular) x y))
(define (make-from-mag-ang r a)
  ((get 'make-from-mag-ang 'polar) r a))
```

### 消息传递（message passing）

将表按列分解，不采用智能操作，而是采用智能数据对象。数据对象作为一个过程，以操作的名字作为参数，执行指定操作。

例子
```lisp
(define (make-from-real-imag x y)
  (define (dispatch op)
    (cond ((eq? op 'real-part) x)
          ((eq? op 'imag-part) y)
          ((eq? op 'magnitude)
           (sqrt (+ (square x) (square y))))
          ((eq? op 'angle) (atan y x))
          (else
           (error "Unknown op -- MAKE-FROM-REAL-IMAG" op))))
  dispatch)
```
这个改成这个
```lisp
(define (apply-generic op arg) (arg op))
```

## 带有通用型操作的系统

使用数据导向构造一个算数运算包，系统具有可加性。

![算术运算包](/images/sicp_2_8.png)

### 通用算术过程
```lisp
(define (add x y) (apply-generic 'add x y))
(define (sub x y) (apply-generic 'sub x y))
(define (mul x y) (apply-generic 'mul x y))
(define (div x y) (apply-generic 'div x y))

;;scheme数字
(define (install-scheme-number-package)
  (define (tag x)
    (attach-tag 'scheme-number x))    
  (put 'add '(scheme-number scheme-number)
       (lambda (x y) (tag (+ x y))))
  (put 'sub '(scheme-number scheme-number)
       (lambda (x y) (tag (- x y))))
  (put 'mul '(scheme-number scheme-number)
       (lambda (x y) (tag (* x y))))
  (put 'div '(scheme-number scheme-number)
       (lambda (x y) (tag (/ x y))))
  (put 'make 'scheme-number
       (lambda (x) (tag x)))
  'done)

;;构造函数
(define (make-scheme-number n)
  ((get 'make 'scheme-number) n))

;;有理数
(define (install-rational-package)
  ;; internal procedures
  (define (numer x) (car x))
  (define (denom x) (cdr x))
  (define (make-rat n d)
    (let ((g (gcd n d)))
      (cons (/ n g) (/ d g))))
  (define (add-rat x y)
    (make-rat (+ (* (numer x) (denom y))
                 (* (numer y) (denom x)))
              (* (denom x) (denom y))))
  (define (sub-rat x y)
    (make-rat (- (* (numer x) (denom y))
                 (* (numer y) (denom x)))
              (* (denom x) (denom y))))
  (define (mul-rat x y)
    (make-rat (* (numer x) (numer y))
              (* (denom x) (denom y))))
  (define (div-rat x y)
    (make-rat (* (numer x) (denom y))
              (* (denom x) (numer y))))
  ;; interface to rest of the system
  (define (tag x) (attach-tag 'rational x))
  (put 'add '(rational rational)
       (lambda (x y) (tag (add-rat x y))))
  (put 'sub '(rational rational)
       (lambda (x y) (tag (sub-rat x y))))
  (put 'mul '(rational rational)
       (lambda (x y) (tag (mul-rat x y))))
  (put 'div '(rational rational)
       (lambda (x y) (tag (div-rat x y))))

  (put 'make 'rational
       (lambda (n d) (tag (make-rat n d))))
  'done)
(define (make-rational n d)
  ((get 'make 'rational) n d))

;;复数
(define (install-complex-package)
  ;; imported procedures from rectangular and polar packages
  (define (make-from-real-imag x y)
    ((get 'make-from-real-imag 'rectangular) x y))
  (define (make-from-mag-ang r a)
    ((get 'make-from-mag-ang 'polar) r a))
  ;; internal procedures
  (define (add-complex z1 z2)
    (make-from-real-imag (+ (real-part z1) (real-part z2))
                         (+ (imag-part z1) (imag-part z2))))
  (define (sub-complex z1 z2)
    (make-from-real-imag (- (real-part z1) (real-part z2))
                         (- (imag-part z1) (imag-part z2))))
  (define (mul-complex z1 z2)
    (make-from-mag-ang (* (magnitude z1) (magnitude z2))
                       (+ (angle z1) (angle z2))))
  (define (div-complex z1 z2)
    (make-from-mag-ang (/ (magnitude z1) (magnitude z2))
                       (- (angle z1) (angle z2))))
  ;; interface to rest of the system
  (define (tag z) (attach-tag 'complex z))
  (put 'add '(complex complex)
       (lambda (z1 z2) (tag (add-complex z1 z2))))
  (put 'sub '(complex complex)
       (lambda (z1 z2) (tag (sub-complex z1 z2))))
  (put 'mul '(complex complex)
       (lambda (z1 z2) (tag (mul-complex z1 z2))))
  (put 'div '(complex complex)
       (lambda (z1 z2) (tag (div-complex z1 z2))))
  (put 'make-from-real-imag 'complex
       (lambda (x y) (tag (make-from-real-imag x y))))
  (put 'make-from-mag-ang 'complex
       (lambda (r a) (tag (make-from-mag-ang r a))))
  'done)

;;构造函数
(define (make-complex-from-real-imag x y)
  ((get 'make-from-real-imag 'complex) x y))
(define (make-complex-from-mag-ang r a)
  ((get 'make-from-mag-ang 'complex) r a))
```

### 不同类型数据组合

以上没有定义不同类型数据之间的运算。

一种方便的解决方案是强制(coercion)。

定义强制过程
```lisp
(define (scheme-number->complex n)
  (make-complex-from-real-imag (contents n) 0))
```
安装到强制表
```lisp
(put-coercion 'scheme-number 'complex scheme-number->complex)
```

修改这个过程
```lisp
(define (apply-generic op . args)
  (let ((type-tags (map type-tag args)))
    (let ((proc (get op type-tags)))
      (if proc
          (apply proc (map contents args))
          (if (= (length args) 2)
              (let ((type1 (car type-tags))
                    (type2 (cadr type-tags))
                    (a1 (car args))
                    (a2 (cadr args)))
                (let ((t1->t2 (get-coercion type1 type2))
                      (t2->t1 (get-coercion type2 type1)))
                  (cond (t1->t2
                         (apply-generic op (t1->t2 a1) a2))
                        (t2->t1
                         (apply-generic op a1 (t2->t1 a2)))
                        (else
                         (error "No method for these types"
                                (list op type-tags))))))
              (error "No method for these types"
                     (list op type-tags)))))))
```

#### 类型的层次结构

在实际中，还存在着不同类型相互关系更全局性的结构。

![塔结构](/images/sicp_2_9.png)

这被称为塔结构。优点是简化了新类型的加入，加入时只需要知道如何连接上面的超类型，以及如何作为下面的超类型，强制转化时只需逐层上升。并且实现了继承的概念，能够继承超类型的所有操作。另一个优点是，下降它可以使它到达最简单的表达形式。

#### 层次结构的不足

遗憾的是，事情通常不是这样。一个类型有可能有多个子类型，也有可能有多个超类型。这是当时在研究的领域。

![图形的关系](/images/sicp_2_10.png)

# ch3 模块化，对象和状态

## 概述

前面讲了将过程和数据组合起来构造复合实体，在克服大型系统复杂性问题，抽象有重要作用。但是对于设计程序而言，这些手段不够用，还需要组织原则，系统化完成系统的整体设计。

基于被模拟系统的结构去设计程序的结构，是一种强有力的设计策略。且希望扩充时不必做全面修改。

以下研究2种特点鲜明的设计策略，源自于对系统结构的2种世界观。第一种关注对象，可能随时间变化。另一种关注流过系统的信息流，像信号处理系统。

## 赋值（assignment）和局部状态

对象具有随时间变化的状态，对象可能通过交互影响其它对象的状态。因为状态会改变，语言必须提供一个赋值运算符。

例子
```lisp
(define (make-account balance)
  (define (withdraw amount)
    (if (>= balance amount)
        (begin (set! balance (- balance amount))
               balance)
        "Insufficient funds"))
  (define (deposit amount)
    (set! balance (+ balance amount))
    balance)
  (define (dispatch m)
    (cond ((eq? m 'withdraw) withdraw)
          ((eq? m 'deposit) deposit)
          (else (error "Unknown request -- MAKE-ACCOUNT"
                       m))))
  dispatch)
```

### 引进赋值带来的利益
不用赋值：

x2 = (rand-update x1)

x3 = (rand-update x2)

使用赋值：
```lisp
 (define rand
  (let ((x random-init))
    (lambda ()
      (set! x (rand-update x))
      x)))
```

与所有状态都必须显式地操作和传递额外参数的方式相比，通过引入赋值和将状态隐藏在局部变量中的技术，我们能以一种更模块化的方式构造系统。

### 引进赋值的代价

一旦在语言里引进了赋值，代换就不再适合作为过程应用的模型了。

不使用赋值的程序设计叫函数式编程（functional programming）。使用赋值的程序设计叫命令式编程（imperative programming）。

引入赋值，一个变量不是一个简单的名字，而是索引一个保存值的位置，位置上的值可变。

#### 同一和变化

如果一个语言在表达式里支持“同一的东西可以相互替换”的概念，这样替换不会改变表达式的值，这个语言就称为是具有引用透明性（referentially transparent）。

在我们的计算机语言包含了赋值操作之后，也就打破了引用透明性。

#### 命令式程序设计的缺陷

命令式编程中赋值可能要有相对顺序才是正确的(若有多个并发执行的程序，复杂性会糟糕)，函数式编程不需要考虑顺序。

## 求值的环境模型（environment model）

引入赋值，一个变量不是一个简单的名字，而是索引一个保存值的位置。在新的求值模型中，位置维持在称为环境的结构中。

环境是框架的一个序列（frame）。框架是包含约束的表格，约束将一些变量名字关联于对应的值。每个框架有一个指针，指向框架的外围环境。如果框架是全局的，它将没有外围环境。一个变量相对于某个特定环境的值，是在这一环境中，包含着该变量的第一个框架里的约束值。若不存在，则说这个变量在该特定环境无约束。

如图，A、B、C、D是环境，Ⅰ、Ⅱ、Ⅲ是框架，框架中是约束。

![环境模型](/images/sicp_3_0.png)

### 求值规则

关于解释器如何求值一个组合式的问题，其整体描述仍然与第一次介绍时完全一样。
如果要对一个组合表达式求值：
1. 求值这一组合式里的各个子表达式。
2. 将运算符子表达式的值应用于运算对象子表达式的值。

现在我们要用求值的环境模型代替求值的代换模型。

#### 创建过程

一个过程是一些代码和一个指针的序对。

例如

```lisp
(define (square x)
  (* x x))
```

![环境模型](/images/sicp_3_1.png)

#### 应用过程

在将一个过程应用于一组实际参数时，将会建立起一个新环境，其中包含了将所有形式参数约束于对应的实际参数的框架，该框架的外围环境就是所用的那个过程的环境。随后就在这个新环境之下求值过程体。

```lisp
(square 5)
```

![环境模型](/images/sicp_3_2.png)

### 简单过程的应用

使用环境模型分析实例

```lisp
(define (square x)
  (* x x))
(define (sum-of-squares x y)
  (+ (square x) (square y)))
(define (f a)
  (sum-of-squares (+ a 1) (* a 2)))
```
![环境模型](/images/sicp_3_3.png)

```lisp
 (f 5)
```

![环境模型](/images/sicp_3_4.png)

### 将框架看作局部状态的仓库

一个例子

```lisp
(define (make-withdraw balance)
  (lambda (amount)
    (if (>= balance amount)
        (begin (set! balance (- balance amount))
               balance)
        "Insufficient funds")))

(define W1 (make-withdraw 100))
```
![环境模型](/images/sicp_3_5.png)

```lisp
(W1 50)
```
![环境模型](/images/sicp_3_6.png)

![环境模型](/images/sicp_3_7.png)

```lisp
(define W2 (make-withdraw 100))
```
![环境模型](/images/sicp_3_8.png)

### 内部定义

利用环境模型考察内部定义的行为

```lisp
(define (sqrt x)
  (define (good-enough? guess)
    (< (abs (- (square guess) x)) 0.001))
  (define (improve guess)
    (average guess (/ x guess)))
  (define (sqrt-iter guess)
    (if (good-enough? guess)
        guess
        (sqrt-iter (improve guess))))
  (sqrt-iter 1.0))

 (sqrt 2)
```
![环境模型](/images/sicp_3_9.png)

## 用变动数据（mutable data）做模拟

为了模拟那些由具有不断变化的状态组成的系统，我们除了需要做复合数据对象的构造和成分选择之外，还可能需要修改它们。为了模拟具有不断变化的状态的复合对象，我们将设计出与之对应的数据抽象，使其中不但包含了选择函数和构造函数，还有包含一些称为改变函数的操作，这种操作能够修改有关的数据对象。

举例，对银行系统的模拟就需要修改账户的余额。
```lisp
（set-balance！<accoun> <new-value>）
```

下面都是例子。。。

在此不列出

## 并发（concurrency）：时间是一个本质问题

具有内部状态的计算对象，存在时间问题，同一个表达式求值，却产生不同结果，因为赋值语句执行依赖于求值发生的时间。

现实中的对象是并发活动的，顺序计算机可以把它们像并发一样执行，可以使程序更加模块化，提供速度优势。

赋值使问题严重。

在处理时间和状态时，我们在计算模型领域所遭遇的复杂性，事实上，可能就是物理世界中最基本的复杂性。

### 并发系统中时间的性质

从表面上看，时间似乎是非常简单的东西。它也就是强加在各种事件上的一个顺序。

时间顺序的非确定性，可能对并发系统的设计提出严重问题。

一个例子

![时间顺序的问题](/images/sicp_3_10.png)

使事情变得更加复杂的原因，就是多个进程有可能同时试图去操作共享的状态。

#### 并发程序的正确行为

上面例子的情况非常典型，是可能潜藏在并发程序里的微妙错误。这一复杂性的根源，就在于这里出现了对不同进程之间共享的变量的赋值。对于并发进程，我们对于赋值就更需要特别小心，因为在这里可能无法控制其他进程所做赋值的出现顺序。如果几个这样的修改可能并发出现，我们就需要采用某些方式，以设法保证系统的行为是正确的。

对于并发的一种可能限制方式是规定，修改任意共享状态变量的两个操作都不允许同时发生。这是一个特别严厉的要求。这样做可能过于低效，也太保守了。

对于并发的另一种不那么严厉的限制方式是，保证并发系统产生出的结果与各个进程按照某种方式顺序运行产生出的结果完全一样。事件数量增加时，就很复杂。

### 控制并发的机制

另一种更实际的方法是，在设计并发系统时，设法做出一些一般性的机制，使我们可能限制并行进程之间的交错情况，以保证程序具有正确的行为方式。人们已经为此目的而开发了许多不同的机制。这一节里将讨论其中的一种：串行化组（serializer）。

#### 对共享变量的串行访问

串行化就是实现下面的想法：使进程可以并发地执行，但是其中也有一些过程不能并发地执行。说得更准确些，串行化就是创建一些不同的过程集合，并且保证在每个时刻，在任何一个串行化集合里至多只有一个过程的一个执行。如果某个集合里有过程正在执行，而另一进程企图执行这个集合里的任何过程时，它就必须等待到前一过程的执行结束。
我们可以借助串行化去控制对共享变量的访问。

例子

假设有一个实现串行执行的过程
```lisp
(parallel-execute <p1> <p2> ... <pk>)
```
使用make-serializer构造串行化组，串行化组以过程为参数，返回串行化的过程。对一个给定串行化组的所有调用返回的串行化过程都属于同一个集合。以实现执行过程不会交错进行。

例如
```lisp
(define x 10)

(define s (make-serializer))

(parallel-execute (s (lambda () (set! x (* x x))))
                  (s (lambda () (set! x (+ x 1)))))
```
下面是存款和取款操作已经做了串行化的make-account
```lisp
(define (make-account balance)
  (define (withdraw amount)
    (if (>= balance amount)
        (begin (set! balance (- balance amount))
               balance)
        "Insufficient funds"))
  (define (deposit amount)
    (set! balance (+ balance amount))
    balance)
  (let ((protected (make-serializer)))
    (define (dispatch m)
      (cond ((eq? m 'withdraw) (protected withdraw))
            ((eq? m 'deposit) (protected deposit))
            ((eq? m 'balance) balance)
            (else (error "Unknown request -- MAKE-ACCOUNT"
                         m))))
    dispatch))
```

#### 使用多重共享资源的复杂性

串行化提供了一种非常强有力的抽象，能帮助我们将并发程序的复杂性孤立起来，使这种程序能够被小心地和（希望是）正确地处理。然而，如果只存在一个共享资源（例如一个银行账户），串行化的使用问题是相对比较简单的。但是如果存在着多项共享资源，并发程序设计就可能变得非常难以把握了。

#### 串行化的实现

我们将用一种更基本的称为互斥元（mutex）的同步机制来实现串行化。互斥元是一种对象，假定它提供了两个操作。一个互斥元可以被获取（acquired）或者被释放（released）。一旦某个互斥元被获取，对于这一互斥元的任何其他获取操作都必须等到该互斥元被释放之后。

在我们的实现里，每个串行化组关联着一个互斥元。给了一个过程p，串行化组将返回一个过程，该过程将获取相应互斥元，而后运行p，而后释放该互斥元。这样就能保证，由这个串行化组产生的所有过程中，一次只能运行一个，这就是需要保证的串行化性质。

#### 死锁（deadlock）

现在已经看了可以如何实现串行化，但也应该看到，即使采用了上面给出的过程serialized-exchange，在账户交换问题里还存在一个麻烦。

现在设想Peter企图去交换账户al和a2，同时Paul并发地企图去交换a2和a1。假定Peter的进程到达这样一点，此时它已经进入了保护a1的串行化进程，而正好在此之后，Paul的进程也进入了保护a2的串行化进程。现在Peter已经无法继续前进了（因为无法进入保护a2的串行化进程），他需要一直等到Paul退出保护a2的串行化进程。与Peter的情况类似，Paul也无法前进了，他需要等到Peter退出保护a1的串行化进程。这样每个进程都要无穷无尽地等待下去，等着另一个进程的活动，这种情况就称为死锁。在那些提供了对于多种共享资源的并发访问的系统里，总是存在着死锁的危险。

## 流（streams）

如果用离散的步长去度量时间，那么我们就可以用一个（可能无穷的）序列去模拟一个时间函数。在这一节里，我们将看到如何用这样的序列去模拟变化，为了做到这些，我们需要引进一种称为流的新数据结构，探索对状态进行模拟的另一条途径，以避免赋值带来的问题。

### 流作为延时的表

流是一种非常巧妙的想法，使我们可能利用各种序列操作，但又不会带来将序列作为表去操作而引起的代价。利用流结构，我们能得到这两个世界里最好的东西：如此形成的程序可以像序列操作那么优雅，同时又能得到递增计算的效率。如果使用者需要访问这个流的尚未构造出的那个部分，那么这个流就会自动地继续构造下去，但是只做出足够满足当时需要的那一部分。

例如
通过序列方式去计算从10000到1000000的区间里的第二个素数，这种低效情况就表现得太明显了：

```lisp
(car (cdr (filter prime?
                  (enumerate-interval 10000 1000000))))
```

而使用流计算从10000到1000000的区间里的第二个素数
```lisp
(stream-car
 (stream-cdr
  (stream-filter prime?
                 (stream-enumerate-interval 10000 1000000))))
```

最终到这一步就返回了10009
```lisp
(cons 10009
      (delay
        (stream-filter
         prime?
         (cons 10010
               (delay
                 (stream-enumerate-interval 10011
                                            1000000))))))
```

#### delay和force的实现

delay必须包装起一个表达式，使它可以在以后根据需要去求值
```lisp
（delay <exp>）
```
实际上不过是在下面形式的外面包装起一层语法糖衣：
```lisp
（lambda（）<exp>）
```
而force也就是简单地调用由delay产生的那种（无参）过程
```lisp
（define（force delayed-object）
（delayed-object））
```
### 无穷流

例如 正整数
```lisp
(define (integers-starting-from n)
  (cons-stream n (integers-starting-from (+ n 1))))

(define integers (integers-starting-from 1))
```
#### 隐式地定义流

例如 1,1,1,...
```lisp
(define ones (cons-stream 1 ones))
```
例如 Fibonacci 数
```lisp
(define fibs
  (cons-stream 0
               (cons-stream 1
                            (add-streams (stream-cdr fibs)
                                         fibs))))
```
### 流计算模式的使用
#### 系统地将迭代操作方式表示为流过程
例如
```lisp
(define (sqrt-improve guess x)
  (average guess (/ x guess)))

(define (sqrt-stream x)
  (define guesses
    (cons-stream 1.0
                 (stream-map (lambda (guess)
                               (sqrt-improve guess x))
                             guesses)))
  guesses)
(display-stream (sqrt-stream 2))
1.
1.5
1.4166666666666665
1.4142156862745097
1.4142135623746899
...
```
#### 序对的无穷流

![序对的无穷流](/images/sicp_3_11.png)

```lisp
(stream-map (lambda (x) (list (stream-car s) x))
            (stream-cdr t))

(define (interleave s1 s2)
  (if (stream-null? s1)
      s2
      (cons-stream (stream-car s1)
                   (interleave s2 (stream-cdr s1)))))

(define (pairs s t)
  (cons-stream
   (list (stream-car s) (stream-car t))
   (interleave
    (stream-map (lambda (x) (list (stream-car s) x))
                (stream-cdr t))
    (pairs (stream-cdr s) (stream-cdr t)))))
```
#### 将流作为信号
实现一个积分器

$$S_i=C+\sum_{j=1}^{i}x_jdt$$

```lisp
(define (integral integrand initial-value dt)
  (define int
    (cons-stream initial-value
                 (add-streams (scale-stream integrand dt)
                              int)))
  int)
```
![信号处理系统](/images/sicp_3_12.png)

### 流和延迟求值

定义反馈循环的能力依赖于 delay。

#### 规范序求值


我们已经构造出了两类过程：常规的过程和要求延时参数的过程。一般说，如果创建了不同种类的过程，就将迫使我们同时去创建不同种类的高阶过程。为了避免这个问题，一种方式是让所有过程都用延时参数。我们可以采纳一种求值模型，其中所有过程参数都自动延时，只有在实际需要它们的时候才强迫参数求值。这样做，就把我们的语言转到了采用规范序的方式。

不幸的是，把延时包含到过程调用中，将会对我们设计依赖于事件顺序的程序的能力造成极大损害。

目前所有的人都知道，变动性和延时求值在程序设计语言里结合得非常不好，设计出某些方式，适当地处理这两种东西，仍然是一个很活跃的研究领域。
### 函数式程序的模块化和对象的模块化

引进赋值的主要收益就是使我们可以增强系统的模块化，把一个大系统的状态中的某些部分封装隐藏到局部变量里。流模型可以提供等价的模块化，同时又不必使用赋值。

#### 时间的函数式程序设计观点

本章开始时提出了一个目标，那就是构造出一些计算模型，使其结构能够符合我们对于试图去模拟的真实世界的看法。我们可以将这一世界模拟为一集相互分离的、受时间约束的、具有状态的相互交流的对象，或者可以将它模拟为单一的、无时间也无状态的统一体。每种观点都有其强有力的优势，但就其自身而言，又没有一种方式能够完全令人满意。我们还在等待着一个大统一的出现。


# 总结

主要通过实例和习题来理解。。。

然而毕竟是入门课程，其中涉及的很多知识点在其它地方都了解过了。。。