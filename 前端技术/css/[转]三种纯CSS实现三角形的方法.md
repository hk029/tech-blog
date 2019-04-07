---
title: 三种纯CSS实现三角形的方法
date: 2016-09-20
categories: 
- 前端技术
- css
tags: 
- css
- 常用css技巧
init: 1
---
> 这篇博文现在已找不到原文出处了，但是我记得应该是16年9月之前就有了，当时觉得很好就转了。

纯 CSS 做三角形的方法，目前我知道三种
- 利用 border 属性
- “◆”字符
-  CSS3 transfrom 做 45 度旋转实现的

# 利用 border 属性实现三角形

这个原理很简单，我我们先看下面的图，这是一个边框为 20px 的 div，看他的边框，是个梯形，变化会从这里开始。

![](http://Img.hksite.cn/1474350664807)

CSS：

```css
.triangle{
    width:30px;
    height:30px;
    border-width:20px;
    border-style:solid;
    border-color:#e66161 #f3bb5b #94e24f #85bfda;
}
```


好的，现在我把它的宽和高都设为 0，看看有什么变化。

![](http://Img.hksite.cn/1474350680684)

四个边框都变成三角形了，现在我再把它的左右和下边框的颜色都设成透明或和背景颜色相同的颜色，就出来我们想要的三角形了，推荐把边框设置成透明，这样拓展性更好。

![](http://Img.hksite.cn/1474350802504)

注：IE6下把边框设置成 transparent 时会出现黑影，并不会透明，把 border-style 设置成 dashed 可以解决。

CSS：

```css
.triangle{
    width:0;
    height:0;
    border-width:20px;
    border-style:solid dashed dashed dashed;
    border-color:#e66161 transparent transparent transparent;
}
```


如果我们想实现下图的效果该怎么办呢？很简单，做两个小三角，一个是背景色，一个是边框色，然后利用定位重叠在一起，记住他们的定位要相差一个像素。

![](http://Img.hksite.cn/1474350825918)

HTML：

```html
<div class="message-box">
  <span>我是利用 border 属性实现的</span>
  <div class="triangle-border tb-border"></div>
  <div class="triangle-border tb-background"></div>
</div>
```




CSS：

```css
.message-box {
    position:relative;
    width:240px;
    height:60px;
    line-height:60px;
    background:#E9FBE4;
    box-shadow:1px 2px 3px #E9FBE4;
    border:1px solid #C9E9C0;
    border-radius:4px;
    text-align:center;
    color:#0C7823;
}
.triangle-border {
    position:absolute;
    left:30px;
    overflow:hidden;
    width:0;
    height:0;
    border-width:10px;
    border-style:solid dashed dashed dashed;
}
.tb-border {
    bottom:-20px;
    border-color:#C9E9C0 transparent transparent transparent;
}
.tb-background {
    bottom:-19px;
    border-color:#E9FBE4 transparent transparent transparent;
}
```



# 利用”◆“字符实现三角形

字符实现也是要用两个字符用绝对定位去模拟，只是它不能模拟出三角形，它是个菱形，然后露出半个头，底色又和背景色一样，看上去就像是个三角形了。。

注意：它的大小是由 font-size 决定的，width 和 height 都决定不了，但最好还是加上，这样各个浏览器去生成这个字符的时候能保持一致，我们去写绝对定位的时候就不用写 hack 了。

HTML：

```html
<div class="message-box">
  <span>我是利用 ◆ 字符实现的</span>
  <div class="triangle-character tc-border">◆</div>
  <div class="triangle-character tc-background">◆</div>
</div>
```

CSS：

```css
.message-box {
    position:relative;
    width:240px;
    height:60px;
    line-height:60px;
    background:#E9FBE4;
    box-shadow:1px 2px 3px #E9FBE4;
    border:1px solid #C9E9C0;
    border-radius:4px;
    text-align:center;
    color:#0C7823;
}
.triangle-character {
    position:absolute;
    left:30px;
    overflow:hidden;
    width:26px;
    height:26px;
    font:normal 26px "宋体"; // 字符的大小和字体也有关系哦！
}
.tc-border {
    bottom:-13px;
    color:#C9E9C0;
}
.tc-background {
    bottom:-12px;
    color:#E9FBE4;
}
```

# 利用 CSS3 transfrom 旋转 45 度实现三角形

先创建一个带 border 的 div ，设置好背景色和相邻的两个边框的颜色，然后选择 45 度，听着很简单是吗，但是利用 IE 的 matrix filter 实现 css3 transfrom 的兼容方案很头大，我是没看懂，有看懂的兄弟情赐教啊：）

注：IE6下无效。

![](http://Img.hksite.cn/1474351061477)

HTML：

```html
<div class="message-box">
  <span>我是利用 css transfrom 属性字符实现的</span>
  <div class="triangle-css3 transform ie-transform-filter"></div>
</div>
```




CSS：

```css
.message-box {
    position:relative;
    width:240px;
    height:60px;
    line-height:60px;
    background:#E9FBE4;
    box-shadow:1px 2px 3px #E9FBE4;
    border:1px solid #C9E9C0;
    border-radius:4px;
    text-align:center;
    color:#0C7823;
}
.triangle-css3 {
    position:absolute;
    bottom:-8px;
    bottom:-6px;
    left:30px;
    overflow:hidden;
    width:13px;
    height:13px;
    background:#E9FBE4;
    border-bottom:1px solid #C9E9C0;
    border-right:1px solid #C9E9C0;
}
.transform {
    -webkit-transform:rotate(45deg);
    -moz-transform:rotate(45deg);
    -o-transform:rotate(45deg);
    transform:rotate(45deg);
}
/*ie7-9*/
.ie-transform-filter {
    -ms-filter: "progid:DXImageTransform.Microsoft.Matrix(
        M11=0.7071067811865475,
        M12=-0.7071067811865477,
        M21=0.7071067811865477,
        M22=0.7071067811865475,
    SizingMethod='auto expand')";
    filter: progid:DXImageTransform.Microsoft.Matrix(
        M11=0.7071067811865475,
        M12=-0.7071067811865477,
        M21=0.7071067811865477,
        M22=0.7071067811865475,
    SizingMethod='auto expand');
}
```