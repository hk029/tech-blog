---
title: 一文看完HTML知识点
date: 2019-01-20
categories: 
- 前端技术
- html
tags: 
- html
init: 1
---
[TOC]



# html的文件结构



```html

<html>
    <head>...</head>
    <body>...</body>
</html>

```



# 写在开头

网页技术严格的三层分离：

- html就是负责描述页面的语义（**结构层**）；
- css负责描述页面的样式（**样示层/表示层**）；
- js负责描述页面的动态效果的（**行为层**）。

**html中，除了语义，其他什么都没有。**

html的作用只是增加语义！没有任何别的作用！比如：**h1标签只是给文本增加主标题的语义**，而不给文字加粗、加黑、变大，所有样式都属于css干的事情。

## 何谓语义化

通俗来讲就是：每个标签命名直接表明其用途比如，网页上的文章的标题就可以用标题标签，网页上的各个栏目的栏目名称也可以使用标题标签。文章中内容的段落就得放在段落标签中，在文章中有想强调的文本，就可以使用 em 标签表示强调等等。

讲了这么多语义化，但是语义化可以给我们带来什么样的好处呢？

1. 更容易被搜索引擎收录。
2. 更容易让屏幕阅读器读出网页内容。

## HTML语法特性

- HTML对换行不敏感，对tab不敏感

  所以HTML并不依靠缩进来表明嵌套关系，html可以进行压缩处理。

  ![](http://img.hksite.cn/2019-04-05-062712.jpg)

- 空白折叠现象

  HTML中所有的文字之间，如果有**空格、换行、tab**都将被折叠为一个空格显示。

- 标签要严格封闭

  如果标签不封闭的话，会启用自动添加闭合标签的策略，可能会引来灾难！

  ```html
  <h1>这是标题1
  <div>这是内容
  ```

  会转换成

  ```html
  <h1>
  	这是标题1
  	<div>这是内容</div>
  </h1>
  ```
## 文本级、容器级

HTML标签是分等级的，HTML将所有的标签分为两种：**容器级、文本级**。

顾名思义，**容器级的标签，里面可以放置任何东西；文本级的标签里面，只能放置文字、图片、表单元素**。

再次强调：**文本级的标签里面，只能放置文字、图片、表单元素**，不要试图在`<p>`标签里放别的标签比如`<h1>`标签。

## 块级、行内级

每个HTML元素都有一个默认的显示值，具体取决于它的元素类型。大多数元素的默认显示值是**块（block）**或**内联(inline)**。他们决定了元素默认显示模型，可以参考 重读css规范：可视化格式模型

常见**块级标签**：`<div> <form> <hx>  <hr> <dl> <dt> <ol> <ul> <li> <p> <pre> <table>`，当然html5新增了很多新的语义化标签类型：`<header> <footer> <aside> <nav>...`，还有很多新特性功能性的块级标签: `<canvas> <svg> <video>`

常见**行内标签**：`<a> <br> <button> <code> <em> <strong> <i> <img> <input> <label> <q>  <script> <select> <span> <sub> <sup> <textarea> ... `

## 转义字符

默认情况下，`< >`都会被自动识别成标签部分内容，然后空白字符又会自动折叠，因此需要对这些特殊符号进行转换：

```
< : &lt;
> : &gt;
空格：&nbsp;
换行：使用<br>
“： &quot;
&：&amp;
©: &copy;

```

# head [c]

head部分主要有：`<title>、<script>、 <style>、<link>、 <meta>`

## meta 

meta是`自封闭标签`，不需要加闭合标签。里面可以定义：

- 字符集 **charset** utf-8  / gb2312 ……

- 关键字 **Keywords**  SEO搜索引擎优化

- 页面描述 **Description** SEO搜索引擎优化

- 页面缩放……


## title

title标签是网页的标题信息，可以用来SOE

![image-20190405144422924](http://img.hksite.cn/2019-04-05-064425.png)


# body

body部分主要有：`<h1>、<p>、<a>、<img>`等网页内容标签，**在这里的标签中的内容会在浏览器中显示出来**。


## hx  [B]

`<hx>`标签是标题标签，表示文章标题（x=1-6）


```vbscript-html
<h1>一级标题</h1>
<h2>二级标题</h2>
<h3>三级标题</h3>
<h4>四级标题</h4>
<h5>五级标题</h5>
<h6>六级标题</h6>
<h7>七级标题（不存在）</h7>

```

![](http://img.hksite.cn/2019-04-05-064255.png)


## p [B]


作用：p标签是段落标签，**文本级**，不要试图在`<p>`标签里放别的标签比如`<h1>`标签

语法：`<p>段落文本</p>`


## em/strong [I]


作用：强调文字，默认em是斜体，strong是粗体，都可以用css调整

用法：`<em>强调文本</em>`

用法：`<strong>强调文本</strong>`


## q [I]

作用：q标签是引用标签，**引用的文字会自动加上引号**

用法：`<q>引用文本</q>`

![](http://img.hksite.cn/2019-04-05-065838.png)


## blockquote [B]

作用：长引用标签，默认做法是缩进

用法：`<blockquote>引用文本</blockquote>`

![image-20190405145906272](http://img.hksite.cn/2019-04-05-065906.png)

## br [I]


作用：换行 ( break ) **自封闭**

用法：`<br />`

注意：很多时候，应该用`<p>`代替`<br>`，如果文字需要换行，那它应该是另一个文本块。

## hr [B]


作用：分隔线 ( horizontal )

用法：`<hr>`


## code [I]

作用：代码，防止浏览器误认为是要执行代码，而没显示代码。加了标签浏览器就不会执行了，而是像文本一样显示出来

用法：`<code>代码语言</code>`

![](http://img.hksite.cn/2019-04-05-070136.png)

## pre [I]

作用：在处理多段预览文字或代码时使用（**保证显示的格式**，不需要`<br />`或`&nbsp`控制格式）

用法：`<pre>语言代码段</pre>`

```html
<pre>
let a = 1;
let b = a + 1;
if (b > a) {
  ...
}
</pre>
```

![](http://img.hksite.cn/2019-04-05-070258.png)

## a [I]

### **作用1**：实现超链接

用法：`<a  href="目标网址"  title="鼠标滑过显示的文本" target="_blank">链接显示的文本</a>`

`target`属性表示是在当前窗口还是新窗口打开，`_blank`表示新窗口，还有`_self`自身窗口，`_top`顶部框架,`_parent`父框架。


### **作用2**：链接Email地址**mailto**

![your text](http://img.hksite.cn/1473842446659)


```html

<a href="mailto: hk2291976@hotmail.com ? cc=abc@hotmail.com ; bb@hotmail.com & subject=主题 & body =邮件内容 ">发送邮件</a>

```

### **作用3**：页面锚点


锚点用name属性来设置，一个a标签如果name属性（或者id属性），那么就是页面的一个锚点。

```html

<a name="foot">跳转到文末</a>

或者：

<a id="foot">跳转到文末</a>

```

那么网址：`html#foot`就能够让这个锚点在页面最末端显示，此时页面有卷动。

同时如果有一个超链接，也可以跳转到锚点


```html

	<a href="#foot">点击我就查看我的作品</a>

```


**注意：**如果需要一个段落内文字都被点击，那么应该是用**p包裹a**而不是a包裹p，**a的语义要小于p，a就是可以当做文本来处理，所以p里面相当于放的就是纯文字。**

## img [I]

作用：插入图片，**自封闭标签，也称为单标签**

语法：`<img src="图片地址" title="鼠标滑过显示文字" alt="下载失败时的替换文本" title = "提示文本">`

**注意：**src可以使用相对或绝对路径，相对路径的`..`只能写在开头



# 列表

## ul-li [B]

作用：**无序**列表显示，**导航条**，内容列表

用法：

```html

<ul>
  <li>新闻1</li>
  <li>新闻2</li>
   ......
</ul>

```

效果：

![your text](http://img.hksite.cn/1473841390637)


**注意**：ul里面只能放li。**ul的作用，并不是给文字增加小圆点的，而是增加无序列表的“语义”的**。同时li是一个**容器级**标签，里面啥都可以放(div，p，hx，ul，img……)！！这也是为什么li经常用来做导航/内容列表





## ol-li [B]

作用：**有序**列表显示

用法：


```html

<ol>
  <li>新闻1</li>
  <li>新闻2</li>
   ......
</ol>

```

效果：

![your text](http://img.hksite.cn/1473841501816)


注意：用的不多，除非是非要自动编号的场景。




## dl-dt-dd [B]


作用：带**标题**的列表显示用法：


用法


```html
<dl>
   <dt>计算机</dt>
   <dd>用来计算的仪器 ... ...</dd>
   <dt>显示器</dt>
   <dd>以视觉方式显示信息的装置 ... ...</dd>
</dl>

```

效果：![your text](http://img.hksite.cn/1488868869467)


注意：基本都可以用**div+ul**来代替，根据场景需要使用，基本所有列表的使用方法都可以转换。

# 表格

## table [B]

作用，产生一个表格

用法：主要是`table、tbody、tr、th、td`四元素的使用

`<table>`表示一个表格

`<tbody>`表示这个表格内容是一个整体（一次显示而不是多次加载）

`<tr>`表示一行

`<td>`表示一个单元格

`<th>`表示**表头**

注意：默认是不代框的，如果加框可以用css样式


```html

<table>
  <tbody>
    <tr>
      <th>班级</th>
      <th>学生数</th>
      <th>平均成绩</th>
    </tr>
    <tr>
      <td>一班</td>
      <td>30</td>
      <td>89</td>
    </tr>
    <tr>
      <td>二班</td>
      <td>35</td>
      <td>85</td>
    </tr>
  </tbody>
</table>

```

![your text](http://img.hksite.cn/1473841886075)



## caption

作用：表格标题

## summary
作用：表格



# 表单

## form

作用：表单数据，用于和用户交互

语法：`<form   method="传送方式"   action="服务器文件">`

传送方式：`POST`,`GET`

## label


作用：显示文本和**关联控件**

语法：`<label for="关联名字">显示文本</label>`

**注意：**用`for`属性可以关联控件的**id**，有些像单选多选框这类不易点击的，可以用label关联


```html

<form>
   <label for="male">男</label>
  <input type="radio" name="gender" id="male" />
  <br />
  <label for="female">女</label>
  <input type="radio" name="gender" id="female" />
  <br />
</form>

```

这样就可以点击“男”或“女”触发点击单选的功能

![your text](http://img.hksite.cn/1473845077992)

**注意**：什么表单元素都可以有label。


## input

作用：根据type类型可以展现出不同的表现形式
用法：`<input type="类型">显示文本</input`

type类型：

- `text`: 文本类型，**value**里放值，可以显示在文本框里（**作默认值**）

- `password`: 密码类型

- `radio`：name="name" **name属性相同**的单选框

- `checkbox`：name="name" **name属性相同**的单选框复选框

- `file`: 选择文件

- `button`：按钮（定义onclik触发消息），和button标签实现功能一样

- `submit`：提交数据，自动有“提交”文字，点击这个表单就会被提交到form标签的action属性中的那个页面中去

- `reset` : 重置数据


HTML5中还加入了例如`datatime,date,color,tel,range,number……`等类型

[详细信息](http://www.monmonkey.com/rumenpian/html/input.html)


**注意：**在HTML5中，可以用`placeholder`属性作为占位符，可以实现提示信息，input的**值都需要通过value**设置和取得。


![your text](http://img.hksite.cn/1473844238263)







## textarea

作用：文本域，支持多段文本
用法：`<textarea  rows="行数" cols="列数">文本</textarea>`

**注：**属性可以通过css修改`weight`和`height`更改，或者通过`cols`或`rows`更改。

## select-option

作用：选择框，**节约空间**

用法：`<option value='提交值' selected="selected">显示值</option>`

其中：`selected="selected"`表示选中
```
    <label>爱好:</label>

    <select>
      <option value="看书">看书</option>
      <option value="旅游" selected="selected">旅游</option>
      <option value="运动">运动</option>
      <option value="购物">购物</option>
    </select>
```

![your text](http://img.hksite.cn/1473844600847)

注意：下拉框可以多选，`select`启用属性`multiple="multiple"`,然后在选择的时候，按`ctrl`（mac上`command`），就可以多选

![your text](http://img.hksite.cn/1473844735310)



# 用来布局的两大盒子

## div

作用：**最常用的标签之一**，是**最重要的容器**（盒子），划分（division ），可以根据逻辑组成把页面划分成不同部分，然后通过css对不同部分进行装饰。（就像屋内的房间划分一样）

用法：`<div>…</div>`

![image-20190405151907354](http://img.hksite.cn/2019-04-05-071916.png)

由于div太常用了，所以，我们亲切的称呼这种模式叫做“**div+css**”。div标签负责布局，负责结构，负责分块。css负责样式。

## span

作用：**最常用的标签之一**，也是**很重要的盒子**，它是**文本级的标签**，span标签为文字单独设置样式（通过css）

用法：`<span>单独处理文本</span>`

可以说，span里面是放置文本元素的，div里面放置布局元素的