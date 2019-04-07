---
title: css3背景新增属性
date: 2016-09-13
categories: 
- 前端技术
- css
tags: 
- css
- css3
init: 1
---
# 设置背景图片的起始位置

## background-origin

## 用法：

`background-origin ： border-box | padding-box | content-box;`

## 效果：

![your text](http://img.hksite.cn/1473775321591)

注意：如果背景不是`no-repeat`，这个属性无效，它会从边框开始显示。


# 背景裁减

## background-clip

### 用法：

`background-clip ： border-box | padding-box | content-box | no-clip`


参数分别表示从**边框**、或**内填充**，**或者内容区域向外裁剪背景**。**no-clip**表示不裁切，和参数border-box显示同样的效果。backgroud-clip默认值为border-box。


### 效果：

![your text](http://img.hksite.cn/1473776082265)



# 背景大小

## background-size

### 用法：

`background-size: auto | <长度值> | <百分比> | cover | contain`

1、auto：默认值，不改变背景图片的原始高度和宽度；

2、<长度值>：成对出现如200px 50px，将背景图片宽高依次设置为前面两个值，当设置一个值时，将其作为图片宽度值来等比缩放；

3、<百分比>：0％~100％之间的任何值，将背景图片宽高依次设置为所在元素宽高乘以前面百分比得出的数值，当设置一个值时同上；

4、cover：顾名思义为覆盖，即将背景图片等比缩放以填满整个容器；

5、contain：容纳，即将背景图片等比缩放至某一边紧贴容器边缘为止。

### 例子
- 百分比

```css3
.demo {
    background: url(http://www.crazyxhtml.com/blog/wp-content/uploads/2012/11/css3-markup.jpg) no-repeat;
    width: 300px;
    height: 300px;
    border: 1px solid #999;
    background-size:40%;
}
```
![your text](http://img.hksite.cn/1473776334179)

- px

```css3
    background-size:150px;
```

- contain
```css3
    background-size:cover;
```
![your text](http://img.hksite.cn/1473776513103)


- cover
```css3
    background-size:cover;
```
![your text](http://img.hksite.cn/1473776415140)


# 多重背景
## 用法：
```css3
background ： [background-color] | [background-image] | [background-position][/background-size] | [background-repeat] | [background-attachment] | [background-clip] | [background-origin],...
```
其中：size必须用反斜杠，而且必须接在position之后

用拆解方式更容易理解：每个属性依次设置不同的背景

```css3
background-repeat : repeat1,repeat2,...,repeatN;
backround-position : position1,position2,...,positionN;
background-size : size1,size2,...,sizeN;
background-attachment : attachment1,attachment2,...,attachmentN;
background-clip : clip1,clip2,...,clipN;
background-origin : origin1,origin2,...,originN;
background-color : color;
```
注意：background-color只能设置一个！

### 实例
```css3
.demo{
    width: 300px;
    height: 140px;
    border: 1px solid #999;

    background-color:black;
    background-image: url(./1.jpg),
                      url(./2.jpg),
                      url(./3.jpg);

    background-position: left top, 100px 0, 250px 0;
    background-size: 20% 30%, 100px 100px,50px;
    background-repeat: no-repeat, no-repeat, no-repeat;
    
    margin:0 0 20px 0;
}

```

![your text](http://img.hksite.cn/1473777974551)