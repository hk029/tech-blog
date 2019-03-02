---
title: 优先队列(3)——堆排序
date: 2016-04-29
categories: 
- 技术生活
- 数据结构
tags: 
- 《常用算法和数据结构》
---

# **堆排序**
---
> 本系列文章主要介绍常用的算法和数据结构的知识，记录的是《Algorithms I/II》课程的内容，采用的是“[算法（第4版）](https://book.douban.com/subject/19952400/)”这本红宝书作为学习教材的，语言是java。这本书的名气我不用多说吧？豆瓣评分9.4，我自己也认为是极好的学习算法的书籍。
> 
> **通过这系列文章，可以加深对数据结构和基本算法的理解（个人认为比学校讲的清晰多了），并加深对java的理解。**


##**1 堆排序思想**
堆排序的思想很简单，就是利用了堆的优点，不断的找最大值，然后删除最大值，重复操作。

- 首先，要构造一个二叉堆出来，我们采用自底向上的思想。

![](http://img.hksite.cn/2019-03-01-084744.jpg)

- 做删除root的操作（但是不是真删除，只是把root元素放到最后了）

![](http://img.hksite.cn/2019-03-01-084752.jpg)

- 循环直到数组有序。

我们可以发现加入的代码非常少！！

##**2 堆排序代码**
```java
public class Heap
{
   public static void sort(Comparable[] a)
   {
      int N = a.length;
      for (int k = N/2; k >= 1; k--)
         sink(a, k, N);
      while (N > 1)
      {
         exch(a, 1, N);
         sink(a, 1, --N);
      }
   }
   private static void sink(Comparable[] a, int k, int N)
   {  
/* as before */
  }
   private static boolean less(Comparable[] a, int i, int j)
   {  
/* as before */
  }
   private static void exch(Comparable[] a, int i, int j)
   {  
/* as before */
  }
}
```
**注意:** 我们之前操作堆都是从1开始的，但是排序的时候，需要排序的元素是从0开始的，我们需要在交换的时候，做一些变化（减1）。

##**3 堆排序的性能**
> 堆排序是我们目前接触的**第一个** `in-place` 的并且时间复杂度为NlogN的算法（快排最坏情况下会有$N^2$的复杂度）

![](http://img.hksite.cn/2019-03-01-084805.jpg)

但是它有一些缺点，导致实际用的不多：
- 内部循环代码过多（多次比较）
- 很少能用到cache，因为它的比较是跳跃的，用不到附近的元素。（快排是跟附近的元素比较）
- 不稳定