---
title: 纯css3实现标签页切换
date: 2017-03-08
categories: 
- 前端技术
- css
tags: 
- css
- 小练手
init: 1
---

## 原理

利用`:target`属性，每个标签都是一个a标签，链接到不同的页面锚点。当点击选中的时候，触发`:target`属性，没选择的时候触发`:not(:target)`属性。

```css
.bg:not(:target) {
	display: none;
}
.bg:target{
	display: block;
}
```

## html结构

```html
<body>
<div id="container" class="container">
<ul>
<li id="item1"><a href="#content-1">商品1</a></li>
<li id="item2"><a href="#content-2">商品2</a></li>
<li id="item3"><a href="#content-3">商品3</a></li>
<li id="item4"><a href="#content-4">商品4</a></li>
<li id="item5"><a href="#content-5">商品5</a></li>
</ul>
<div class="current-content">
<div id="content-1" class="bg"><img src="images/1.jpg" alt="" ></div>
<div id="content-2" class="bg" ><img src="images/2.jpg" alt=""></div>
<div id="content-3" class="bg" ><img src="images/3.jpg" alt=""></div>
<div id="content-4" class="bg" ><img src="images/4.jpg" alt=""></div>
<div id="content-5" class="bg" ><img src="images/5.jpg" alt=""></div>
</div>
</div>
</body>
```

## css结构

```css
*{margin:0;padding:0;} 

.container{
  display: block;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
ul{
  display: block;
  letter-spacing: -9px;
  background: #eee;
  list-style: none;
}
ul>li{
  display: inline-block;
  line-height: 40px;
}
ul>li a{
  list-style: none;
  box-sizing: border-box;
  text-decoration: none;
  position: relative;
  color: black;
  display: inline-block;
  border: 1px solid #aaa;
  border-right: 0px;
  width: 80px;
  height: 40px;
  letter-spacing: normal;
  text-align: center;
}
ul a:last-child{
  border-right: 1px solid #aaa;
}
ul a:hover{
  background: white;
  border-bottom: 0px;
  cursor: pointer;
}
.current-content div img{
  width: 100%;
  height: 100%;
}
```

## 效果

![your text](<http://img.hksite.cn/1488885804298> "")

![your text](<http://img.hksite.cn/1488885823578> "")