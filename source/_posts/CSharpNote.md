---
title: C#学习笔记
tags: C#
categories: 学习笔记
---

# 基础

## 代码结构

```c#
using System; //引入命名空间（引用已有工具包）

namespace Test //定义函数命名空间（工具包）
{
	class Program //定义类（工具）
    {
        static void Main(string[] args) //定义函数（工具的使用方法）
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```

## 数据类型

### 整形

| 数据类型 | 别名 | 字节数 | 位数 | 允许的值 |
| :------: | :------: |:------: |:------: | :------: |
| sbyte    | System.Sbyte | 1      | 8    | -127~128 |
| byte | System.Byte | 1 | 8 | 0~255 |
| short | System.Int16 | 2 | 16 | -32768~32767 |
| ushort | System.UInt16 | 2 | 16 | 0~65535 |
| int | System.Int32 | 4 | 32 | -2147483648~2147483647 |
| int | System.UInt32 | 4 | 32 | 0~4294967295 |
| long | System.Int64 | 8 | 64 | -9223372036854775808~9223372036854775807 |
| ulong | System.UInt64 | 8 | 64 | 0~18446744073709551615 |

### 浮点型

| 数据类型 |      别名      | 字节数 | 位数 |  标识   |
| :------: | :------------: | :----: | :--: | :-----: |
|  float   | System.Single  |   4    |  32  |    f    |
|  double  | System.Double  |   8    |  64  | d(默认) |
| decimal  | System.Decimal |   16   | 128  |    m    |

### 简单类型

| 数据类型 | 别名           | 字节数 | 位数 | 允许的值            |
| :------: | :------: | :------: |:------: | :------:|
| char     | System.Char    | **2**  | **16** | 一个Unicode字符     |
| bool     | System.Boolean | 1      | 8    | 布尔值：true或false |
| string   | System.String  | ---    | ---  | 一组字符            |

### 类型转换

隐式转换：变量转换为范围更大、精度更高的类型可自动完成，称为隐式转换

显式转换：变量转换为范围更小、精度更低的类型可能会出现异常、损失精度，因此需要手动转换，称为显示转换。

#### 显式转换的四种方式

1. (类型)变量
2. 类型.Parse(字符串)
3. Convert.ToInt32(变量)
4. 变量.ToString()

## 值类型和引用类型

### 值类型

整型、浮点型、char、bool、结构体，值类型的默认值可以用关键字default(类型)查看

存储在栈内存，赋值时直接拷贝数据

### 引用类型

数组、string、类

存储在堆内存，赋值时拷贝内存地址

ps：string频繁创建时会制止大量内存垃圾

## 函数

函数的参数变量默认是拷贝的。相当于创建一个新的局部变量并赋值，对参数的修改不会影响外部变量。

### ref和out

借助ref或out修饰符，可以传入外部变量的引用。

ref和out的区别：

ref传入的变量必须先初始化，但是在函数内可以不赋值。

out传入的变量可以不初始化，但是在函数内必须被赋值。

### 变长参数

借助params修饰符，可以传入变长参数。params可以修饰任意类型的一维数组参数。

params修饰的参数可以接收0或任意个参数。

参数列表里最多只能有一个params关键字，且必须是最后一个参数。

### 可选参数

参数可以有默认值，在没传入参数时取默认值。有默认值的参数称为可选参数。

参数列表里可以有多个可选参数。但是可选参数必须跟在普通参数后面。

### 函数重载

在同一个语句块内，可以定义多个同名但是**参数列表不同**的函数。

## 类和对象



### 成员

- 成员变量和成员方法统称为类的成员。
- 可以使用public、protected和private三种访问修饰符来控制成员的访问权限

### 成员属性和索引器

#### 成员属性

用于封装保护成员变量。

关键字：get、set、return、value

示例：

```c#
class Person
{
  private string name;
  public string Name
  {
    get
    {
      return name;
    }
    set
    {
      name = value;
    }
  }
  public int age
  {
    get;
    set;
  }
}
```

