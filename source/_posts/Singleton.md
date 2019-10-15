---
title: 单例模式
tags: 设计模式
categories: 学习笔记
---

# 单例模式

>  保证一个类只有一个实例，并且提供了访问该实例的全局访问点。

单例模式或许是游戏开发中最常用的设计模式了。
在开发游戏时，开发者通常会为每个子系统定义单例类。
这些单例类负责统一管理某个系统，通常被称为管理器（manager）。
例如常见的UI管理器（UIManager）、场景管理器（SceneManager）等。

[GPP（Game Programming Patterns）](https://gpp.tkchu.me/singleton.html)中以一个文件系统封装类作为例子。

经典的实现方案:
```C++
class FileSystem
{
public:
  static FileSystem& instance()
  {
    // 惰性初始化
    if (instance_ == NULL) instance_ = new FileSystem();
    return *instance_;
  }

private:
  FileSystem() {}

  static FileSystem* instance_;
};
```

线程安全的现代的实现方案：
```C++
class FileSystem
{
public:
  static FileSystem& instance()
  {
    static FileSystem *instance = new FileSystem();
    return *instance;
  }

private:
  FileSystem() {}
};
```



