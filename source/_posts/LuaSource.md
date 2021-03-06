---
title: Lua源码笔记
tags: Lua
categories: 学习笔记
---

# 概述

用Lua开发游戏已经有一段时间了，但是对Lua的理解还一直停留在浅层。最近想深入研究下，于是着手开始看Lua源码，并在本文记录知识点。

## 模块 

### GC

#### 1.基本数据结构

Lua的基本数据结构是一个类型union+type。相关的核心代码如下：

~~~c++
/*
** Common type for all collectable objects
*/
typedef struct GCObject GCObject;

/*
** Common Header for all collectable objects (in macro form, to be
** included in other objects)
*/
#define CommonHeader \
	GCObject *next;  \
	lu_byte tt;      \
	lu_byte marked

/*
** Common type has only the common header
*/
struct GCObject
{
	CommonHeader;
};

/*
** Tagged Values. This is the basic representation of values in Lua,
** an actual value plus a tag with its type.
*/

/*
** Union of all Lua values
*/
typedef union Value {
	GCObject *gc;	/* collectable objects */
	void *p;		 /* light userdata */
	int b;			 /* booleans */
	lua_CFunction f; /* light C functions */
	lua_Integer i;   /* integer numbers */
	lua_Number n;	/* float numbers */
} Value;

#define TValuefields \
	Value value_;    \
	int tt_

typedef struct lua_TValue
{
	TValuefields;
} TValue;
~~~

#### 2.GC算法和流程

1. 双色标记清除算法

   在Lua5.0中的GC，是一次性不可被打断的操作，执行的算法是Mark-and-sweep算法，在执行GC操作的时候，会设置2种颜色，黑色和白色，然后执行GC的流程，大体的伪代码流程如下:

   ~~~
   每个新创建的对象为白色
   
   //初始化阶段
   遍历root链表中的对象，并将其加入到对象链表中    
   
   //标记阶段   
   当前对象链表中还有未被扫描的元素:    
       从中取出对象并将其标记为黑色   
       遍历这个对象关联的其他所有对象: 
           标记为黑色
           
   //回收阶段
   遍历所有对象:   
       如果为白色:   
           这些对象没有被引用，则执行回收
       否则: 
           这些对象仍然被引用，需要保留
   ~~~

   整个过程是不能被打断的，这是为了避免一种情况：
   如果可以被打断，在GC的过程中新创建一个对象
   那么如果标记为白色，此时处于回收阶段，那么这个对象没有被扫描就会被回收；
   如果标记为黑色，此时处于回收阶段，那么这个对象没有被扫描就会被保留
   两种情况都不适合，所以只有让整个过程不可被打断，带来的问题就是造成gc的时候卡顿

2. 三色标记清除算法

   虽然是三色，本质是四色，颜色分为三种:

   **白色:** 当前对象为待访问状态，表示对象还未被gc标记过，也就是对象创建的初始状态； 同理，如果在gc完成后，仍然为白色，则说明当前对象没有被引用，则可以被清除回收

   **灰色:** 当前对象为待扫描状态，当前对象已经被扫描过，但是其引用的其他对象没有被扫描

   **黑色:** 当前对象已经扫描过，并且其引用的其他对象也被扫描过

   其流程伪代码:

   ~~~
   每个新创建的对象为白色
   
   //初始化阶段   
   遍历root阶段中引用的对象，从白色设置为灰色，并放入到灰色节点列表中   
   
   //标记阶段    
   当灰色链表中还有未被扫描的元素:    
       从中去除一个对象并将其标记为黑色   
       遍历这个对象关联的其他所有对象:   
           如果是白色:
               标记为灰色，并加入灰色链表中   
               
   //回收阶段  
   遍历所有对象:   
       如果为白色: 
           这些对象没有被引用，需要被回收
       否则:
           重新加入对象链表中等待下次gc   
   ~~~

   整个标记过程是可以被打断的，被打断后回来只需要接着执行标记过程即可，回收阶段是不可被打断的。

   如何解决在标记阶段之后创建的对象为白色的问题?
   分裂白色为两种白色，一种为当前白色 currentwhite， 一种为非当前白色 otherwhite，新创建的对象都为otherwhite，则在执行回收的时候，如果为otherwhite则不执行回收操作，等待下次gc的时候，会执行白色的轮换，则新创建的对象会进入下一轮GC。



