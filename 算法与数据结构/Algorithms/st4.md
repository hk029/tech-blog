---
title: 符号表ST（4）——二叉查找树删除 （附动画）
date: 2016-05-14
categories: 
- 算法与数据结构
- Algorithms
tags: 
- 《常用算法和数据结构》
init: 1
---


> 本系列文章主要介绍常用的算法和数据结构的知识，记录的是《Algorithms I/II》课程的内容，采用的是“算法（第4版）”这本红宝书作为学习教材的，语言是java。这本书的名气我不用多说吧？豆瓣评分9.4，我自己也认为是极好的学习算法的书籍。
通过这系列文章，可以加深对数据结构和基本算法的理解（个人认为比学校讲的清晰多了），并加深对java的理解。

我们之前介绍了[二叉查找树和它的基本操作](./st3.md)，唯独一个操作没介绍，就是删除操作，这里，我们重点介绍删除操作。

#  1 一种偷懒的方式

还记得我们曾经在[符号表的介绍](./st1.md)中说过的一种删除方式吗？

我们可以不删除，而是把需要删除的节点的value设为null（这里我们叫放“墓碑”）。key还是放那里，还是可以用来比较（但是不能做匹配操作）

![](http://img.hksite.cn/2019-03-01-065723.png)

但是如果频繁删除，符号表里会有很多“墓碑”，这不利于我们维护我们的符号表。因此我们不考虑这种方式。

#  2 删除的一般情况

由于二叉查找树的特殊结构，使得它的删除操作并没那么容易，我们还是采用递归的方式去做。很容易想到，删除操作可能会出三种情况：

##  2.1 被删除元素没有孩子

这个比较简单，直接返回null，就可以让它的父亲节点指向null，然后自己就可以等着被回收就好了。

![](http://img.hksite.cn/2019-03-01-065733.png)

##  2.2 被删除的元素有一个孩子

这个也还好，直接返回自己的另一个孩子，让自己的父亲节点指向自己的另一个孩子，自己坐等被回收。

![](http://img.hksite.cn/2019-03-01-065740.png)

> 情形1和2可以用来实现删除最小值或最大值

```java
public void deleteMin()
 {  root = deleteMin(root);  }
 private Node deleteMin(Node x)
 {
    if (x.left == null) return x.right;
    x.left = deleteMin(x.left);
    x.count = 1 + size(x.left) + size(x.right);
    return x;
 }

 public void deleteMax()
 {  root = deleteMax(root);  }
 private Node deleteMax(Node x)
 {
    if (x.right == null) return x.left;
    x.right= deleteMin(x.right);
    x.count = 1 + size(x.left) + size(x.right);
    return x;
 }
```

##  2.3 被删除的元素有两个孩子

这个是最麻烦的操作，前使用的方案是50年前提出来的Hibbard deletion（50年了，天呐）

> 找自己右孩子里面的最小值（最左）然后替换自己和它，然后删除自己

**原理：** root的右孩子肯定全大于左孩子，然后它又是右孩子里面的最小值，所以它做root节点可以满足左边比自己小，右边比自己大的条件。

![](http://img.hksite.cn/2019-03-01-065751.png)

注意：要同步更新count值

```java
public void delete(Key key)
{
    return delete(root,key);
}
private Node delete(Node x, Key key)
{
    if(x == null) return null;
    /*********找key*************/
    int cmp = key.compareTo(x.key);
    if(cmp > 0)
        x.right = delete(x.right,key);
    if(cmp < 0)
        x.left = delete(x.left,key);
    /**********找到key************/
    else{
    /**********情形1/2************/
        if(x.right == null)   
            return x.left;
        if(x.left == null)   
            return x.right;
    /**********情形3***********/
        Node min = Min(x.right);
        min.right = deleteMin(x.right);
        min.left = x.left;
        return min;
    }
    /**********更新count***********/
    x.count = 1 + size(x.right) + size(x.left);  
}
```

#  3 问题

由于删除操作的特殊性，每次找右孩子替代自己，会导致二叉树失去平衡，大量随机测试表明，通过随机的删除后的二叉树深度会退化到sqrt(N)，远大于log(N) ，这样，所有二叉树的操作效率的会大打折扣

![](http://img.hksite.cn/2019-03-01-065759.gif)

![](http://img.hksite.cn/2019-03-01-065802.png)

所以，看似简单的问题，却难有好的解决方案。 50年来二叉的树的删除仍然是一个研究方向，大量科学家在寻找一个更直观有效的删除策略。就像我们本能的觉得应该有更简单的`in place`的归并策略，但是50年过去了，仍然没人发现。

    