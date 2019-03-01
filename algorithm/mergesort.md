---
title: 元素排序(5)——归并排序
date: 2016-05-09
categories: 
- 技术生活
- 数据结构
tags: 
- 《常用算法和数据结构》
---

> 本系列文章主要介绍常用的算法和数据结构的知识，记录的是《Algorithms I/II》课程的内容，采用的是“算法（第4版）”这本红宝书作为学习教材的，语言是java。这本书的名气我不用多说吧？豆瓣评分9.4，我自己也认为是极好的学习算法的书籍。
通过这系列文章，可以加深对数据结构和基本算法的理解（个人认为比学校讲的清晰多了），并加深对java的理解。

# 1.归并排序介绍

## 1介绍

![](http://img.hksite.cn/1473422339157)

### 1.2归并排序步骤：

> 它的思想就是简单的分治（D&C）。

- Divide : 分（把数组成2部分）
- 循环分（直到不能分）
- Conquer : 治（合并，将每2个部分合到一起）

分很简单，其中最关键的部分就是如何合并(Merge)，这也是这个算法的来历。
分两种情况讨论： 1. 当数组元素为1的时候，很简单，小的放前，大的放后。 2. 当数组元素大与2的时候，我们可以用一个新的数组和2个指针快速解决这个问题：

- 复制到新数组，指针i,j分别指向2个部分的开头

![](http://img.hksite.cn/1473422352881)

- 如果aux[i] < aux[j] 则把aux[i]的元素放到a[k]，然后i 和 k向后移动，反之同理，直到遍历完所有元素。

![](http://img.hksite.cn/1473422366674)

可以发现，每次Merge的时间复杂度是O(n)，加上一共合并log2N
次，可以说是非常不错。

## 1.3 归并排序代码

```java
private static void merge(Comparable[] a, Comparable[] aux, int lo, int mid, int hi)
{
   assert isSorted(a, lo, mid);    // precondition: a[lo..mid]   sorted
   assert isSorted(a, mid+1, hi);  // precondition: a[mid+1..hi] sorted
   for (int k = lo; k <= hi; k++)
      aux[k] = a[k];
   int i = lo, j = mid+1;
   for (int k = lo; k <= hi; k++) 
   {
      if (i > mid)              
          a[k] = aux[j++];
      else if (j > hi)               
          a[k] = aux[i++];
      else if (less(aux[j], aux[i])) 
          a[k] = aux[j++];
      else                           
          a[k] = aux[i++];
   }
   assert isSorted(a, lo, hi);     
// postcondition: a[lo..hi] sorted
}
```

上面3处assert的好处是： - 帮助发现逻辑上的错误 - 可以说明代码是做什么用的

![](http://img.hksite.cn/1473422732796)

```java
public class Merge
{
   private static void merge(...)
   {  
/* as before */
  }
   private static void sort(Comparable[] a, Comparable[] aux, int lo, int hi)
   {
      if (hi <= lo) return;
      int mid = lo + (hi - lo) / 2;
      sort(a, aux, lo, mid);
      sort(a, aux, mid+1, hi);
      merge(a, aux, lo, mid, hi);
   }
   public static void sort(Comparable[] a)
   {
      aux = new Comparable[a.length];
      sort(a, aux, 0, a.length - 1);
   }
}
```

注意： 1. 上面两处sort，一个是提供对外的接口，一个是对内的递归调用使用的。 2. 在对外接口中创建aux数组，而不要在内部调用的sort中创建aux数组，否则会出现bug。

## 1.4 实际运行步骤：

![](http://img.hksite.cn/1473422724077)

## 1.5 算法性能：

### 1.5.1 比较次数和数组访问次数

![](http://img.hksite.cn/1473422696570)

### 1.5.2 运行时间

![](http://img.hksite.cn/1473422690905)

### 1.5.3 内存占用

![](http://img.hksite.cn/1473422656837)

## 1.6 改进

> 归并排序的速度很快，唯一的不足就是内存占用很大（目前有可以不用额外空间的归并排序，这里不涉及）特别是小子串的开销很大，有一些改进的方案，可以减少对内存的占用。

### 1.6.1 对小子串使用插入排序 (可以提升20%左右) ：（设定一个Cutoff， 一般是7个元素）

```java
private static void sort(Comparable[] a, Comparable[] aux, int lo, int hi)
{
   if (hi <= lo + CUTOFF - 1)
   {
      Insertion.sort(a, lo, hi);
      return;
   }
   int mid = lo + (hi - lo) / 2;
   sort (a, aux, lo, mid);
   sort (a, aux, mid+1, hi);
   merge(a, aux, lo, mid, hi);
}
```

### 1.6.2 对已经排序好的2个子串直接跳过Merge阶段。（对部分有序的数组有用）

![](http://img.hksite.cn/1473422648359)

### 1.6.3 不用全复制（节约时间但是不节约空间）

把aux和a的位置交换，每次只用在Merge的时候从一个数组移动到另一个数组就行了，减少了复制的过程。

![](http://img.hksite.cn/1473422642285)

# 2.Bottom-up merge sort

之前讲的mergesort是一个递归版本，这个是一个非递归的版本。 思想也很简单，就是依次对每隔1,2,4,8的子串进行merge。 比如： 第一次是[0]+[1] [2]+[3] [3]+[4] …… 第二次是[0-1] + [2-3] …… 第三次是[0-3] + [4-7] …… 直到排序完毕

![](http://img.hksite.cn/1473422632623)

# 3.Comparator接口

> 如果你需要对一个对象的多个键值进行排序（比如一首歌的歌名，作者，日期等），可以考虑用Comparator。

在class中间，可以申明几个Comparator接口，并实现比较函数

![](http://img.hksite.cn/1473422614628)

然后在使用的时候，改sort函数，把Comparator作为一个参数传入（注意要更改之前的数组的变量类型为object）

![](http://img.hksite.cn/1473422606287)

使用的时候，加入比较参数就行了

![](http://img.hksite.cn/1473422596525)

# 排序算法稳定性

> 一个排序算法还有一个衡量指标就是，它是否是稳定的，稳定的如何衡量呢？就是对于有同样排序等级的元素B1B2，原本B1在前面的，结果排序后它到后面去了变成B2B1了。这就是不稳定的。在现实生活中，这个性质还是很重要的。

比如，我们已经按名字排好序的一个名单，我们按第二项排序，我们期望的是第二项相同的情况下，名字在前面的依然在前面，结果，发现并不是这样，这就是不稳定排序。

![](http://img.hksite.cn/1473422439850)

很容易知道，我们之前学的算法，插入排序是稳定的，因为它每当比较到一个相同的元素时，就停止了，不会继续比较了。

![](http://img.hksite.cn/1473422429621)

插入排序是不稳定的，因为涉及到长距离的交换

![](http://img.hksite.cn/1473422423610)

同理，希尔排序也是不稳定的

![](http://img.hksite.cn/1473422412476)

归并排序是稳定的，因为我们在编程的时候可以规定。

![](http://img.hksite.cn/1473422407058)
