---
title: 知识点记录
tags: 其他
categories: 学习笔记
---

# 知识点记录

平时总是能遇到各种各样零碎的知识点，有的太小了没有写下来，有的则是太零碎了不知道怎么写下来，久而久之很多以前知道的也忘了，以前遇到过的问题又再次成为阻碍，那感觉实在难受。所以开一个笔记专门记录一些小的知识点，等有空再把知识点细化单独整理成博客。相当于作为博客的题材库。



### 1.想看源码，必须适应C语言的宏定义。

### 2.枚举常量不能用做C/C++宏判断的条件，比如下面的代码：

~~~c++
#include <iostream>
#define PLATFORM PS4
enum PLATFORM_TYPE {
	SWITCH,
	PS4,
};
int main()
{
#if PLATFORM == SWITCH		//判断语句1
	std::cout << "Nintendo!" << std::endl;
#elif PLATFORM == PS4		//判断语句2
	std::cout << "Sony!" << std::endl;
#endif
	return 0;
}
~~~

按照预期，如果#define PLATFORM SWITCH输出“Nintendo!”， 如果#define PLATFORM PS4则输出“Sony!”。

但是实际运行的结果总是“Nintendo!”，也就是判断语句1始终成立！

百思不得其解之后，我终于意识到条件编译是预编译阶段的事情，而enum的定义要等到编译阶段才会处理。也就是说在预编译阶段压根儿没有SWITCH和PS4这两个标识符。而且在 #if 之后，所有不能通过 #define 被替换为字面量的标识符和关键字都会被替换为0，因此也不会报错。所以实际上执行的判断语句是：

~~~c++
#if 0 == 0		//判断语句1
	std::cout << "Nintendo!" << std::endl;
#elif 0 == 0	//判断语句2
	std::cout << "Sony!" << std::endl;
#endif
~~~

所以才会有上面的现象。修改后逻辑终于正常了，代码如下：

~~~c++
#include <iostream>
#define PLATFORM PS4
#define SWITCH 1
#define PS4 2
int main()
{
#if PLATFORM == SWITCH		//判断语句1
	std::cout << "Nintendo!" << std::endl;
#elif PLATFORM == PS4		//判断语句2
	std::cout << "Sony!" << std::endl;
#endif
	return 0;
}
~~~

### 3.C/C++的头文件

保证同一个头文件不会被包含两次的方法有两个：

1.#ifndef

~~~c++
#ifndef __SOMEFILE_H__
#define __SOMEFILE_H__
// 头文件声明、定义语句
#endif
~~~

这种方式受C/C++语言标准支持。

优点是它不仅可以保证同一个文件不会被包含多次，也能保证内容完全相同的多个文件（或者代码片段）也不会被同时包含。

缺点是宏名冲突时会出现一系列奇怪的问题。

2.\#pragma once

~~~ c++
#pragma once
// 头文件声明、定义语句
~~~

\#pragma once 一般由编译器提供保证：同一个文件不会被包含多次。注意这里所说的“同一个文件”是指物理上的一个文件，不适用于多个相同内容的文件。

优点是写法简洁，也不必再担心宏名冲突。

缺点是在存在多个相同内容的头文件时，还是会出现多次包含的情况。以及部分老的编译器可能不支持。

### 4.垃圾回收算法

1. 引用计数法

2. Mark-Sweep法（标记清除法）：一个优点就是可以避免循环引用，当A和B两个对象可能互相指向对方时，标记可以避免无限递归。缺点是会带来内存碎片。
3. 三色标记法
4. 分代收集

