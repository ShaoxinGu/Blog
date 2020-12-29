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

