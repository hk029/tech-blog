---
date: 2016-12-12
categories: 
- 前端技术
- javascript
tags: 
- javascript精粹
- Javascript
title: javascript精粹（2）-对象
---

## 介绍



### 简单数据类型



Javascript的简单数据类型包括`数字`、`字符串`、`布尔值`、`null值`、和`undefined值`。其他所有的值都是对象



`数字`、`字符串`、`布尔值`看上去是对象，因为它们拥有方法，但是实际上，**他们是不可变的**



## 对象



### 概况



- js中的对象是**可变的**`键控组合`（keyed collections）
- `数组`、`函数`、`正则表达式`都是对象，当然**对象也是对象**
- 对象是属性的容器： 

    - 属性的名字可以是**空字符串在内**的任意字符串
    - 属性值可以是**除undefined值**之外的任何值

    <!-- -->

- 对象是**无类型**的（class-free）。它对新属性的名字和属性的值没有限制。
- 对象可以包含对象
- js包含一种`原型链`的特性，允许对象继承另一个对象的属性。正确使用能减少对象初始化的时间和内存。

<!-- -->



### 对象字面量



就是包含在`{}`之间的**零或多个**`名/值`对。对象字面量可以**出现在任何允许表达式出现的地方**。



- 如果属性名是一个合法的`标识符`，则**不强制要求使用引号括住属性名**：

    > "first-name" 是必须的
    > 
    > 
    > 
    > first\_name 是可选的


<!-- -->



### 检索



要检索里面的值，可以采用`[]`，如果是合法的标识符则可以用`.`代替。（优先考虑`.`，因为它更加紧凑好看）



- 如果值不存在，会返回`undefined`

    - `||`可以用来填充默认值

    <!-- -->

    > var status = flight.status \|\| "unknown"

- 尝试从`undefined`中获取属性会报异常

    - `&amp;&amp;`可以用来规避错误

    <!-- -->

    > flight.equipment && flight.equipment.mode


<!-- -->



### 更新



对象值可以通过赋值语句**更新**或者**新建**



### 引用



对象通过引用来传递。**它们永远不会复制！！！**



### 原型



每个对象都会连接到一个原型对象，并且可以从它中继承属性。所有的对象都连接到`Obeject.prototype`



新建对象：



```javascript
<span class="kwd">if</span><span class="pln"> </span><span class="pun">(</span><span class="kwd">typeof</span><span class="pln"> </span><span class="typ">Object</span><span class="pun">.</span><span class="pln">beget </span><span class="pun">!==</span><span class="pln"> </span><span class="str">'function'</span><span class="pun">){</span><span class="pln">
</span><span class="typ">Object</span><span class="pun">.</span><span class="pln">create </span><span class="pun">=</span><span class="pln"> </span><span class="kwd">function</span><span class="pun">(</span><span class="pln">o</span><span class="pun">){</span><span class="pln">
</span><span class="kwd">var</span><span class="pln"> F </span><span class="pun">=</span><span class="pln"> </span><span class="kwd">function</span><span class="pun">(){};</span><span class="pln">
<p>F<span class="pun">.</span><span class="pln">prototype </span><span class="pun">=</span><span class="pln"> o</span><span class="pun">;</span><span class="pln"></span></p>
</span><span class="kwd">return</span><span class="pln"> </span><span class="kwd">new</span><span class="pln"> F</span><span class="pun">();</span><span class="pln">
</span><span class="pun">}</span><span class="pln">
</span><span class="pun">}</span>
```



- 原型链在更新的使用不起作用（我们**改变对象不会影响原型链**）
- 如果没有属性，会去查原型，如果原型没有就是去查原型的原型……一直查到`Obejct`，如果还是没有，就报`undefined`。这过程称为**委托** （这就像一条链一样，所以称为原型链）
- **动态**：如果在原型中增加一个属性，它**立刻对所有基于该原型的对象可见**

<!-- -->



### 枚举



- for in 可以用来遍历一个对象中的所有`属性名`

- 过滤原型属性，可以用`hasOwnProperty()`

    ```javascript
    <span class="kwd">if</span><span class="pln"> </span><span class="pun">(</span><span class="kwd">typeof</span><span class="pln"> one_obejct</span><span class="pun">.</span><span class="pln">hasOwnProperty</span><span class="pun">(</span><span class="pln">name</span><span class="pun">))</span><span class="pln">
    </span><span class="pun">{</span><span class="pln">
    </span><span class="pun">……</span><span class="pln">
    </span><span class="pun">}</span>
    ```

- 过滤函数，可以用`typeof`

    ```javascript
    <span class="kwd">if</span><span class="pln"> </span><span class="pun">(</span><span class="kwd">typeof</span><span class="pln"> one_obejct</span><span class="pun">[</span><span class="pln">name</span><span class="pun">]</span><span class="pln"> </span><span class="pun">!==</span><span class="pln"> </span><span class="str">'function'</span><span class="pun">)</span><span class="pln">
    </span><span class="pun">{</span><span class="pln">
    </span><span class="pun">……</span><span class="pln">
    </span><span class="pun">}</span>
    ```

- for in 是**不保证顺序的**，如果想保证顺序，可以另创建一个数组包含需要的属性名，用简单的for循环


<!-- -->



### 删除



- `delete`可以删除对象的属性**，不会触及原型链中的任何对象**
- 删除对象属性后，原型链的属性会**透现出来**

<!-- -->



### 减少全局污染



最小化使用全局变量的方法之一是为你的应用只创建一个唯一的全局变量：



```javascript
<span class="kwd">var</span><span class="pln"> MYAPP </span><span class="pun">=</span><span class="pln"> </span><span class="pun">{};</span>
```



把这个`MYAPP`作为你的应用容器



```javascript
<span class="pln">MYAPP</span><span class="pun">.</span><span class="pln">person </span><span class="pun">=</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
<p>firstName <span class="pun">=</span><span class="pln"> </span><span class="str">'Joe'</span><span class="pun">,</span><span class="pln"></span></p>
<p>lastName<span class="pun">=</span><span class="pln"> </span><span class="str">'Howard'</span><span class="pln"></span></p>
</span><span class="pun">}</span><span class="pln">
<p></p>
<p>MYAPP<span class="pun">.</span><span class="pln">enrollment </span><span class="pun">=</span><span class="pln"> </span><span class="kwd">function</span><span class="pun">(){</span><span class="pln"></span></p>
</span><span class="pun">……</span><span class="pln">
</span><span class="pun">}</span>
```



另一种方法减少全局变量污染的是`闭包`

