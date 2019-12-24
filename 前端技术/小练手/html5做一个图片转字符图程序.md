---
title: 用html5做一个图片转字符图
date: 2017-04-11
categories: 
- 前端技术
- 小练手
tags: 
- 小练手
- 前端技术
- 项目
init: 1
---

放在以前，纯用前端js操作图片还是不可想象的，现在随着html5的发展，开放了文件api和canvas之后，操作图片变成了可能。

测试地址：[点击测试](http://hksite.cn/project/img2ch.html)

## 思路

1. 利用html5提供的`FileReader()`接口去读取图片。
2. 将读取的图片数据放到`canvas`之中，然后利用canvas提供的`getImageData`函数读取图片内容。
3. 将图片内容转换成灰度值。
4. 通过不同的灰度值选取不同的字符，显示。

## 基础知识

### input:file

这个DOM节点在读取文件后会填充其files属性（File数组），通过读取这个属性可以获取File对象。

```javascript
var file = $('#file');
var ifile = file.files[0];
```

### FileReader()

FileReader这个Object内部有3个方法用来读取文件：

- `FileReader.readAsBinaryString()`
- ``FileReader.readAsArrayBuffer()`
- `FileReader.readAsDataURL()`
- `FileReader.readAsText()`

一般来说图片使用`readAsDataURL()`, 文本文件采用`readAsText()`，二进制文件采用`readAsBinaryString()`

```javascript
var rFile = new FileReader();
rFile.readAsDataURL(ifile);
```

参数都是`Blob`或`File`对象。

读取文件后，会触发一个`onload`的事件，所以需要设置一个回调函数，读的数据结果在`result`里。

```javascript
 rFile.onload = function(){
	 console.log(this.result)    
 }
```

### 设置canvas

要使用canvas的时候，首先需要指定里面的上下文环境是2d还是3d：

```javascript
var cv = $('#cv');
var ctx = cv.getContext('2d')，
```

然后，通过ctx去画图，里面有大量有关绘图的方法（矩形，文字，路径，弧线……）

这里我们使用`drawImage()`给canvas填充图片。

### ctx.getImageData()

会返回一个`ImageData`的对象，里面有个属性是`data`，这个属性是一个数组（包含rgba像素点），每个像素占4个单位（r,g,b,a）。

### rgb和灰度转换

固定公式：0.299 * r + 0.578 * g + 0.114 * b;

## 问题

这里面会遇到一些问题：

1. **像素和字符的关系**，如果把每个像素点显示为一个字符，肯定是不靠谱的，因为这会让输入的字符画太大！

   一般来说，我们可以设置字符大小为12px，这时候，每个字符的高12px，宽6px，那么我们可以用一个字符代表12*6的像素点。

2. **判断图片格式**，在读文件的时候要进行格式判断，确保是图片，可以使用正则做。

3. **字符大小不一致**，必须使用等宽字体，否则英文字符的宽度不一致，可以使用 '宋体'/'monospace'

## 布局

```html
 <input type="file" name="" id="file" onchange="loadFile();">
    <div>
        <canvas id="cv"></canvas>
    </div>
    <div id="charPic"></div>
```

## js代码

```javascript
  function $(params) {
            return document.querySelector(params);
        }

        var rFile = new FileReader();
        var file = $('#file');
        var content = $('#content');
        var chPic = $('#charPic');
        var cv = $('#cv'),
            ctx = cv.getContext('2d');
        var img = document.createElement('img');  //创建一个放图片的地方
        
        var rFilter = /^(?:image\/bmp||image\/gif|image\/jpeg|image\/jpg|image\/png|image\/svg\+xml|image\/tiff)$/i;

        function loadFile() {
            var ifile = file.files[0];
            if (rFilter.test(ifile.type)) {
                rFile.readAsDataURL(file.files[0]);
            } 
        }

        rFile.onload = function (oFREvent) {
                img.src = this.result;
                content.innerText = "";
        };

        //当图片载入完毕后执行转换操作
        img.onload = function() {
            //获取img的长宽，为后面的getImageData提供参数
            var w = img.width,
                h = img.height;
            //把canvas的布局做出来
            cv.width = w;
            cv.height = h;
            //把图片数据放进去
            ctx.drawImage(img, 0, 0);
            //开始读取图片数据
            var imgData = ctx.getImageData(0, 0, w, h),
                imgArr = imgData.data;
            var cur = 0;
            var p = '<p>';
            var html = '';
            //每12*6个像素点做一次灰度画，用一个字符代替
            for (var i = 0; i < h; i += 12) {
                var p = '<p>';
                for (j = 0; j < w; j += 6) {
                    //每个像素点占4个单位
                    var index = (j + w * i) * 4;
                    var r = imgArr[index];
                    var g = imgArr[index + 1];
                    var b = imgArr[index + 2];
                    var gray = getGray(r, g, b);
                    p += toText(gray);
                }
                p += '</p>';
                html += p;
            }
            chPic.innerHTML = html;
        }
        //转换字符，这里字符选取的规则是由复杂到简单（空白由少到多，体现灰度关系）
        function toText(g) {
            //字符可以自由替换
            var charList = ['$','&','%','*','c','o','+','.','&ensp;'];
            var len = charList.length;
            var step = 255/len;
            return charList[parseInt(g/step)];
        }
        //灰度转换公式
        function getGray(r, g, b) {
            return 0.299 * r + 0.578 * g + 0.114 * b;
        }

```

## 结果

![your text](http://img.hksite.cn/1491824297741)

![your text](http://img.hksite.cn/1491824428651)