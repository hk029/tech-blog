---
title: css中的inline-block元素之间的空白间隔问题
date: 2017-03-08
categories: 
- 前端技术
- css
tags: 
- css
init: 1
---
## 问题

有时候，我们需要将`<li>`横向排列，而又为了能设置其宽度和高度，为其设置`display:inline-block`，相邻`<li>`之间会出现4px的空白间隔（根据浏览器不同），不是margin也不是padding。

![image-20190402204903048](http://img.hksite.cn/2019-04-02-124903.png)

可以[点击这里](https://codepen.io/voidsky/pen/xeGPRK)查看代码：

<p class="codepen" data-height="265" data-theme-id="0" data-default-tb="css,result" data-user="voidsky" data-slug-hash="xeGPRK" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;" data-pen-title="xeGPRK">
  <span>See the Pen <a href="https://codepen.io/voidsky/pen/xeGPRK/">

## 原因

浏览器会把inline元素间的**空白字符**渲染成空格。而为了美观。我们通常是一个`<li>`放在一行，这导致`<li>`换行后产生换行字符，它变成一个空格，占用了一个字符的宽度。（详细可以参考文档匿名块部分：<https://www.w3.org/TR/CSS22/visuren.html#anonymous-block-level>）

## 解决

### 方法一

为`<li>`设置float: left。

**不足：**有些容器是不能设置浮动，如左右切换的焦点图等。

### 方法二

将所有`<li>`写在同一行。

```html
<ul class="pagination">
  <li>1</li><li>2</li><li>3</li><li>4</li>
</ul>
```

这一方法诞生了很多变形：
利用不闭合标签，注意，最后一个列表的标签的结束（闭合）标签是为兼容IE6/IE7。
```html
<ul class="pagination">
  <li>1
  <li>2
  <li>3
  <li>4</li>
</ul>
```
利用标签换行

```html
<ul class="pagination">
  <li>1</li
  ><li>2</li
  ><li>3</li
  ><li>4</li>
</ul>
```
利用注解
```html
<ul class="pagination">
  <li>1</li><!--
  --><li>2</li><!--
  --><li>3</li><!--
  --><li>4</li>
</ul>
```

**不足：**代码不美观。

### 方法三

将`<li>`内的字符尺寸直接设为0，即**font-size: 0**。

**不足：**`<li>`中的其他字符尺寸也被设为0，需要额外重新设定其他字符尺寸，且在Safari浏览器依然会出现空白间隔。

### 方法四

利用word-spacing

```css
.pagination {
    word-spacing: -6px;
}
.pagination li {
    word-spacing: 0;
}
```

### 方法五

safari可以用消除字符间隔**letter-spacing: -npx**，而这也设置了`<li>`内的字符间隔，因此需要将`<li>`内的字符间隔设为默认**letter-spacing: normal**。

## 最终解决方案

```css

.finally-solve {
  letter-spacing: -4px;/*根据不同字体字号或许需要做一定的调整*/
  word-spacing: -4px;
  font-size: 0;
}
.finally-solve li {
  font-size: 16px;
  letter-spacing: normal;
  word-spacing: normal;
  display:inline-block;
  *display: inline;
  zoom:1;
}
```