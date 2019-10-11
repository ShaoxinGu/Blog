---
title: lua的面向对象
tags: Lua
categories: 学习笔记
---

# 概述
Lua中并没有严格意义上的类和对象的概念，但是可以利用Lua的metatable来模拟类和对象。
Lua的作者在PIL中推荐了[一种实现LuaOO的方案](http://www.lua.org/pil/16.html)，这种方案很简单也很容易理解。  
在lua里，类和实例都是table。在继承或实例化的时候，类会将自己设置为实例或子类的metatable。这样当实例或子类在索引不到某个属性或方法的时候，会调用metatable的__index元方法，也就是查找父类的方法。核心代码其实就这几行：
```lua
class = {}
function class:new (o)
    o = o or {}   -- create object if user does not provide one
    setmetatable(o, self)
    self.__index = self
    return o
end
```
具体的案例如下：
```lua
Base = {}
function Base:new (o)
    o = o or {}   -- create object if user does not provide one
    setmetatable(o, self)
    self.__index = self
    return o
end

function Base:Test1()
    print("Base Test1")
end

function Base:Test2()
    print("Base Test2")
end

--Child既可以说是Base类的实例，也可以说是Base类的子类
Child = Base:new()

function Child:Test1() --Child重写了Test1方法
    print("Child Test1")
end

local a = Base:new()    --生成一个Base类的实例
a:Test1()               --将打印"Base Test1"
a:Test2()               --将打印"Base Test2"

local b = Child:new()   --生成一个Child类的实例
b:Test1()               --将打印"Child Test1"
b:Test2()               --将打印"Base Test2",调用的是父类的方法
```
可以看到，这里的类和实例都是table，并没有本质上的区别。这并不影响我们的初衷——代码复用，甚至提供了其他面向对象语言所没有的灵活性，比如你可以直接给实例定义新的方法。