#### 索引器

用于实现对象的下标访问操作。

示例：

```c#
class Group
{
  private int[] array;
  private int[,] table;
  public int this[int index]
  {
    get
    {
      return array[index];
    }
    set
    {
      array[index] = value;
    }
  }
  //重载
  public int this[int x, int y]
  {
    get
    {
      return table[x][y];
    }
    set
    {
      array[x][y] = value;
    }
  }
}
```

### 静态相关（static）

- 静态成员与非静态成员。

  - 用static修饰的成员为静态成员，否则为非静态成员。
  - 静态成员存储在静态存储区，不参与垃圾回收，生命周期为整个程序运行期间。通过**类名.静态成员**的方式直接访问。
  - 非静态成员存储在动态存储区，参与垃圾回收，生命周期由构造函数和析构函数控制。需要创建实例，通过**实例.成员**访问。

  - 另外用const关键字修饰的成员变量称为常量，具有静态属性，但不分配内存，而是在编译时直接替换。因此必须被初始化。

- 静态类与非静态类

  - 用static关键字修饰的类为静态类，否则为普通类。
  - 静态类只能包含静态成员。
  - 静态类不能被实例化，通常作为工具类使用。

- 静态构造函数

  - 用static修饰的构造函数称为静态构造函数，否则为普通构造函数。
  - 会在类第一次被使用的时候自动执行一次，一般用于对静态变量的初始化。
  - 静态构造函数不能带参数。
  - 静态构造函数不构成对类普通构造函数的重载。

### 拓展方法

- 只能定义为静态类中的静态方法。
- 用于给已有的类型拓展方法。

示例：

```c#
static class Tools
{
  public static void PrintNum(this int value)
    Console.WriteLine(value.ToString());
}
int a = 10;
a.PrintNum();
```

### 运算符重载

语法：public static 返回值 operator 运算符(参数列表)

示例：

```c#
class Point
{
  public int x;
  public int y;
  Point(int x, int y)
  {
    this.x = x;
    this.y = y;
  }
  public void Print()
  {
    Console.WriteLine("x = " + x + ", y = " + y);
  }
  public static Point operator +(Point p1, Point p2)
  {
    Point newPoint = new Point();
    newPoint.x = p1.x + p2.x;
    newPoint.y = p1.y + p2.y;
    return newPoint;
  }
  public static Point operator +(Point p, int num)
  {
    Point newPoint = new Point();
    newPoint.x = p.x + num;
    newPoint.y = p.y + num;
    return newPoint;
  }
  public static Point operator +(int num, Point p)
  {
    Point newPoint = new Point();
    newPoint.x = p.x + num;
    newPoint.y = p.y + num;
    return newPoint;
  }
}
Point p = new Point(1, 1);
Point p2 = new Point(2, 2);
Point p3 = p + p2;
p3.Print();
```

- 一定是一个public的静态方法
- 可重载的运算符：所有的算数运算符、逻辑非!、所有的位运算符、所有的条件运算符（必须成对实现）
- 不可重载的运算符：逻辑或||、逻辑与&&、索引符[]（由索引器实现）、强转运算符()、访问运算符.、三目运算符?、赋值运算符=
- 同一个运算符可多次重载
- 重载方法不可使用ref、out运算符

### 内部类和分部类

出于可读性考虑，一般不使用。

#### 内部类

在一个类的内部定义的类称为内部类。

```c#
class Person
{
  int age;
  string name;
  public Body body;
  public class Body
  {
    Arm leftArm;
    Arm rightArm;
    class Arm
    {
      
    }
  }
}
```

#### 分部类

关键字：partial

示例：

```c#
partial class Student
{
  public string name;
  partial void Test();
}
partial class Student
{
  public void Speak(string str)
  {
    
  }
  
  partial void Test()
  {
    //分部方法的实现
  }
}
```

