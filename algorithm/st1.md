---
title: 符号表ST（1）——基本介绍
date: 2016-05-14
categories: 
- 技术生活
- 数据结构
tags: 
- 《常用算法和数据结构》
---

![](http://img.hksite.cn/1474640016197)

> 本系列文章主要介绍常用的算法和数据结构的知识，记录的是《Algorithms I/II》课程的内容，采用的是“算法（第4版）”这本红宝书作为学习教材的，语言是java。这本书的名气我不用多说吧？豆瓣评分9.4，我自己也认为是极好的学习算法的书籍。通过这系列文章，可以加深对数据结构和基本算法的理解（个人认为比学校讲的清晰多了），并加深对java的理解。

# **1 符号表介绍和API**

## **1.1 符号表介绍**

> 符号表（Symbol Table）是一个非常常见的数据结构，在现实生活中应用很多。它是一个“键”—“值”对应的结构。在符号表中，存储的是键值对。通过输入键，查询对应的值。

这个基础的数据结构，在现实生活中使用的特别多，比如字典。

![](http://img.hksite.cn/2019-03-01-064437.jpg)

## **1.2 符号表API**

其实符号表的操作，也无非就是增删改查之类的

| **函数名**                       | **功能**                                          |
| -------------------------------- | ------------------------------------------------- |
| **ST()**                         | 创建一个符号表对象                                |
| **void Put(Key key, Value val)** | 往集合中插入一条键值对记录，如果value为空，不添加 |
| **Value Get(Key key)**           | 根据key查找value，如果没找到返回null              |
| **void Delete(Key key)**         | 删除键为key的记录                                 |
| **boolean Contains(Key key)**    | 判断集合中是否存在键为key的记录                   |
| **boolean IsEmpty()**            | 判断查找表是否为空                                |
| **int Size()**                   | 返回集合中键值对的个数                            |
| **Iterable Keys()**              | 返回集合中所有的键                                |

Ps.有一些约定要提前说一下 - 值不为null - 如果key不存在，get()返回为null - put()会覆盖旧值

通过这几个约定，我们可以把contain和delete函数简单实现。

    public boolean contains(Key key)
    {  return get(key) != null;  }
    
    public void delete(Key key)
    {  put(key, null);  }

# **2 键和值的约定**

## **2.1 值（value）**

在java中如果我们想要实现一个符号表，我们希望它是支持所有泛型的。

## **2.2 键(Key)**

对于键的，我们希望：

- Key是可比较的，即是`Comparable`的，并且使用`compareTo()`函数
- Key是泛型的
- 能用`equals()`判断相等，能用`hashCode()`获取键（这两个函数都是java的内置函数）
- 对于Key最好是使用不可变的类型(Integer,Double, String之类的)

# **3 相等性测试**

如果我们提到了

`equals()`

函数，就不得不提提java的相等性测试，Java要求

`equals()`

满足：

- **自反性：** x.equals(x) is true
- **对称性：** x.equals(y) iff y.equals(x)
- **传递性：** if x.equals(y) and y.equals(z), then x.equals(z)
- **不为null：** x.equals(null) is false

一般来说，我们做判断使用的( x == y ) 这个式子并不做类型检查。所以，我们在实现equals()的时候，要特别注意，它看起来很简单，但是想实现完美比较麻烦。

比如一个日期的class，你可能会这样实现

`equals()`

```java
public class Date implements Comparable<Date>
{
   private final int month;
   private final int day;
   private final int year;
   ...
   public boolean equals(Date that)
   {
      if (this.day   != that.day  ) return false;
      if (this.month != that.month) return false;
      if (this.year  != that.year ) return false;
      return true;
   }
}
```

但是你如果这样实现会更好：

- 用`final`字段，不然可能会违反对称性（继承）
- 最好用`Object`,(不过这个专家们目前还在激烈争论中)
- 加入自反性，不为null的判断，并且验证类型一致性。
- 关于比较一个类中的不同变量：
    - 如果比较的是一个原始类型，用==
    - 如果比较的是一个对象，用equals()
    - 如果比较的是一个数组，对Arrays.equals(a, b)，而不是a.equals(b)
```java

    public final class Date implements Comparable<Date>
    {
       private final int month;
       private final int day;
       private final int year;
       ...
       public boolean equals(Object y)
       {
    ​      if (y == this) return true;
    ​      if (y == null) return false;
    ​      if (y.getClass() != this.getClass())
    ​         return false;
    ​      Date that = (Date) y;
    ​      if (this.day   != that.day  ) return false;
    ​      if (this.month != that.month) return false;
    ​      if (this.year  != that.year ) return false;
    ​      return true;
       }
    }
```