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

### 成员变量和成员方法

可以使用public、protected和private来控制访问权限

### 成员属性和索引器

关键字：get、set、return、value

成员属性示例：

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

索引器示例：

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

