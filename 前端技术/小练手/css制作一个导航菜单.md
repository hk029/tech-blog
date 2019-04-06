---
title: css制作一个导航菜单
date: 2016-11-02
tags: 
- css
- 小练手
---
# 预计效果
![](http://img.hksite.cn/2019-04-05-055925.png)

# 步骤

## 整体构造，使用列表

```html

<ul class="nav">
    <li><a href="">Home</a></li>
    <li><a href="">About Me</a></li>
    <li><a href="">Portfolio</a></li>
    <li><a href="">Blog</a></li>
    <li><a href="">Resources</a></li>
    <li><a href="">Contact Me</a></li>
</ul>
```



效果：

![your text](http://img.hksite.cn/1473813579540)

## 增加整体css样式

```css
 .nav {
    width: 560px;
    height: 50px;
    font: bold 0/50px Arial;
    text-align: center;
    margin: 40px auto 0;
    background: #f65f57;
}

.nav li {
    position: relative;
    display: inline-block; /*重要，不然无法在一行显示*/
    padding: 0 16px;
    font-size: 13px;
    text-shadow: 1px 2px 4px rgba(0, 0, 0, 0.5);
    list-style: none outside none;
}

.nav a {
    color: white;
}

.nav a:hover {
    color: yellow;
    font-size: 120%;
}

```

效果：

![your text](http://img.hksite.cn/1473814127872)

## 增加圆角

```css

.nav{
    ……
    /*增加圆角和阴影*/
    border-radius:10px;
    box-shadow:0px 4px 1px #B64B41;
}
```

![your text](http://img.hksite.cn/1473814270617)

## 增加分隔线

### 直接用背景图片放到右边，或者用渐变色,但这样没办法实现阴影效果

```css3

 .nav li {
     background: linear-gradient(to bottom, rgba(100, 100, 100, 0.5), gray, rgba(100, 100, 100, 0.5))
     no-repeat right/ 1px 15px;
 }
/*使用伪元素删除最后一个分隔符*/
.nav li:last-child{
    background:none;
}
```

效果：

![your text](http://img.hksite.cn/1473814427371)



### 使用伪元素制作导航列表项分隔线

```css3

.nav li:after{
         content:"";
         width:1px;
         height:13px;
         display:block;
         position:absolute;
         top:20px;right:0;
         background:rgba(255,255,255,0.3);
         border-radius:50%;
         box-shadow: 1px 0 3px 1px rgba(0,0,0,0.3) ;
     }
/*使用伪元素删除最后一个分隔符*/
.nav li:last-child:after{
    background:none;
    box-shadow:none;
}
```



![your text](http://img.hksite.cn/1473814464473)

