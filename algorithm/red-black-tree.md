---
title: 平衡查找树(2)——红黑树（附动画）
date: 2016-05-25
categories: 
- 技术生活
- 数据结构
tags: 
- 《常用算法和数据结构》
---

![](http://img.hksite.cn/1474641956384)

> 本系列文章主要介绍常用的算法和数据结构的知识，记录的是《Algorithms I/II》课程的内容，采用的是“算法（第4版）”这本红宝书作为学习教材的，语言是java。这本书的名气我不用多说吧？豆瓣评分9.4，我自己也认为是极好的学习算法的书籍。 通过这系列文章，可以加深对数据结构和基本算法的理解（个人认为比学校讲的清晰多了），并加深对java的理解。

---

# 红黑树介绍

> 红黑树是一种简单的实现2-3树的数据结构，它方便的把我们之前实现的二叉搜索树改造成了一棵2-3树。它的核心思想是用一条左倾链（红链）作为“胶水”把二叉树的两个节点给粘起来，形成一个3节点。

![](http://img.hksite.cn/1464084199170)

把红链看成水平的，看是不是和2-3树就一样了

![](http://img.hksite.cn/1464084302728)

BST改造成红黑树有一些约定：

- 每个节点最多只有一个红链与之相连(连父亲和孩子)
- 每条从root到null的路径，都是同样的黑链数（绝对黑平衡）
- 红链都在左边

这样做的好处是什么？就是之前写的代码很多基本可以不用改就可以直接用（比如get，floor，ceiling操作，因为这些操作是不需要考虑红黑链的）

# 红黑树表示

红黑树表示只用在BST中加入一个用来表明链的颜色的变量就行了。默认空链是黑色，我们把颜色加在node节点的属性中，通过查找孩子的node节点颜色，我们就知道指向这个节点的链的颜色了。

![](http://img.hksite.cn/1464084309078)

```java
private static final boolean RED   = true;
 private static final boolean BLACK = false;
 private class Node
 {
    Key key;
    Value val;
    Node left, right;
    boolean color;   
// color of parent link
 }
 private boolean isRed(Node x)
 {
    if (x == null) return false;
    return x.color == RED;
 }
```

# 红黑树操作

## 左旋操作

这个操作用来修正因任何情况导致红链出现在右边的情况。操作主要步骤：

1. h的右孩子变成x的左孩子，h变成红色
2. x的左孩子变成h，x变成黑色
3. x作为新的子链返回

![](http://img.hksite.cn/1464084318192)

![](http://img.hksite.cn/1464084323247)

```java
public Node rotateLeft(Node h)
{
    assert isRed(h.right);
    Node x = h.right;
    h.right = x.left;   //step 1
    x.left = h;        //step 2
    x.color = h.color;
    h.color = RED;
    return x;     //setp 3
}
```

## 右旋操作

和左旋操作差不多，只是方向变了

1. h的左孩子变成x的右孩子，h变成红色

2. x的右孩子变成h，x变成黑色

3. x作为新的子链返回

```java

    public Node rotateRight(Node h)
    {
        assert isRed(h.left);
        Node x = h.left;
        h.left = x.right;   //step 1
        x.right = h;        //step 2
        x.color = h.color;
        h.color = RED;
        return x;     //setp 3
    }
```

## 颜色翻转

操作实现也比较简单，因为4节点的实现还是用的二叉树，我们只用把链的颜色改变就行了。

```java
private void flipColors(Node h)
{
    assert !isRed(h);
    assert isRed(h.left);
    assert isRed(h.right);
    h.color = RED;
    h.left.color = h.right.color = BLACK;
}
```

## **插入操作**

### **插入只有1个节点的树**

- 如果是插入左边，那么比较简单，直接是插入操作加上把节点改成红色就行了
- 如果是插入右边，那么需要做一个左旋操作。

![](http://img.hksite.cn/1464084738110)

### **插入底部的一个2-节点**

- 和第一种情况一样

    ![](http://img.hksite.cn/1464084743817)

### **插入只有2个节点的树**

1. 如果比它们都大，则插入最右边，形成4-节点，进行颜色翻转， 就行了
2. 如果比它们都小，则插入最左边，这时候中间节点有2条红链，进行右旋操作， 和1就一样了
3. 如果在它们之间，则插入左边节点的右侧，这时候，要进行左旋，就和2一样了。

![](http://img.hksite.cn/1464084748800)

### **插入底部的一个3-节点**

- 和上面一样，颜色翻转后，在逐层往上检查是不是有需要调整的地方（旋转，颜色翻转）

![](http://img.hksite.cn/1464084768514)

![](http://img.hksite.cn/1464084776157)

## **插入总结**

其实所有的插入操作，可以归结为下面三种：

1. 右红，左黑：左旋
2. 左红，左孩左红：右旋
3. 双孩子红：颜色翻转

## **插入代码**

```java
private Node put(Node h, Key key, Value val) 
 {
        /******原插入代码*******/
        if (h == null) 
            return new Node(key, val, RED);
        int cmp = key.compareTo(h.key);
        if (cmp < 0) {
            h.left = put(h.left, key, val);
        } else if (cmp > 0) {
            h.right = put(h.right, key, val);
        } else if (cmp == 0) {
            h.val = val;
        }
        h.count = 1 + size(h.left) + size(h.right);
        /******情形1*******/
        if (isRed(h.right) && !isRed(h.left)) 
            h = rotateLeft(h);
        /******情形2*******/
        if (isRed(h.left) && isRed(h.left.left))
            h = rotateRight(h);
        /******情形3*******/
        if (isRed(h.left) && isRed(h.right))
            flipColors(h);
        return h;
   }
```

# 红黑树构造动画

讲了这么多，我们来看看一棵红黑树到底如何构造出来的吧，通过通话来加深理解。

![](http://img.hksite.cn/1464085260522)

# 红黑树性能分析

所有基于红黑树的符号表实现都保证操作的运行时间为对数级别！

![](http://img.hksite.cn/1464084783174)

因为，不论插入的顺序如何，红黑树都几乎是完美平衡的

不管是在随机情况下

![](http://img.hksite.cn/1464085919633)

还是在最坏的情况下，红黑树的深度也不会超过2logN

![](http://img.hksite.cn/1464144485406)

红黑树更吸引人的一点在于，基本除了put和delete算法比较复杂，其他的代码，都可以直接使用二叉树的代码。
