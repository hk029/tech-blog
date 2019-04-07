---
title: text-overflow和word-wrap处理文本溢出问题
date: 2016-10-20
categories: 
- 前端技术
- css
tags: 
- css
- 常用css技巧
init: 1
---
# 


在日常生活中，我们经常会遇到这个头疼的问题就是一行显示的内容过多（**div部分必选限制了宽度**，如果没限制宽度也就不存在溢出的概念了，你懂的。。。），在css3中我们一般有两个常用场景：

- 让多余的部分**截去**或者显示**省略号**

- 让多余英文强制**换行**

可以看[具体例子](https://codepen.io/voidsky/pen/MRyXgg)

![image-20190405135446347](http://img.hksite.cn/2019-04-05-055446.png)

# 单行省略

![image-20190405135522571](http://img.hksite.cn/2019-04-05-055523.png)

## text-overflow

![your text](http://img.hksite.cn/1473772161542)

这个函数的功能就是实现对溢出文本的操作，如果选择`clip`则会截去，选择ellipsis则会显示省略号。但是为了实现文本的溢出，我们一般要**同时**做以下设置：

```css
text-overflow:ellipsis; 
overflow:hidden; 
white-space:nowrap; 
```

其中

`overflow:hidden`   表示隐藏溢出部分 
`white-space:nowrap ` 表示强制在一行显示，不然会自动换行（英文默认是按单词换行）

### 举例

```css
.test-demo{
    text-overflow:ellipsis; 
    overflow:hidden; 
    white-space:nowrap; 
    width:200px; 
}
```



# 多行省略

![image-20190405135631957](http://img.hksite.cn/2019-04-05-055632.png)

多行省略主要是用的WebKit的CSS扩展属性

```css
display: -webkit-box;
-webkit-box-orient: vertical;
-webkit-line-clamp: 3;
overflow: hidden;
```

`-webkit-line-clamp`用来限制在一个块元素显示的文本的行数。 为了实现该效果，它需要组合其他的WebKit属性。

常见结合属性：

- `display: -webkit-box;` 必须结合的属性 ，将对象作为弹性伸缩盒子模型显示 。

- `-webkit-box-orient` 必须结合的属性 ，设置或检索伸缩盒对象的子元素的排列方式 

```css
.test-demo{
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 3;
  overflow: hidden;	
}
```

# 英文强制换行

![image-20190405135648470](http://img.hksite.cn/2019-04-05-055649.png)

## word-wrap

![your text](http://img.hksite.cn/1473772253912)

另一种解决方案就是换行了，它有两个参数normal和break-word，这个不太好解释，主要是在英文句子中间，遇到一个连续的单词到底换不换行的问题，直接看例子，很清楚：

```css
.test-demo{

    word-wrap: normal; 
/*     word-wrap: break-word;  */
    width:200px; 
    background:#aaa;
}
```