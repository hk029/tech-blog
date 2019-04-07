---
title: 重读css规范：可视化格式模型
date: 2019-04-07
categories: 
- 前端技术
- css
tags: 
- css
- css规范
init: 1
---
# 重读css规范：可视化格式模型

> 本文是重读css规范——可视化格式模型部分的笔记，增加了一些个人见解和扩展。
>
> 规范文档地址：<https://www.w3.org/TR/CSS22/visuren.html>>

## 介绍

可视化格式模型：对于可视化媒体，UA怎样处理文档树

在可视化格式模型中，文档树中的每个元素根据其盒模型生成0个或多个盒。这些盒的布局由（以下因素）控制：

- **盒尺寸**与**类型**
- **定位模式**（常规流，浮动与绝对定位）
- 文档树中**元素间的关系**
- 外部信息（例如，视口大小，图片的内在尺寸等等）

## 视口(viewport)

[连续媒体](http://www.ayqy.net/doc/css2-1/media.html#continuous-media-group) (对应分页媒体) 的UA一般会给用户提供一个视口viewport（屏幕上的一个窗口或者视图区域），用户通过它来查阅文档。视口尺寸变化（见[初始包含块](http://www.ayqy.net/doc/css2-1/visudet.html#containing-block-details)）时，**用户代理可能会改变文档的布局**，当视口比渲染文档的画布区域小时，**用户代理应该提供一种滚动机制**。一个画布（canvas）最多对应一个视口，但用户代理**可能会渲染到多个画布上**（即提供同一文档的不同视图)





## 包含块（containing block）

包含块是一个很重要的概念，许多盒子的位置和大小是根据一个称为“包含块”的矩形框的边缘计算的。

通常，生成的方块充当子代方块的块；我们说一个**盒子为它的后代建立了包含块**。短语“一个盒子包含的块”是指盒子所包含的那个块，而不是它所生成的那个块。

### 包含块规则

**没啥特殊的规则，都是之前知道的，注意的就是绝对定位的盒是根据padding盒而不是content盒边**

1. [根元素](http://www.ayqy.net/doc/css2-1/conform.html#root)所在的包含块是一个被称为**初始包含块的矩形**。对于连续媒体，尺寸取**视口**的尺寸，并且被固定在画布开始的位置；对于分页媒体就是**页区（page area）**。初始包含块的'direction'属性与根元素的相同

2. 对于其它元素，如果该元素的position是'relative'或者'static'，包含块由其最近的**块容器**祖先盒的**content边**形成

3. 如果元素具有'position: fixed'，包含块由连续媒体的**视口**或者分页媒体的**页区**建立

4. 如果元素具有'position: absolute'，包含块由最近'**position**'为'absolute'，'relative'或者'fixed'的祖先建立，按照如下方式：

   1. 如果该祖先是一**个内联元素**，包含块就是环绕着为该元素生成的**第一个**和**最后一个**内联盒的**padding box的边界框（bounding box）**。在CSS 2.1中，如果该内联元素被跨行分割了，那么包含块是未定义的
   2. 否则，包含块由该祖先的[padding边](http://www.ayqy.net/doc/css2-1/box.html#padding-edge)形成

   如**果没有这样的祖先，包含块就是初始包含块**

每个盒子的position都是依赖其包含块的，但是它不受这个包含块的限制，它可能会溢出（overflow）。



### *控制盒子生成

[块级元素]()是源文档中那些被格式化成**视觉上的块**的元素（例如，p）。

`display`属性的下列值能让一个元素变成块级的：'`block`'，`list-item`和`table`



[块级盒]()是参与**块格式化上下文**(**BFC**)的盒。每个块级元素生成一个 **主块级盒**(principal block-level box)，用来包含后代盒和生成的内容，并且**任何定位模式**都与该盒有关。有些块级元素可能生成除主盒外的**额外的盒**：'list-item'元素。这些额外的盒根据主盒来放置



除了后面章节描述的**表格盒**与**可替换元素**外，一个**块级盒**也是一个**块容器盒**。一个块容器盒要么只包含块级盒，要么建立了**内联格式化上下文(IFC)**并因此只包含内联盒。



不是所有的块容器盒都是块级盒：**不可替换的内联块**和**不可替换的表格单元**是块级容器，但不是块级盒。作为**块级容器的块级盒**也叫**块盒（block box）**

**块盒 要求 比块级盒更加严格**

三个术语“`块级盒（block-level box）`”，“`块容器盒（block container box）`”与“`块盒（block box）`”在没有歧义的时候就简称为“块（block）”

### *匿名块盒

#### 块级盒包含内联和块级盒

在一个这样的文档中：

```
<DIV>
  Some text
  <P>More text
</DIV>
```

（假设DIV和P都有'display: block'），DIV**似乎既有内联内容也有块内容**。为了更容易定义格式，我们假设在"Some text"四周有一个***匿名块盒***

![your text](http://img.hksite.cn/1499844512098)

换句话说：**如果一个块容器盒（例如，为上面的DIV生成的）里面有一个块级盒（例如上面的P），那么我们强制让它里面只含有块级盒** 



#### 内联盒包含块级盒

当一个**内联盒**包含一个流内**块级盒时**，该**内联盒**（及与它处于同一行盒里的内联祖先）会被从周围的块级盒（和任何连续的，或者仅仅被可折叠的空白字符和/或流外元素隔开的块级兄弟）中**拆分出来**，把内联盒**分成两个盒**（即使有一边是空的），**分别位于块级盒的两边**。



**拆分前的行盒**与**拆分后的行盒**都被包进**匿名块盒**，并且该块级盒**成为了这些匿名盒的兄弟**。当这样一个内联盒受到相对定位的影响时，**任何由此产生的转变也会影响内联盒里面的块级盒**

```css
p    { display: inline }
span { display: block }
```

被应用在如下HTML文档的话：

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN">
<HEAD>
<TITLE>Anonymous text interrupted by a block</TITLE>
</HEAD>
<BODY>
<P>
This is anonymous text before the SPAN.
<SPAN>This is the content of SPAN.</SPAN>
This is anonymous text after the SPAN.
</P>
</BODY>
```

![your text](http://img.hksite.cn/1499845936333)

产生的盒将会是一个代表BODY的块盒，包含一个环绕着C1的匿名块盒，SPAN块盒和另一个环绕着C2的匿名块盒

匿名盒的属性是从**包围**它的**非匿名盒继承**来的，不可继承的属性，取其初始值。例如，匿名盒的字体是从那个DIV继承的，但margin将为0

**设置在该元素上的属性仍然会应用于该元素生成的匿名盒上**。例如，如果一个border被设置在上例中的P元素上，border将会绘制在C1（在行结束的位置打开）和C2（在行开始的位置打开）周围

有些用户代理以它们的方式给内联包含块实现了border。

在处理相关的百分比值时，**匿名块盒会被忽略**：**用最近的非匿名祖先盒代替**。例如，如果上面DIV里面的匿名块盒的子级为了处理百分比高度，需要知道其包含块的包含块的高度，那么它将采用DIV形成的包含块的高度，而不是匿名块盒的



## 内联级元素(Inline-level elements)与内联盒( inline boxes)

内联级元素是源文档中那些不会为其内容形成新块的元素，内容分布于行中（例如，强调段落中的一部分文本，内联图片等等）。



'display'属性的下列值能让一个元素变成**内联级**：'`inline`'，`'inline-table`'和'`inline-block`'。



**内联级元素可以生成内联级盒（inline-level boxes）**，即参与**内联格式化上下文（IFC）**的盒



一个内联盒（inline-box）是一个（特殊的）内联级盒，其**内容**参与了它的包含**内联格式化上下文**。



一个'display'值为'**inline**'的**不可替换元素**会生成一个**内联盒**。



不属于内联盒的内联块级盒（例如，**可替换内联级元素**，inline-block元素和inline-table元素）被称为**原子内联级盒**（atomic inline-level boxes），因为它们作为单一的不透明盒（opaque box）参与其内联格式化上下文（类似**块盒**）



内联盒》内联级盒



#### 匿名内联盒

任何被直接包含在一个块容器元素中（不在一个内联元素里面）的**文本**，必须视为一个**匿名内联元素**

在一个有如下HTML标记的文档里：

```
<p>Some <em>emphasized</em> text</p>
```

`<p>`会生成一个块盒，里面还有几个内联盒：

- "emphasized"的盒是一个由内联元素（`<em>`）生成的**内联盒**
- 其它盒（"Some"和"text"）都是由块级元素（`<p>`）生成的**匿名内联盒**，因为它们没有与之相关的内联级元素

这种匿名内联盒从它们**父级块盒继承了可继承的属性**，不可继承的属性取其初始值。示例中，匿名内联盒的颜色是从P继承的，但背景是透明的



后续将根据white-space属性合并起来的空白字符内容，不会生成任何匿名内联盒





## display属性

![your text](http://img.hksite.cn/1499848706595)

- 不可继承，不可设置百分比
- 默认inline
- 属性：
  - **block**：该值会让元素生成一个块盒
  - **inline-block**：该值会让元素生成一个**内联级块容器**（inline-level block container）。一个inline-block的内部会被**格式化成一个块盒**，而该元素本身会被格式化成一个**原子内联级盒**
  - **inline**：该值会让元素生成一个或多个内联盒
  - **list-item**：该值会让元素（例如，HTML中的LI）生成一个**主块盒**（principal block box）和一个**标记盒**（marker box）。
  - **none**：该值会让元素不在格式化结构（formatting structure）中出现（即在可视化媒体中，**元素不会生成盒也不会影响布局**）。**后代元素也不会生成任何盒**，该元素及其内容会**从格式化结构中全部移除**。这种行为**不能通过给后代设置'display'属性来重写**

 

**注意**：display为none时**不会创建一个不可见的盒，它根本不会创建任何盒**。CSS提供了一种让一个元素在格式化结构中生成能影响格式化的但本身不可见的盒的机制。详细信息请查看可见性章节



**注意**：尽管['display'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-display)的[初始值](http://www.ayqy.net/doc/css2-1/about.html#initial-value)是'inline'，用户代理[默认样式表](http://www.ayqy.net/doc/css2-1/cascade.html#default-style-sheet)中的规则可能会[重写](http://www.ayqy.net/doc/css2-1/cascade.html)该值。见附录中HTML 4的[样例样式表](http://www.ayqy.net/doc/css2-1/sample.html)