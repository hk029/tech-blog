---
title: 优先队列(2)——二叉堆
date: 2016-04-29
categories: 
- 算法与数据结构
- Algorithms
tags: 
- 《常用算法和数据结构》
init: 1
---


# 二叉堆

> 本系列文章主要介绍常用的算法和数据结构的知识，记录的是《Algorithms I/II》课程的内容，采用的是“[算法（第4版）](https://book.douban.com/subject/19952400/)”这本红宝书作为学习教材的，语言是java。这本书的名气我不用多说吧？豆瓣评分9.4，我自己也认为是极好的学习算法的书籍。
>
> **通过这系列文章，可以加深对数据结构和基本算法的理解（个人认为比学校讲的清晰多了），并加深对java的理解。**

之前我们已经介绍了[优先队列](http://blog.csdn.net/hk2291976/article/details/51277753)的基本内容，现在来看看用二叉堆实现优先队列吧。

## 1 二叉堆的定义

堆是一个完全二叉树结构（除了最底下一层，其他层全是完全平衡的），如果每个结点都大于它的两个孩子，那么这个堆是有序的。

> 二叉堆是一组能够用堆有序的完全二叉树排序的元素，并在数组中按照层级存储（不用数组的第一个位置）

![](http://img.hksite.cn/2019-03-01-084429.jpg)

## 2 二叉堆的性质	
- 最大的元素在a[1] (root结点)
- 每个k的父亲在k/2
- 每个k的孩子在k*2和k*2+1

## 3 二叉堆的操作

### 3.1 上浮（孩子大于父亲）——对应插入操作

循环，每次比较自己和父亲，如果比父亲大就交换，直到root。

![](http://img.hksite.cn/2019-03-01-084445.jpg)

### 3.2 插入

先把元素加入数组的最后一个位置，然后进行上浮到正确位置

![](http://img.hksite.cn/2019-03-01-084510.jpg)

### 3.3 下沉（父亲小于儿子）——对应删除操作

循环，每次比较自己和两个孩子，如果比孩子小就交换，如果比两个孩子都小，就交换到两个里面较大的一个。直到叶子。

![](http://img.hksite.cn/2019-03-01-084520.jpg)


### 3.4 删除最大元素

<font color="red">先把根元素与最后一个元素交换</font>，删除最后一个元素，然后从根开始下沉到正确位置。

![](http://img.hksite.cn/2019-03-01-084527.jpg)

## 4  二叉堆优先队列代码

```java
/**
 *
 * @author rocky
 */
public class MaxPQ<Key extends Comparable<Key>> {

    private Key[] pq;
    private int N;

    public MaxPQ(int capacity) {
        pq = (Key[]) new Comparable[capacity + 1];
    }

    public boolean isEmpty() {
        return N == 0;
    }

    public void insert(Key key)
    {      
        N++; 
        pq[N] = key;
        swim(N);
    }

    public Key delMax() {
        Key max = pq[1];  //get the max element
        exch(1, N);     //exchange between the root and the last element
        N--;   
        sink(1);   //sink to the right place
        pq[N+1] = null;   //delete
        return max;
    }

    private void swim(int k)
    {
        while(k > 1 && less(k/2, k))
        {
            exch(k, k/2);
            k /= 2;
        }
        
    }

    private void sink(int k) {
        while(k*2 <= N)    //if this node has left child 
        {
            int child = k * 2;   
            if (child < N && less(child, child + 1)) {  //if the left child is less than the right child
                child = child + 1;   //change child to the right child
            }
            if (less(k, child)) {
                exch(k, child);
            }
            k = child;
        }
    }

    private boolean less(int i, int j) {
        return pq[i].compareTo(pq[j]) < 0;
    }

    private void exch(int i, int j) {
        Key t = pq[i];
        pq[i] = pq[j];
        pq[j] = t;
    }
}

```

##  5 二叉堆扩展

![](http://img.hksite.cn/2019-03-01-084559.jpg)

###  5.1 不可变性

我们算法的前提是用户不会改变队列的元素，如果用户能改变队列的元素，那么队列成立的条件就会破坏。
不可变的数据类型，就是说一个实例一旦建立，就不可以改变。java里很多类型都是不可变的。

![](http://img.hksite.cn/2019-03-01-084607.jpg)

这有很多好处，但是坏处就是如果你需要改变值必须要新建一个对象。

![](http://img.hksite.cn/2019-03-01-084622.jpg)
    