---
title: css3选择器
date: 2016-10-14
categories: 
- 前端技术
- css
tags: 
- css
- css3
init: 1
---
css3选择器引入了通配符的概念，和jQuery一样可以使用^，$操作，用法也类似

## 使用说明：

![your text](<http://img.hksite.cn/1473815245277> "")

## 实例

html

```html
<a href="xxx.pdf">我链接的是PDF文件</a>
<a href="#" class="icon">我类名是icon</a>
```


css

```css
a[class^=icon]{
  background: green;
  color:#fff;
}
a[href$=pdf]{
  background: orange;
  color: #fff;
}
a[title*=more]{
  background: blue;
  color: #fff;
}
```

<p>[codepen_embed height="265" theme_id="dark" slug_hash="LbvzOd" default_tab="css,result" user="voidsky"]See the Pen &lt;a href='<a href="http://codepen.io/voidsky/pen/LbvzOd/">http://codepen.io/voidsky/pen/LbvzOd/</a>'&gt;LbvzOd by HuangKai (&lt;a href='<a href="http://codepen.io/voidsky">http://codepen.io/voidsky</a>'&gt;@voidsky) on &lt;a href='<a href="http://codepen.io">http://codepen.io</a>'&gt;CodePen.[/codepen_embed]</p>