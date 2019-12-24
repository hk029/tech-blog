---
date: 2017-04-12
tags: 
- javascript
categories: 
- 前端技术
- javascript
title: javascript的Array.prototype.slice.call(arguments)实现原理
---


## javascript的Array.prototype.slice.call(arguments)实现原理

### slice()方法

数组和字符串都有这个slice方法，这个方法的作用是截取一段数据。**它接收两个参数，第一个参数是要截取的位置索引，第二参数可选，表示要截取到的结束位置，但是不包括结束位置。**在数组中，该方法的返回值是包含截取元素的组成的数组，在字符串中，该方法的返回值是包含截取字符串组成的字符串。

该方法也可以传入负数值，当参数为负数的时候，将参数和数组或字符串的长度相加得到的正数作为实际的参数。

如下：

```javascript
<span class="pun">[</span><span class="lit">1</span><span class="pun">,</span><span class="lit">2</span><span class="pun">,</span><span class="lit">3</span><span class="pun">,</span><span class="lit">4</span><span class="pun">,</span><span class="lit">5</span><span class="pun">,</span><span class="lit">6</span><span class="pun">].</span><span class="pln">slice</span><span class="pun">(</span><span class="lit">2</span><span class="pun">,</span><span class="lit">4</span><span class="pun">);</span><span class="pln">
</span><span class="pun">[</span><span class="lit">1</span><span class="pun">,</span><span class="lit">2</span><span class="pun">,</span><span class="lit">3</span><span class="pun">,</span><span class="lit">4</span><span class="pun">,</span><span class="lit">5</span><span class="pun">,</span><span class="lit">6</span><span class="pun">].</span><span class="pln">slice</span><span class="pun">(-</span><span class="lit">4</span><span class="pun">,-</span><span class="lit">2</span><span class="pun">);</span>
```

返回值均为[3,4]，为数组。

```javascript
<span class="str">'everything'</span><span class="pun">.</span><span class="pln">slice</span><span class="pun">(</span><span class="lit">2</span><span class="pun">,</span><span class="lit">4</span><span class="pun">);</span><span class="pln">
</span><span class="str">'everything'</span><span class="pun">.</span><span class="pln">slice</span><span class="pun">(-</span><span class="lit">4</span><span class="pun">,-</span><span class="lit">2</span><span class="pun">);</span>
```

返回值分别为'er'和'hi'，为字符串。

### Array.prototype.slice

因为slice是Array的一个默认方法，所以在Array的原型内肯定有这个方法。

### call()和apply()

call()和apply()方法主要是用来扩充函数的作用域（改变this指针），这两个方法基本一样，就是参数不一样。他们第一个参数都是**新的作用域**，就是后续参数不一样：

call() ：传递函数的参数必须逐个写入。

apply()：第二个是参数数组，其中第二个参数可以是数组实例，**也可以是arguments对象**。

## 如何将一个类数组转换成数组

### Array.prototype.slice.call

这个方法的核心在于slice（a,b）的实现原理，它就像一个循环

```javascript
<span class="kwd">var</span><span class="pln"> begin </span><span class="pun">=</span><span class="pln"> a</span><span class="pun">||</span><span class="lit">0</span><span class="pun">;</span><span class="pln">
</span><span class="kwd">var</span><span class="pln"> end </span><span class="pun">=</span><span class="pln"> b</span><span class="pun">||</span><span class="kwd">this</span><span class="pun">.</span><span class="pln">length</span><span class="pun">;</span><span class="pln">
</span><span class="com">//其实应该是+this.length)%this.length，这里简化就不写了</span><span class="pln">
</span><span class="kwd">for</span><span class="pun">(</span><span class="kwd">var</span><span class="pln"> i </span><span class="pun">=</span><span class="pln"> a</span><span class="pun">;</span><span class="pln">i</span><span class="pun"><</span><span class="pln">b</span><span class="pun">;</span><span class="pln">i</span><span class="pun">++){.....}</span>
```

从如果不传参数，它默认是根据`this.length`来判断截取长度，也就是说，关键是你的类数组对象有一个`length`属性，然后0，1，2……这些属性有值，它就能根据这个属性进行切割数组。

![your text](<http://img.hksite.cn/1491976686912> "")

而argumemts就是这样一个类数组对象，所以自然可以使用Array.prototype.slice.call进行转换了

![your text](<http://img.hksite.cn/1491977667050> "")

