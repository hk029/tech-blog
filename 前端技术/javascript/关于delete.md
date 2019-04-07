---
title: 关于delete
date: 2018-10-29
categories: 
- 前端技术
- javascript
tags: 
- javascript
init: 1
---

源于作者在阅读，斯托亚诺夫的《面向对象的Javascript》，这本书《函数》一章，中发现的问题。文中说

> function is treated as a normal variable—it can be copied to a different variable and even deleted.

并且举例证明了它的理论：

```
  >>> var sum = function(a, b) {return a + b;}
  >>> var add = sum;
  >>> delete sum
  true
  >>> typeof sum;
  "undefined"
```
但是问题是，这里`delete`不应该成功。你在Chrome上测试也不会成功，但是Firefox上确实可以这么干（所以作者估计也是在FireFox上做了一个简单的测试）
![20180918153723932560721.png](http://hksite.cn/20180918153723932560721.png)

由此，引入了一个很有意思的话题：delete到底是如何工作的？

## 理论

> `delete` 方法只有在删除对象的属性，才会返回`true`

```
  var o = { x: 1 };
  delete o.x; // true
  o.x; // undefined
```

在删除变量

```
  var x = 1;
  delete x; // false
  x; // 1
```

或者函数的时候，都应该返回`false`

```
  function x(){}
  delete x; // false
  typeof x; // "function"
```

要理解这种行为，需要理解js中的变量实例`variable instantiation` 和 对象的属性 （`property attributes` ），在接下来的段落里，就会就这些概念深入讲解，如果你不关心它们是怎么工作的，可以跳过这些段落。

### 代码类型

简单来说JS代码里有三种类型的代码：**全局代码**，**方法代码**，**eval代码**（**Global code**, **Function code** and **Eval code**）。

- 当一个源文本作为一个程序,它在全局范围内执行，被认为是**全局代码**。在浏览器环境中,`script`元素的内容通常被解析成程序，因此可以被认为是一个**全局代码**。
- 很明显，任何直接在函数执行的代码可以被认为是一个**函数代码**。在浏览器中，事件属性(例如`<p onclick = "…" >`)通常是解析成**函数代码**。
- 最后，被包裹在`eval`中的代码，被认为是eval代码。我们接下来看看为什么这个类型这么特殊。



### 执行上下文（Execution context）

关于执行上下文，可以参考[《You Dont Know J》](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20&%20closures/README.md#you-dont-know-js-scope--closures)

总的来说，当ECMAScript代码执行时，它总是发生在特定的 `执行上下文`。执行上下文是一个抽象的实体（这有助于理解范围和变量实例化是如何工作的）。的三种类型的可执行代码,有一个执行上下文。当执行一个函数时,据说,控制进入执行上下文功能代码;当全球代码执行,控制进入执行上下文对全球代码,等等。