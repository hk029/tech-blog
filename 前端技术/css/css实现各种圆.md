---
title: css实现各种圆
date: 2016-09-13
categories: 
- 前端技术
- css
tags: 
- css
- 常用css技巧
init: 1
---

基本上圆都是通过圆角实现的，下面简要介绍一下圆角半径的使用方法：

# 圆角

## border-radius

### 使用方法：
`border-radius: [半径] `
- 设置所有角
```css
border-radius: 5px; 
```
- 设置不同的角：  四个半径值分别是左上角、右上角、右下角和左下角，顺时针 
```css
border-radius: 1px 2px 3px 4px; 
```

![your text](http://img.hksite.cn/1473769031264)

# 各种圆例子

可以[点击查看](https://codepen.io/voidsky/pen/JVXpbN)实例

- 实心圆：
方法：把宽度（width）与高度(height)值设置为一致（也就是正方形），并且四个圆角值都设置为它们值的一半。如下代码：
![](http://img.hksite.cn/2019-04-05-032804.png)

```html
div{
    height:100px;/*与width设置一致*/
    width:100px;
    background:#9da;
    border-radius:50px;/*四个圆角值都设置为宽度或高度值的一半， 用50%也行*/
    }
```

- 实心上半圆：

方法：把高度(height)设为宽度（width）的一半，并且只设置左上角和右上角的半径与元素的高度一致（**大于**也是可以的，关键是两个半径值**一样**）。

```css3

div{
    height:50px;/*是width的一半*/
    width:100px;
    background:#9da;
    border-radius:50px 50px 0 0;/*半径至少设置为height的值*/
    }
```
![](http://img.hksite.cn/2019-04-05-032818.png)

- 实心左半圆：

方法：同上，把宽度设为高度的一半，并且只设置左上角和右上角的半径与元素的宽度一致（**大于**也是可以的，关键是两个半径值**一样**）。

```css3

div{
    height:100px;
    width:50px;/*是height的一半*/
    background:#9da;
    border-radius:50px 0 0 50px;/*半径至少设置为width的值*/
    }
```

![](http://img.hksite.cn/2019-04-05-032825.png)