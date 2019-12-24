---
title: 利用@font-face嵌入字体
date: 2016-10-29
categories: 
- 前端技术
- css
tags: 
- css
- 常用css技巧
- font
init: 1
---

# @font-face
@font-face能够加载服务器端的字体文件，让浏览器端可以显示用户电脑里没有安装的字体。

## 使用方法
```css3
@font-face {
    font-family : 字体名称;
    src : 字体文件在服务器上的相对或绝对路径;
}
```
## 相关网站推荐
几个比较大的字体网站：
- 首推google
https://font.google.com

- adobe
https://edgewebfonts.adobe.com/fonts

- 有字库
http://www.youziku.com/

- fontsquirrel
这里有个字体转换器
https://www.fontsquirrel.com/fonts/list/hot

- dafont
这里面还有大量字体图标
http://www.dafont.com