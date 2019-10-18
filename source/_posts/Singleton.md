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

## 描述

- 保证一个类只有一个实例：有时候，如果类存在多个实例就不能正确的运行。这种类的特点是，对这些类进行调用的时候必须接触之前所有的操作。如果这些操作的发生在不同的实例，一个实例就无法知道另一个实例的操作，也就无法正常地运行了。
- 提供了访问该实例的全局访问点： 有时候，游戏中的不同系统都会使用同一个类。 如果这些系统不能创建这个类的实例，那这个类就需要提供获取它实例的全局方法。

## 实现

单例模式保证一个类只有一个实例，并且提供了该实例的全局访问点。

[GPP（Game Programming Patterns）](https://gpp.tkchu.me/singleton.html)中以一个文件系统封装类作为例子。

经典的C++实现方案:
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

静态的`instance_`成员保存了一个类的实例， 私有的构造器保证了它是**唯一**的。 公开的静态方法`instance()`让任何地方的代码都能访问实例。 在首次被请求时，它同样负责惰性实例化该单例。 

线程安全的现代C++实现方案：

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

## 为什么我们使用它

-  **如果没人用，就不必创建实例。** 这种惰性初始化为节约内存和CPU资源带来了好处。

-  **它在运行时实例化。** 单例通常的替代方案是使用含有静态成员变量的类，但是静态成员是自动初始化的。编译器在main()运行前初始化静态变量，而且初始化的顺序不可控，这意味着它们的相互依赖不可靠。而单例模式的惰性初始化没有这个问题。

-  **可继承单例。**  单例可继承。这意味着单例模式可以配合多态性写出优美的代码。核心代码如下：

  ```lua
  class FileSystem
  {
  public:
      static FileSystem& instance();
  
      virtual ~FileSystem() {}
      virtual char* readFile(char* path) = 0;
      virtual void  writeFile(char* path, char* contents) = 0;
  
  protected:
      FileSystem() {}
  };
  
  FileSystem& FileSystem::instance()
  {
  #if PLATFORM == PLAYSTATION3
  	static FileSystem *instance = new PS3FileSystem();
  #elif PLATFORM == WII
  	static FileSystem *instance = new WiiFileSystem();
  #endif
  	return *instance;
  }
  ```

   通过一个简单的编译器转换，我们把文件系统包装类绑定到合适的具体类型上。 整个代码库都可以使用`FileSystem::instance()`接触到文件系统，而无需和任何平台相关的代码耦合。耦合发生在为特定平台写的`FileSystem`类实现文件中。 

## 为什么我们后悔使用它

- **它是一个全局变量**

  - 让理解更加困难：全局变量的使用让我们除了关心当前函数之外，还需要关心全局变量的状态。而由于全局变量的全局访问性，我们可能要追踪整个代码库才能找到某个静态变量在什么地方被赋予了错误的值。
  - 促进了耦合的发生：全局变量具有全局可见性，新手程序员可以会在单例中#include 包含了其他模块的头文件。新手程序员用这种方法顺利地完成了任务，却破坏了框架留下了耦合。如果不用全局实例，那他这样做的时候将会遇到阻碍，这种阻碍会提醒他不该这样做。 通过控制对实例的访问，你控制了耦合。
  - 对并行不友好： 当我们将某些东西转为全局变量时，我们创建了一块每个线程都能看到并访问的内存， 却不知道其他线程是否正在使用那块内存。 这种方式带来了死锁，竞争状态，以及其他很难解决的线程同步问题。

- **它能在你只有一个问题的时候解决两个**

  正如之前的描述里写的，单例模式解决了两个问题。如果我们只有其中一个问题呢？ 保证实例是唯一存在的是很有用的，但是谁告诉我们要让每个人都能接触到它？ 同样，全局接触很方便，但是必须禁止存在多个实例吗？这是一个奇怪的约束，比如我们为了便利的访问把一个类改成了单例，却发现这个类无法创建不同的实例了。

- **惰性初始化从你那里剥夺了控制权**

  - 如果初始化很耗时，而初始化发生在游戏的高潮部分，会导致可见的掉帧和断续的游戏体验 。
  -  游戏通常需要严格管理在堆上分配的内存来避免碎片。如果系统在初始化时分配到了堆上。我们需要知道初始化在何时发生， 这样我们才可以控制内存待在堆的哪里。  

## 那该如何是好

- **看看你是不是真正地需要类**

  有些时候我们并不需要管理器来管理某些对象，而是让这些对象管理自己。

- **将类限制为单一的实例**

  我们可以通过别的方法保证一个类只有单一实例，比如用一个bool值记录，而无需提供全局的接触点。

- **为了给实例提供方便的访问方法**

  - 传进来：在用其他更加繁杂的方法前，考虑一下这个解决方案。 

  - 从基类中获得：很多游戏架构有浅层但是宽泛的继承层次，通常只有一层深。举个例子，你也许有`GameObject`基类，每个游戏中的敌人或者对象都继承它。使用这样的架构，很大一部分游戏代码会存在于这些“子”推导类中。这就意味着这些类已经有了对同样事物的相同获取方法：它们的`GameObject`基类。我们可以利用这点： 

    ```c++
    class GameObject
    {
    protected:
        Log& getLog() { return log_; }
    
    private:
        static Log& log_;
    };
    
    class Enemy : public GameObject
    {
        void doSomething()
        {
            getLog().write("I can log!");
    	}
    };
    ```

  - 从已经是全局的东西中获取： 大多数代码库仍有一些全局可用对象，比如一个代表了整个游戏状态的`Game`或`World`对象。我们可以让现有的全局对象捎带需要的东西，来减少全局变量类的数目。不让`Log`，`FileSystem`和`AudioPlayer`都变成单例，而是这样做：

    ```c++
    class Game
    {
    public:
        static Game& instance() { return instance_; }
    
        // 设置log_, et. al. ……
    
        Log&         getLog()         { return *log_; }
        FileSystem&  getFileSystem()  { return *fileSystem_; }
        AudioPlayer& getAudioPlayer() { return *audioPlayer_; }
    
    private:
        static Game instance_;
        
        Log         *log_;
        FileSystem  *fileSystem_;
        AudioPlayer *audioPlayer_;
    };
    ```

  - 从服务定位器中获得： 定义一个类，存在的唯一目标就是为对象提供全局访问。 这种常见的模式被称为服务器定位模式，[有单独讲它的章节]( https://gpp.tkchu.me/service-locator.html )。

## Lua中的实现

```lua
Singleton = {}
function Singleton:new(o)
    o = o or {}
    setmetatable(o,self)
    self.__index = self
    return o
end
 
function Singleton:Instance()
	if not self.instance then
		self.instance = self:new()
	end
	return self.instance
end

s1 = Singleton:Instance()
s2 = Singleton:Instance()
if s1 == s2 then  
    print("两个对象是相同的实例")  
end
```
