---
的title: 元素排序(1)——简单排序
date: 2016-05-10
categories: 
- 技术生活
- 数据结构
tags: 
- 《常用算法和数据结构》
---

> 本系列文章主要介绍常用的算法和数据结构的知识，记录的是《Algorithms I/II》课程的内容，采用的是“[算法（第4版）](https://book.douban.com/subject/19952400/)”这本红宝书作为学习教材的，语言是java。这本书的名气我不用多说吧？豆瓣评分9.4，我自己也认为是极好的学习算法的书籍。
>
> **通过这系列文章，可以加深对数据结构和基本算法的理解（个人认为比学校讲的清晰多了），并加深对java的理解。**

# 1. 排序问题

>排序问题在日常生活中非常常见，例如最简单的，学生名单，就有多种排序，名字，成绩，号码，宿舍号…… 

![](http://img.hksite.cn/2019-03-01-071840.jpg)

问题是，我们如何对多种类型进行排序？

- 答案是——回调函数。 

它的实现思路很简单： 我们调用系统的sort函数，系统根据需要通过回调对象的CompareTo函数，对特定的对象进行排序。

![](http://img.hksite.cn/2019-03-01-071707.jpg)

## 1.1 回调函数

在不同的语言中，有不同的实现回调函数的方式，其中java使用的是Interfaces 

![](http://img.hksite.cn/2019-03-01-072004.jpg)

## 1.2 Java中回调函数的路线图

![](http://img.hksite.cn/2019-03-01-071955.jpg)

## 1.3 全序关系

设集合X上有一全序关系，如果我们把这种关系用 ≤ 表述，则下列陈述对于 X 中的所有 a, b 和 c 成立： 

>- 如果 a≤b 且 b≤a 则 a=b (反对称性) 
>- 如果 a≤b 且 b≤c 则 a≤c (传递性) 
>- a≤b 或 b≤a (完全性) 
**注意：** double 的 ≤ 不是全序

## 1.4 Comparable API

### 1.4.1 实现原则

- 必须是全序
- 小于，等于，大于分别返回负数（-1）,0, 正数（1）
- 对于不兼容的类型，抛出异常 

![](http://img.hksite.cn/2019-03-01-072023.jpg)
### 1.4.2 例子



![](http://img.hksite.cn/2019-03-01-072028.jpg)

2个比较有用的排序抽象 :

- 小于
- 交换

这两个功能在排序上用处很多

![](http://img.hksite.cn/2019-03-01-072039.jpg)

# 2.选择排序

## 2.1选择排序思想（动画）

选择排序是很简单的思想： 
选择最小的和第一个数交换，第二小的的跟第二个数交换…… 

![](http://img.hksite.cn/2019-03-01-20160406224909757.gif)

## 2.2选择排序的内部循环不变性

![](http://img.hksite.cn/2019-03-01-072112.jpg)

##2.3选择排序代码
```java
public class Selection
{
   public static void sort(Comparable[] a)
   {
      int N = a.length;
      for (int i = 0; i < N; i++)
      {
         int min = i;
         for (int j = i+1; j < N; j++)
            if (less(a[j], a[min]))
               min = j;
         exch(a, i, min);
      }
   }
   private static boolean less(Comparable v, Comparable w)
   {  
/* as before */
  }
   private static void exch(Comparable[] a, int i, int j)
   {  
/* as before */
  }
}
```
# 3.插入排序

## 3.1插入排序思想（动画）

插入排序算法思想也很简单： 
每来一个数，把他插入到前面对应的位置，使得前面一直有序 
![](http://img.hksite.cn/2019-03-01-20160406225147399.gif)

## 3.2插入排序的内部循环不变性

![](http://img.hksite.cn/2019-03-01-072143.jpg)

## 3.3插入排序代码
```java
public class Insertion
{
   public static void sort(Comparable[] a)
   {
      int N = a.length;
      for (int i = 0; i < N; i++)
         for (int j = i; j > 0; j--)
            if (less(a[j], a[j-1]))
               exch(a, j, j-1);
            else break;
   }
   private static boolean less(Comparable v, Comparable w)
   {  
/* as before */
  }
   private static void exch(Comparable[] a, int i, int j)
   {  
/* as before */
  }
}
```
## 3.4 插入排序分析

插入排序最好的情况是比较N-1次0次交换，最坏的情况要N22次比较和N22次交换 

![](http://img.hksite.cn/2019-03-01-072158.jpg)

插入排序对于**部分有序的数组**效率挺高的，特别是对于小数组 


![](http://img.hksite.cn/2019-03-01-072205.jpg)

