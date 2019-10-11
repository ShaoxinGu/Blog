---
title: Lua实现面向对象
tags: Lua
categories: 学习笔记
---

# Lua实现面向对象

## 概述

以下描述出自《Program in Lua》中文版：

>​		一些面向对象的语言中提供了类的概念，作为创建对象的模板。在这些语言里，对象是类的实例。Lua 不存在类的概念，每个对象定义他自己的行为并拥有自己的形状（shape）。然而，依据基于原型（prototype）的语言比如 Self 和 NewtonScript，在 Lua中仿效类的概念并不难。在这些语言中，对象没有类。相反，每个对象都有一个 prototype（原型），当调用不属于对象的某些操作时，会最先会到 prototype 中查找这些操作。在这类语言中实现类（class）的机制，我们创建一个对象，作为其它对象的原型即可（原型对象为类，其它对象为类的 instance）。类与 prototype 的工作机制相同，都是定义了特定对象的行为。



## 方案一

Lua的作者之一[Roberto Lerusalimschy](https://en.wikipedia.org/wiki/Roberto_Ierusalimschy)在《Program in Lua》书中推荐了[一种实现LuaOO的方案](http://www.lua.org/pil/16.html)，这种方案很简单也很容易理解。

在Lua里，类和实例都是table。在继承或实例化的时候，类会被赋值给实例或子类metatable的__index。

核心代码其实就这几行：

```lua
Class = {}
function Class:new (o)
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

--Child既可以说是Base类的实例，也可以说是Base类的子类，因为它们本质上都是table对象
Child = Base:new()

function Child:Test1() --Child类重写了Test1方法
    print("Child Test1")
end

local a = Base:new()    --生成一个Base类的实例
a:Test1()               --将打印"Base Test1"
a:Test2()               --将打印"Base Test2"

local b = Child:new()   --生成一个Child类的实例
b:Test1()               --将打印"Child Test1"
b:Test2()               --将打印"Base Test2",调用的是父类的方法
```


## 方案二

我在工作中接触到的算是方案一的升级版。

核心代码如下：

```lua
Base = {__ClassType = "Base"}
ALL_CLASS = ALL_CLASS or {}

function Base:New(...)
    local o = {}
    setmetatable(o, {__index = self})
    if o.Ctor then o:Ctor(...) end -- 调用构造函数
    return o
end

function Base:Inherit(ClassName, o)
    o = o or {}
    o.mt = { __index = o}
    assert(ClassName, "必须要有类名")
    assert(not (ClassName and ALL_CLASS[ClassName]), string.format("该类已存在： %s", ClassName))

    o.__ClassType = ClassName
    ALL_CLASS[ClassName] = true

    o.__InheritMap = {[self:GetType()] = true }  -- 记录继承类型
    o.__InheritSelf = false
    if self.__InheritMap then
        for UpType, _ in pairs(self.__InheritMap) do
            o.__InheritMap[UpType] = true
        end
    end

    setmetatable(o, {__index = self})
    return o
end

function Base:IsSubObj(ObjType)
    return self:GetTypeMap()[ObjType]
end

function Base:GetTypeMap()
    local clsSelf = getmetatable(self)
    if clsSelf then
        local Temp = clsSelf.__index
        if not Temp.__InheritSelf then
            Temp.__InheritMap[Temp:GetType()] = true
            Temp.__InheritSelf = true
        end
        return Temp.__InheritMap
    end
    return {}
end

function Base:Ctor() end
function Base:InClass() return true end
function Base:OnCreate() end
function Base:OnDestroy() end
function Base:GetType()	return self.__ClassType end
```

与之配套的方法：

```Lua
-- 获取一个类的父类 & 获取一个实例的构造类
function Super(Class)
    return getmetatable(Class).__index
end

-- 判断一个类是否是类的子类 & 判断一个对象否是另一个类的实例
function IsSub(Obj, Base)
    local Temp = Obj
    while  1 do     --循环回溯metatable
        local mt = getmetatable(Temp)
        if mt then
            Temp = mt.__index
            if Temp == Base then
                return true
            end
        else
            return false
        end
    end
end
```

