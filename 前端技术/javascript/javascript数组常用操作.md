---
date: 2016-09-17
categories: 
- 前端技术
- javascript
title: javascript数组常用操作.bak
---

![your text](<http://img.hksite.cn/1474643536781>)

<div class="toc"><ul><li><a href="#用map创建数组">用map创建数组</a></li><li><a href="#用reduce压缩数组">用reduce压缩数组</a></li><li><a href="#用fliter过滤数组">用fliter过滤数组</a></li><li><a href="#数组排序sort">数组排序sort</a></li><li><a href="#逆转数组reverse">逆转数组reverse</a></li><li><a href="#数组拼接concat">数组拼接concat</a></li><li><a href="#字符串和数组转换">字符串和数组转换</a></li><li><a href="#splice函数">splice函数</a></li><li><a href="#slice函数">slice函数</a></li></ul></div>

js的数组和python的list一样可以存不同类型不同维度个数据，除了可以用下标查看修改数据外，还有几个方法：

- push()：加到最后
- pop(): 从最后取
- shift(): 从开头取
- unshift(): 加入开头

<!-- -->

构造数组的方式还有如下：（除了特别说明的外，都不改变原数组）

# 用map创建数组

<pre><code class="language-js hljs "><span class="hljs-keyword">var</span> oldArray = [<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">3</span>,<span class="hljs-number">4</span>]
<span class="hljs-keyword">var</span> timesFour = oldArray.map(<span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">(val)</span>{</span>
<span class="hljs-keyword">return</span> val * <span class="hljs-number">4</span>;
</code><p><code class="language-js hljs ">});</code></p></pre>



# 用reduce压缩数组

reduce的第2个参数（初始值）可选，如果没有，就从数组第一个开始

<pre><code class="language-js hljs "><span class="hljs-keyword">var</span> array = [<span class="hljs-number">4</span>,<span class="hljs-number">5</span>,<span class="hljs-number">6</span>,<span class="hljs-number">7</span>,<span class="hljs-number">8</span>];
<span class="hljs-keyword">var</span> sum = <span class="hljs-number">0</span>;
<p>sum = array.reduce(<span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">(pre,cur)</span>{</span></p>
<span class="hljs-keyword">return</span> pre+cur;
</code><p><code class="language-js hljs ">},<span class="hljs-number">0</span>);</code></p></pre>



# 用fliter过滤数组

如果我们只需要数组中小于6的元素

<pre><code class="language-js hljs ">
<span class="hljs-keyword">var</span> oldArray = [<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">3</span>,<span class="hljs-number">4</span>,<span class="hljs-number">5</span>,<span class="hljs-number">6</span>,<span class="hljs-number">7</span>,<span class="hljs-number">8</span>,<span class="hljs-number">9</span>,<span class="hljs-number">10</span>];
<span class="hljs-keyword">var</span> newArray = oldArray.fliter(<span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">(val)</span>{</span>
<span class="hljs-keyword">return</span> val &lt; <span class="hljs-number">6</span>;
</code><p><code class="language-js hljs ">});</code></p></pre>



# 数组排序sort

数组有排序的功能(会`改变原数组`，并且也会返回)，如果不带参数，默认是按字符串排序，如果要改变排序方式，可以在里面增加比较函数，规则如下

\- 负数：a在b前

\- 大于：b在a前

<pre><code class="language-js hljs ">
<span class="hljs-keyword">var</span> array = [<span class="hljs-number">1</span>, <span class="hljs-number">12</span>, <span class="hljs-number">21</span>, <span class="hljs-number">2</span>];
<span class="hljs-comment">//降序排序</span>
<p>array.sort(<span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">(a,b)</span>{</span></p>
<span class="hljs-keyword">return</span>  b-a;
<p>});</p>
<span class="hljs-comment">//升序排序</span>
<p>array.sort(<span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">(a,b)</span>{</span></p>
<span class="hljs-keyword">return</span>  a-b;
</code><p><code class="language-js hljs ">});</code></p></pre>



# 逆转数组reverse

