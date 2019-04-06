---
title: 优化js轮播图效果
date: 2017-03-25
tags:
- 前端技术
- 小练手
- 轮播图
---

## 之前版本缺陷

之前实现的轮播图有个缺点就是需要**补一张图**在最后才能实现无缝轮播，而且如果做成焦点图，会有一个缺点就是如果横跨张数过多的时候，滚动的速度很快，体验不好，其实还有一种轮播图的实现方案，**可以实现不复制第一张实现任意张数的轮播，而且不论跨多少张点击，都只会显示一次滚动动画。**

## 实现效果

最后实现的效果就是如下图：

![your text](http://img.hksite.cn/1490342102286)



## 页面架构

页面还是比较容易的，就是外面一个大盒子，然后里面套两个小盒子，其中一个用来控制幻灯片播放，下面的盒子用来做控制。

```html
    
<div class="w-slider" id="w_slider">
        <div class="slider">
            <ul class="slider-main">
                <li class="slider-main-img">
                    <a href="#"><img src="images/1.png" alt="" /></a>
                </li>
                <li class="slider-main-img">
                    <a href="#"><img src="images/2.png" alt="" /></a>
                </li>
                <li class="slider-main-img">
                    <a href="#"><img src="images/3.png" alt="" /></a>
                </li>
                <li class="slider-main-img">
                    <a href="#"><img src="images/4.png" alt="" /></a>
                </li>
                <li class="slider-main-img">
                    <a href="#"><img src="images/5.png" alt="" /></a>
                </li>
        
            </ul>
        </div>
        <div class="slider-ctrl" id="slider_ctrl">
            <span class="slider-ctrl-prev" style="display: none;"></span>

            <span class="slider-ctrl-next" style="display: none;"></span>
        </div>
    </div>
```

这里，注意，我们不再是用一个拼接的长图，而是**所有的图片都使用绝对定位**。

我们的控制条和左右箭头使用的是精灵图，这里底部的控制按钮使用的`inline-block`，方便居中。

左右箭头需要使用**绝对定位，定位到图片的两侧**。（需要对大盒子进行相对定位）

```css
 * {
        margin: 0;
        padding: 0;
    }
    ul{list-style: none;}
    img {
        vertical-align: top;
    }
    .w-slider{
        width:400px;
        height: 245px;
        margin: 100px auto;
        overflow: hidden;
        position: relative;

    }
    .slider{
        width: 400px;
        height: 200px;
        position: relative;
    }
    .slider-main-img{
        position: absolute;
        top:0;
        left:0;
    }
    .slider-ctrl{
        margin-top:10px;
        padding-left:10px;
        text-align: center;
        font-size:0;
    }
    .slider-ctrl-con{
        width:24px;
        height: 20px;
        background: url(images/icon.png)-24px -782px;
        display: inline-block;
        cursor: pointer;
        margin-right:10px;
    }
    .slider-ctrl-con.current{
        background: url(images/icon.png)-24px -762px;
    }
    .slider-ctrl-next,
    .slider-ctrl-prev{
        width: 30px;
        height: 36px;
        position: absolute;
        background: url(images/icon.png) no-repeat 6px top;
        top:92px;
        left:0;
        cursor: pointer;
        opacity:0.6;
    }
    .slider-ctrl-next{
        right:0;
        left:auto;
        background-position:-6px -44px;
    }
```



## 逻辑

关键是逻辑实现，由于我们的图片全是使用的绝对定位。所以如果要实现一张图片的进入和退出，就是这两张的left变化。

考虑向左移动的实现。

其实就是当前框内的图片**1缓慢移动到左侧**，然后它后面的图片**2立马出现在右侧**，然后**2缓慢移动进去**，由于2出现在右侧的速度很快，加上动画效果，**基本感觉就是连续的。**

![your text](http://img.hksite.cn/1490342779711)

所以，利用这个原理，可以把任意图片放在1的右边。这样就可以**实现任意图片从1的右侧滑入**。

![your text](http://img.hksite.cn/1490342984878)

**同时改变一下控制条的变化就行了。**



同理，实现向右移动。

## 代码

### 自动生成控制条

首先，我们所有的控制按钮都是根据图片数量**动态生成**的，本身只有两个按钮（左右）

```javascript
   //动态增加下面的按钮
    for(var i = 0;i < s_len;i++)
    {
        var span = document.createElement('span');
        span.className = 'slider-ctrl-con';
        s_ctrl.insertBefore(span,cons[0]);  //把新增的全插到箭头前面，保证编号和幻灯片一致
    }
```

### 左右移动功能

根据之前的逻辑分析，容易写出代码

```javascript
    //右箭头的功能其实就是将当前图片移动到左侧，下一张（cur+len+1)%len移动到中间。
    function moveLeft(to_index) {
        var to = (to_index == undefined) ? (cur + s_len + 1 )%s_len:to_index;
        //当前页移动到左侧
        animate(sliders[cur],{left:-scrollWidth},t);
        cons[cur].classList.remove('current');  //控制条变化
            //先把它移动到右侧
        sliders[to].style.left = scrollWidth + 'px';
            //再动画移动到中间
        animate(sliders[to],{left:0},t);
        cons[to].classList.add('current');
        cur = to;
    }
 //同上
    function moveRight(to_index){
        var to = (to_index == undefined) ? (cur + s_len - 1 )%s_len : to_index;
        animate(sliders[cur],{left:scrollWidth},t);
        cons[cur].classList.remove('current');
        sliders[to].style.left = -scrollWidth + 'px';
        animate(sliders[to],{left:0},t);
        cons[to].classList.add('current');
        cur = to;
    }
```

### 自动播放

自动播放其实就是定时调用**moveLeft**功能

```javascript
function autoPlay(){
        timer = setInterval(moveLeft,2000);
    }
```

### 鼠标移动到盒子内

鼠标移动到盒子内，显示左右箭头，并且停止自动播放，移出隐藏左右箭头，开始自动播放。

```javascript
 //两侧箭头的显示和隐藏
    slider_box.onmouseover = function () {
        cons[cons.length-2].style.display = 'block';
        cons[cons.length-1].style.display = 'block';
        clearInterval(timer);  //停止自动播放
    };
    slider_box.onmouseout = function () {
        cons[cons.length-2].style.display = 'none';
        cons[cons.length-1].style.display = 'none';
        autoPlay();  //开始自动播放
    };
```

### 所有按钮点击

循环一遍绑定所有click事件，首先给每个按钮绑定一个`index`，方便控制对应的幻灯片。

- 如果是左右箭头，直接调用**moveLeft**和**moveRight**函数。
- 如果点击的编号大于当前编号说明要**向左移动**（控制按钮变化和图片滚动方向相反）
- 如果点击的编号小于当前编号说明要**向右移动**

```javascript
  //对所有的元素进行操作
    for(var i = 0;i < cons.length;i++) {
        cons[i].index = i;
        cons[i].onclick = function () {
            if(this.className =='slider-ctrl-prev') {
                moveRight();
            }else if(this.className =='slider-ctrl-next') {
                moveLeft();
            }
            else if (this.index > cur) {
                moveLeft(this.index);
            }
            else if (this.index < cur) {
                moveRight(this.index);
            }
        }
    }
```



### 整体代码

```javascript
/**
 * Created by voidsky on 2017/3/23.
 */
window.onload = function() {
    // 获取元素
    var slider_box = $('#w_slider');
    var sliders = $('.slider-main-img'),
        s_len = sliders.length;
    var s_ctrl = $('#slider_ctrl'),
        cons = s_ctrl.children;
    var timer = null;
    var scrollWidth = slider_box.offsetWidth;
    var cur = 0;
    var t = 20;

    //动态增加下面的按钮
    for(var i = 0;i < s_len;i++)
    {
        var span = document.createElement('span');
        span.className = 'slider-ctrl-con';
        s_ctrl.insertBefore(span,cons[0]);  //把新增的全插到箭头前面
    }
    //两侧箭头的显示和隐藏
    slider_box.onmouseover = function () {
        cons[cons.length-2].style.display = 'block';
        cons[cons.length-1].style.display = 'block';
        clearInterval(timer);  //停止自动播放
    };
    slider_box.onmouseout = function () {
        cons[cons.length-2].style.display = 'none';
        cons[cons.length-1].style.display = 'none';
        autoPlay();  //开始自动播放
    };

    for(i = 1;i < s_len;i++) //初始化，第一张不动，其他的所有图片都跑到右边去
    {
        sliders[i].style.left = scrollWidth + 'px';
    }
    cons[cur].classList.add('current');
    autoPlay();
    //原则：左边的编号永远比右边的编号小
    //先实现自动播放，也就是右箭头的功能
    //右箭头的功能其实就是将当前图片移动到左侧，前一张（cur+len-1)%len移动到中间。
    //当移动到最后一张的时候，就自动把所有的图片都加载到右边
    function moveLeft(to_index) {
        var to = (to_index == undefined) ? (cur + s_len + 1 )%s_len:to_index;
        //当前页移动到左侧
        animate(sliders[cur],{left:-scrollWidth},t);
        cons[cur].classList.remove('current');
            //先把它移动到右侧
        sliders[to].style.left = scrollWidth + 'px';
            //再动画移动到中间
        animate(sliders[to],{left:0},t);
        cons[to].classList.add('current');
        cur = to;
    }
    //同上
    function moveRight(to_index){
        var to = (to_index == undefined) ? (cur + s_len - 1 )%s_len : to_index;
        animate(sliders[cur],{left:scrollWidth},t);
        cons[cur].classList.remove('current');
        sliders[to].style.left = -scrollWidth + 'px';
        animate(sliders[to],{left:0},t);
        cons[to].classList.add('current');
        cur = to;
    }

    function autoPlay(){
        timer = setInterval(moveLeft,2000);
    }

    //对所有的元素进行操作
    for(var i = 0;i < cons.length;i++) {
        cons[i].index = i;
        cons[i].onclick = function () {
            if(this.className =='slider-ctrl-prev') {
                moveRight();
            }else if(this.className =='slider-ctrl-next') {
                moveLeft();
            }
            else if (this.index > cur) {
                moveLeft(this.index);
            }
            else if (this.index < cur) {
                moveRight(this.index);
            }
        }
    }
}

```
