---
title: tab栏切换原理
date: 2017-03-16
categories: 
- 前端技术
- 小练手
tags: 
- 小练手
- 前端技术
init: 1
---

# tab栏切换原理

## 原理

让上面标签的个数和下面盒子个数一样多，然后通过获取类/tag的方式获取双方的数组，这样他们是一一对应的关系。
![your text](http://img.hksite.cn/1489640852246)
## 步骤：

1. 循环，**对于每个上面按钮的元素绑定一个index**的值（不能直接使用i，所有的i都会指向`btn.length`
2. 对每个元素加上 `onmousemove`或`onclick`的事件
3. 在函数内**清空所有按钮的样式**，清空所有的盒子显示
4. 只留下**当前选中按钮的样式和当前盒子的显示**

```javascript
       window.onload = function(){
            var btns = document.getElementsByTagName("button");
            var divs = document.getElementById("divs").getElementsByTagName("div");
            for(var i=0;i<btns.length;i++)
            {
                btns[i].index = i;  // 难点
                btns[i].onmousemove = function(){
                    //让所有的 btn 类名清空
                    for(var j=0;j<btns.length;j++)
                    {
                        btns[j].className = "";
                    }
                    // 当前的那个按钮 的添加 类名
                    this.className = "current";
                    //先隐藏下面所有的 div盒子
                    for(var i=0;i<divs.length;i++)
                    {
                        divs[i].style.display = "none";
                    }
                    //留下中意的那个 跟点击的序号有关系的
                    divs[this.index].style.display ="block";
                }
            }
        }
```


## 代码
```html
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title></title>
    <style>
        .box {
            width: 400px;
            margin:100px auto;
        }
        .bottom div{
            box-sizing: border-box;
            width:100%;
            height: 300px;
            background-color: #fff;
            border: 1px solid #333;
            border-top: none;
            display: none;
        }
        .top{
            height: 30px;
            font-size: 0px;
        }
        .top button{
            height: 100%;
            width: 80px;
            border: none;
            border-right: 1px solid #333;
            border-top: 1px solid #333;
            background: #eee;
        }
        .top button:first-child{
            border-left: 1px solid #333;
        }

        .top .current {
            background-color: #fff;
        }

    </style>
    <script>
        window.onload = function(){
            var btns = document.getElementsByTagName("button");
            var divs = document.getElementById("divs").getElementsByTagName("div");
            for(var i=0;i<btns.length;i++)
            {
                btns[i].index = i;  // 难点
                btns[i].onmousemove = function(){
                    //让所有的 btn 类名清空
                    for(var j=0;j<btns.length;j++)
                    {
                        btns[j].className = "";
                    }
                    // 当前的那个按钮 的添加 类名
                    this.className = "current";
                    //先隐藏下面所有的 div盒子
                    for(var i=0;i<divs.length;i++)
                    {
                        divs[i].style.display = "none";
                    }
                    //留下中意的那个 跟点击的序号有关系的
                    divs[this.index].style.display ="block";
                }
            }
        }
    </script>
</head>
<body>
<div class="box">
    <div class="top">
        <button>第一个</button>
        <button>第二个</button>
        <button>第三个</button>
        <button>第四个</button>
        <button>第五个</button>
    </div>
    <div class="bottom" id="divs">
        <div style="display: block;">1号盒子</div>
        <div style="background: skyblue;">2号盒子</div>
        <div style="background: lightgreen;">3号盒子</div>
        <div style="background: violet;">4号盒子</div>
        <div style="background: coral;">5号盒子</div>
    </div>
</div>

</body>
</html>
```