`改变原数组`<pre><code class="language-js hljs "><span class="hljs-keyword">var</span> array = [<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">3</span>,<span class="hljs-number">4</span>,<span class="hljs-number">5</span>,<span class="hljs-number">6</span>,<span class="hljs-number">7</span>];
</code><p><code class="language-js hljs ">array.reverse();</code></p></pre>



# 数组拼接concat

<pre><code class="language-js hljs "><span class="hljs-keyword">var</span> oldArray = [<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">3</span>];
<span class="hljs-keyword">var</span> newArray = [];
<span class="hljs-keyword">var</span> concatMe = [<span class="hljs-number">4</span>,<span class="hljs-number">5</span>,<span class="hljs-number">6</span>];
</code><p><code class="language-js hljs ">newArray = oldArray.concat(concatMe);</code></p></pre>



# 字符串和数组转换

- 用split切割字符串

<!-- -->

<pre><code class="language-js hljs ">
<span class="hljs-keyword">var</span> string = <span class="hljs-string">"Split me into an array"</span>;
<span class="hljs-keyword">var</span> array = [];
</code><p><code class="language-js hljs ">array = string.split(<span class="hljs-string">' '</span>);</code></p></pre>



- 用joint把数组拼接成字符串

<!-- -->

<pre><code class="language-js hljs "><span class="hljs-keyword">var</span> joinMe = [<span class="hljs-string">"Split"</span>,<span class="hljs-string">"me"</span>,<span class="hljs-string">"into"</span>,<span class="hljs-string">"an"</span>,<span class="hljs-string">"array"</span>];
<span class="hljs-keyword">var</span> joinedString = <span class="hljs-string">''</span>;
</code><p><code class="language-js hljs ">joinedString = joinMe.join(<span class="hljs-string">' '</span>);</code></p></pre>



# splice函数

> array.splice(start, deleteCount[, item1[, item2[, …]]])

js提供了一个splice函数，用来删除`index`位置处的`deleteCount`数目的元素，并且在index处加入item1,2,3……（可以不加入）

这个函数可以用来替换数组内的部分元素

```js
<span class="hljs-keyword">var</span> myFish = [<span class="hljs-string">'angel'</span>, <span class="hljs-string">'clown'</span>, <span class="hljs-string">'mandarin'</span>, <span class="hljs-string">'surgeon'</span>];
<span class="hljs-comment">// removes 0 elements from index 2, and inserts 'drum'</span>
<span class="hljs-keyword">var</span> removed = myFish.splice(<span class="hljs-number">2</span>, <span class="hljs-number">0</span>, <span class="hljs-string">'drum'</span>);
<span class="hljs-comment">// myFish is ['angel', 'clown', 'drum', 'mandarin', 'surgeon']</span>
<span class="hljs-comment">// removed is [], no elements removed</span>
<p>removed = myFish.splice(<span class="hljs-number">3</span>, <span class="hljs-number">1</span>);</p>
<span class="hljs-comment">// myFish is ['angel', 'clown', 'drum', 'surgeon']</span>
<span class="hljs-comment">// removed is ['mandarin']</span>
<p>removed = myFish.splice(<span class="hljs-number">2</span>, <span class="hljs-number">1</span>, <span class="hljs-string">'trumpet'</span>);</p>
<span class="hljs-comment">// myFish is ['angel', 'clown', 'trumpet', 'surgeon']</span>
<span class="hljs-comment">// removed is ['drum']</span>
```

# slice函数

> arr.slice([begin[, end]])

取出数组的从begin到end的元素，重新组成数组。

```js
<span class="hljs-keyword">var</span> fruits = [<span class="hljs-string">'Banana'</span>, <span class="hljs-string">'Orange'</span>, <span class="hljs-string">'Lemon'</span>, <span class="hljs-string">'Apple'</span>, <span class="hljs-string">'Mango'</span>];
<span class="hljs-keyword">var</span> citrus = fruits.slice(<span class="hljs-number">1</span>, <span class="hljs-number">3</span>);<span class="hljs-comment">// ['Orange','Lemon']</span>
```

<div></div>

