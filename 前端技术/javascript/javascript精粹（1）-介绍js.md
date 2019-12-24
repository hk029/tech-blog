---
date: 2016-12-12
categories: 
- 前端技术
- javascript
tags:
- javascript精粹
- Javascript
title: javascript精粹（1）-介绍js
---

# 分析js

js是建立在一些非常优秀的想法和少数非常糟糕的想法之上的

## 优秀

- 函数
- 弱类型
- 动态对象
- 富有表现力的对象字面量表示法

<!-- -->

## 糟糕的想法

全局变量绑定

> 尽管JavaScript有缺陷，但它真的很优秀

# 语法

## 注释

应该使用`//`，而不是`/**/`

## 标识符

建议**应该由字母开头**，允许中间出现`数字`和`_`

应该遵循一定的命名规范

## 数字

- Js只有**一种**数字类型。内部表现为**64位浮点数**，没有分离处整数类型，所以1.0和1一样。可以避免短整型溢出问题。
- 100 = 1e2
- `NaN`是数值，表示不能产生正常结果的运算结果，**它不等于任何值，包括自身**，可以用`isNaN(number)`判断NaN
- `Infinity`表示大于1.79……e+308的值
- **数字拥有方法**，包括在`Math对象`之中。

<!-- -->

## 字符串

- `Unicode`是16位的，所以JS所有字符都是**16位的**
- **JS没有字符类型，**表示字符就创建一个包含一个字符的字符串就行
- 需要记得的几个转义：`\r`,`\n`,`\t`
- “A” === “\u0041” (\u表示特定字符编码)
- 字符串**不可变**，一旦创建就无法改变。可以通过`+`等运算创建一个**新的字符串**
- 包含完全相同字符序列的字符串被认为是完全相同的`'c'+'a'+'t' === 'cat'`

<!-- -->

## 语句

- js中代码块**不会创建新的作用域**，因此变量应该被定义在函数的头部，而不是代码块中。

- 下面的值会被当成**假**：

    - false
    - null
    - ''
    - undefined
    - 数字0
    - 数字NaN

    <!-- -->

- `case`表达式**不一定**必须是**常量**

- `for in`会枚举一个对象的所有**属性（键名）**

- `object.hasOwnProperty()`确定属性是该**对象成员**的，还是来自**原型链**

    ```javascript
    <span class="kwd">for</span><span class="pln">¨NBSP;</span><span class="pun">(</span><span class="pln">myvar in obj</span><span class="pun">)</span><span class="pln">
    </span><span class="pun">{</span><span class="pln">
    </span><span class="kwd">if</span><span class="pun">(</span><span class="pln">obj</span><span class="pun">.</span><span class="pln">hasOwnProperty</span><span class="pun">(</span><span class="pln">myvar</span><span class="pun">))</span><span class="pln">
    </span><span class="pun">{</span><span class="pln">
    </span><span class="pun">……</span><span class="pln">
    </span><span class="pun">}</span><span class="pln">
    </span><span class="pun">}</span>
    ```

- throw 语句中表达式一般是一个**对象字面量**，包含`name`和一个`message`

- Javascript **不允许在return 和表达式之间换行** （会自动在尾部添加分号）

- Javascript **不允许在break 和表达式之间换行**


<!-- -->

## 表达式

### 运算优先级

运算符优先级：基本就是**一元>二元>三元**

<div class="wiz-table-container" style="position: relative; padding: 15px 0px 5px;"><div class="wiz-table-body"><table><thead><tr><th>符号</th><th>含义</th></tr></thead><tbody><tr><td>. [] ()</td><td>提取属性调用函数</td></tr><tr><td>delete new typeof + = !</td><td>一元</td></tr><tr><td><ul><p>/ %</p></ul></td><td>乘除，<strong>求余</strong></td></tr><tr><td><ul><p>-</p></ul></td><td>加/减 连接</td></tr><tr><td><blockquote><p>= &lt;= &gt;&lt;</p></blockquote></td><td>不等</td></tr><tr><td>=== !==</td><td>等式</td></tr><tr><td>&amp;&amp;</td><td>逻辑与</td></tr><tr><td>||</td><td>逻辑或?</td></tr><tr><td>? :</td><td>三元</td></tr></tbody></table></div></div>

- `%` 不是传统意义上的取模，而就是取余（其实就是用trunc模式的取模，见我的另一篇文章）

- `&amp;&amp;` 的真实含义：A?B:A，它的特性**一般用来避免错误**

    > A.b && A.b.c

- `||`的含义：A?A:B ，它的特性**一般用来填充默认值**

    > var color = MYAPP.color \|\| '#FFF'


<!-- -->

## 字面量

字面量是一个可以快速创建`对象`，`数组`,`函数`的表示法。

- 对象字面量：属性可以是**标识符或字符串**，这些名字当作字面量名而不是变量名对待，所以对象的属性名**在编译时才知道**。
- 属性的值就**是表达式**

<!-- -->

