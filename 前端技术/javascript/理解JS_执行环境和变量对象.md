---
title: 理解JS_执行环境和变量对象
tags: 
- javascript
- 作用域
date: 2019-04-11
categories: 
- 前端技术
- javascript
---

> 最新的ECMAScript中很多概念发生了变化，比如ES5中执行环境的组成变成了this绑定，变量环境，词法环境，但是**这里我们依然先沿用es3中的概念**。一来这些概念是符合目前主流认知（比如和红宝书里概念一致），方便理解。二来，在这个基础上，我们后续再学习新的概念的时候，知道这些概念的演变过程，更加深我们对ECMAScript规范的理解。

# 写在开头

有关执行环境和作用域的话题一直困扰初学者的头座大山，接下来的变量提升，闭包，this指针一系列的问题都源于此……因此YDKJS把[作用域是什么](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/ch1.md)作为其上篇开篇第一章，而红宝书也在其第4.2节就抛出了这个话题。

首先，必须要明确的是，`执行环境(execution context)`和`作用域(scope)`是两个完全不同的概念，虽然他们从中文角度来看含义有些类似，但是他们确实是不同的东西。因此我觉得YDKJS中的描述更为靠谱，作用域是一套有关变量查找的**规则**。

其次，在YDKJS中没有明确的说明`执行环境`的问题，他是从编译的角度去聊作用域的（这点很有意思，作者强调JS是一个编译型语言，而非解释型语言，这一点不再深入探讨，关于这一点还可以去看这个issue：[About compilation phase](https://github.com/getify/You-Dont-Know-JS/issues/1375)）。

这一篇我们主要是介绍一下什么是`执行环境`和`执行环境栈`，同时介绍一下执行环境中很重要的概念：`变量对象`，以及它在不同阶段的表现，也为后续介绍`作用域链`和`this绑定`打下基础。

# 代码执行阶段

我们这里聊**执行环境**和**作用域**大多是在ECAMScript规范层面中聊（不考虑JS引擎的实现过程，至于进入执行环境是在编译还是解释阶段不过多探讨），即我们眼中的代码执行根据规范大致会经历两个阶段：

- 数据定义期（进入执行环境）
- 代码执行期（代码正式执行）

注：官方的说法是**进入执行环境阶段**和**代码执行阶段**，这里为了后续表述方便用上面两个阶段表示。

而在数据定义期，会发生一些事情，这些事情就是我们接下来需要讨论的。

## 

一般来说 ，我们会把代码的执行分为两个阶段：

而在`数据定义期`，会发生几件事：**创建活动对象AO**，**创建作用域链**，**确定this指向**。其实也就是填充我们之前所说的**环境对象**，填充的这些数据就会给接下来的代码执行提供参考依据。





# 执行环境EC（Execution Context）

执行环境是我们遇到的第一个抽象概念，我也花了很长时间才把它和执行代码区分开来，下面是规范的定义：

> *Execution context (abbreviated form — EC)* is the abstract concept used by ECMA-262 specification for typification and differentiation of an executable code.

执行环境是规范定义出来的一个**抽象概念**，既然叫执行环境，就可以看出它是一个**动态（非静态），运行时**的概念，这点很重要！就像我们从一个地点走到另一个地点，我们所处的环境会发生变化，从而我们的表现也会发生变化，例如走进图书馆，我们就会小声说话，这就是图书馆提供给我们不同于其他环境的一种约束。

![image-20190412152638320](http://img.hksite.cn/2019-04-12-072646.png)

> 人类生存的空间及其中可以直接或间接影响人类生活和发展的各种自然因素称为环境。

执行环境也会随着我们代码的执行动态的变化，而它提供的一些`配置`，`约束`，`规则`直接影响了所处环境中代码执行时的表现，**例如：出现了变量，变量去哪找，出现了this，this指向谁？**

至于Js引擎如何去实现，这是Js引擎的事情，规范并不关心。所以，在这里，我们就简单的把EC理解为一个`对象`。这个对象有如下配置（属性）

- 环境对象（Variable Object ）

- 作用域链（Scope chain） **（从这里就可以出来执行环境和作用域的关系了吧？）**

- this值（thisValue） ：它总是指向当前所执行代码的对象的引用

  ![](http://img.hksite.cn/2019-04-12-022746.jpg)

通过这个对象，里面的代码再执行的时候就能知道我的变量去哪找，我的this指向谁了。

## 执行环境分类

我们所说的代码，一般只会在**三种环境**（上下文）中：

- `global code` in globalContext：GEC 全局环境，所有不在函数或eval中的代码，都属于全局环境，只有**一个**。
- `function code` in functionContext：FEC 函数环境，用户定义的函数中的的代码。
- `eval code` in evalContext：EEC eval环境，在eval函数中动态执行的代码。

## 执行环境栈

我们刚才介绍了代码执行的两个阶段，这两个阶段总是交替进行的，在`代码执行期`，可能会激活其他EC（调用函数，执行eval，哪怕是函数调用自身……）从而进入新的EC的`数据定义期`……只有当它结束了，才把控制权交还给调用它的环境中。这种结构就是经典的栈结构，类似很多语言的调用栈（call stack）。

![](http://img.hksite.cn/2019-04-12-025623.jpg)

当代码执行到环境外，该环境就弹出栈，从而进入下面的环境中……

![](http://img.hksite.cn/2019-04-12-025758.jpg)

如果用一个数组来表示，我们大概可以看成如下流程：

```javascript
// ECStack初始化
const ECStack = [
  globalEC
];
// globalEC调用EC1
ECStack = [
	EC1,
  globalEC
];
// EC1执行结束
ECStack = [
  globalEC
];
// globalEC执行结束
ECStack = []
```

好了，以上就是EC的大概介绍，而EC主要是在数据定义期确定的，这个阶段会发生三件事情：（至于这个阶段怎么发生的，是静态扫描，编译……我们不做过深的探讨）

- 创建变量对象
- 创建作用域链
- 确定this指向

首先，介绍一个很重要的概念：变量对象VO，它是执行环境所有的**数据源**。

## 变量对象VO

- 每个**执行环境**都有与之关联的**VO**。
- VO是用来存储数据的，环境中定义的所有**变量**和**函数**都保存在这个对象中
- VO在不同执行环境中，结构会略有不同（如下面的**全局对象GO**和和**活动对象AO**）
- **函数表达式**不会被记录到VO中（函数表达式只会在代码执行阶段创建，关于函数表达式更多细节可以查看[ECMA-262-3 in detail. Chapter 5. Functions.](http://dmitrysoshnikov.com/ecmascript/chapter-5-functions/#function-expression)）

例如下面这段代码：

```javascript
var foo = 10;
 
function bar() {} // function declaration, FD  函数声明
(function baz() {}); // function expression, FE  函数表达式
 
console.log(
  this.foo == foo, // true
  window.bar == bar // true
);
 
console.log(baz); // ReferenceError, "baz" is not defined
```


存储在VO中的数据有哪些呢？其实就只有foo和bar（baz是函数表达式）。

![](http://img.hksite.cn/2019-04-12-030516.jpg)

### 全局对象GO (Global Object)

全局对象就是**全局执行环境**的VO

> *全局对象*是在进入任何执行环境之前创建的对象; 
>
> 该对象存在于**单个副本中**，其属性可从程序的任何位置访问，全局对象的生命周期随着程序结束而结束。

所以，我们通常意义上说的global，就是一个全局对象，这个对象在初始化的时候会提供一些通用的对象和方法，例：`Math`, `String`, `Date`, `parseInt`……，另外不同的环境下，还可能会给全局对象增加额外的对象，例如浏览器环境下的BOM(Browser Object Model)。

```javascript
global = {
  Math: {...},
  String: {...}
  ...
  ...
  window: global
};
```

通常来说，我们不需要通过前缀去访问全局对象里的变量（全局变量），但是我们可能会想通过**更直观**的方式访问到这个全局对象（`xxxx.Math`），不然，这个对象的行为对用户来说就太黑盒了。于是规范中提到了两个方案：

- 通过全局环境下的this（GEC中的this指向这个EC的VO，这个后续说this的时候会说）
- 一般GO会暴露一个**自身引用**，浏览器环境下一般是window，Node环境下是global。

所以，以下几种访问是一至的：

```javascript
window.a === a === this.a
```

这也就能解释学Js初期很多人的一个疑问：

> 为什么我全局定义了一个变量，我在window中也能拿到，在this中也能拿到？明明没有操作window，没有操作this。

因为在定义期的时候，GO会拿到环境中所有定义的变量，而window和this都是GO的引用。

###  活动对象AO

而当某个函数激活的时候，我们把该环境对应的对象称为活动对象AO（因为当前只有它是活动的嘛）。注意：**该活动对象无法被直接访问**。但是同时，它额外提供了一个对象还有一些额外的数据：**函数参数**，**arguments**。

```javascript
AbstractVO (generic behavior of the variable instantiation process)
  ║
  ╠══> GlobalContextVO
  ║        (VO === this === global)
  ║
  ╚══> FunctionContextVO
           (VO === AO, <arguments> object and <formal parameters> are added)
```

arguments我们都很熟悉了，它包含：

- `callee ` 当前function的引用
- `length` 参数长度
- `参数索引数组` 它会根据传参的顺序记录每个参数的值

例如如下代码：

```javascript
var a = 10;

function test(x, y) {
  var z = 30;
  function bar() {} // FD
  (function baz() {}); // FE
}
 
test(30, 20);
```
会生成如下结构：

```javascript
// 全局VO
VO(globalContext) = {
  a: 10,
  test: <reference to ``function``>
};

// 当执行到test(30,20)的时候，会生成与test函数环境相关的AO
AO = {
  x: 30,
  y: 20,
  z: undefined，  // 为什么是undefined我们等下会说
  arguments: {...}
};

VO(test functionContext) === AO
```

![Figure 8. An activation object.](http://img.hksite.cn/2019-04-12-040226.png)

# 从一套完整的执行流程看VO

上面大多是介绍抽象概念，下面介绍一下一套代码执行的具体流程，应该能帮助你更好的理解两个阶段分别发生了什么。

首先还是大致说一下代码的执行过程：

- 当JavaScript解释器初始化执行代码时，它首先默认进入**全局执行环境GEC**，开始进行**全局的数据定义**，然后开始进行代码执行，每当执行到**函数调用**都会创建一个**新的EC**。 
- 函数的环境就会被推入一个**环境栈ES中**。在函数执行完后，栈将其环境弹出，把控制权返回给之前的执行环境。ECMAScript程序中的执行流正是由这个便利的机制控制着。 
- 代码进入一个新的EC后，开始进入新的**数据定义期**和**代码执行期**阶段。

## 数据定义期

数据定义期主要是会初始化VO，VO的初始化会**顺序**经历以下过程（理解这个顺序对很多概念，如变量提升，函数定义优先……会有更深刻的认识）

- **过程1**：如果是FEC，则将所有的**参数**添加到AO，如果传值了则设置`初始值`，否则为`undefined`

- **过程2**：查找所有的**函数定义**，添加到AO，如果存在同名属性，**则覆盖**。

- **过程3**：查找所有的**变量定义**，如果存在同名属性，则**不做处理**，否则添加到AO。

注意，初始化VO只关注**变量定义**，具体的变量赋值在代码执行期。

例如：

```javascript
function test(a, b) {
  var c = 10;
  function d() {}
  var e = function _e() {};
  (function x() {});   // 不会添加到AO中
  if(true) {
    var f = 1;
  } else {
    var g = 2;
  }
}
  
test(10); 
```

**当进入test后**，生成的AO如下：

```
AO(test) = {
  a: 10,
  b: undefined,
  c: undefined,
  d: <reference to FunctionDeclaration "d">
  e: undefined,
  f: undefined,
  g: undefined
};
```

注意，虽然我们里面有个`if`语句，理论上其中的else永远不会执行，但是在**数据定义阶段是不会考虑代码的逻辑**的，因此g变量还是会加入AO中（es6中let存在块作用域）。为了验证，我们可以把g输出，可以发现会输出`undefined`，而不是报错`g is not defined`

```javascript
function test(a, b) {
	……
	console.log('c:',c, 'g:', g)   // c: 10 g: undefined
}
```

## 代码执行期

代码执行期其实就是不断的查找AO和修改AO的过程，例如随着代码执行，AO的值不断的被填充：

```
AO['c'] = 10;
AO['e'] = <reference to FunctionExpression "_e">;
```

### 变量提升和函数声明优先

这里就可以引出另一个广为人知的小概念：变量提升，函数声明优于变量声明。其实熟悉了上面的流程之后，这个概念很好理解，都是因为数据定义期的那套规则，例如下面的代码，相信大家都知道输出什么，我们直接分析过程：

```javascript
console.log(x); 
 
var x = 10;
console.log(x); 

x = 20;
 
function x() {}
 
console.log(x); 
```

初始GO，由于先执行过程2，再执行过程3，因此不会替x，你可以看似以下三个过程：

```javascript
GO = {}  // 初始化

GO = {
  x: <reference to FunctionDeclaration>     // 查询方法定义
}

Go = {
  x: <still reference to FunctionDeclaration>  // 找到x的变量定义，不做变化
}
```

执行代码：

```javascript
console.log(x); // 查询GO，发现为function，输出funciton
 
var x = 10;   //  GO['x'] = 10
console.log(x); // 10

x = 20;   //  GO['x'] = 20
 
function x() {}   // 这是定义，跳过
 
console.log(x); // 20  
```

所以，为什么你在一开始就能访问x，哪怕没有定义function，你也能拿到`undefined`，这都是由于经历了数据定义期，**此时环境对象里有数据了。**

讲到这里，其实我们大概知道的执行环境是怎么工作的，它的数据其实都是通过VO进行管理的，到这里可以解决大部分有关执行环境的问题，接下来介绍的两个概念都是指代码中一些特殊情况的处理方案：

- 作用域链：解决VO中找不到对应变量的情况
- this值：代码遇到this时，应该怎么处理的情况



# 附

## 带var和不带var定义变量区别？

很多说不代var定义变量就会定义全局变量，**这是不对的**，实际上变量的定义只有var一种形式（这里是说es3规范），下面这种形式

```
a = 10
```

它只是在`代码执行`阶段**对VO属性的赋值**，至于为什么它会被赋值到GO上，后面讲到作用域链就会说到，因为它会一直向上查询，直到找到GO为止。

所以，如果你写以下代码：

```
console.log(a); // undefined
console.log(b); // Uncaught ReferenceError: b is not defined
 
b = 10;
var a = 20;

```

你会发现，运行到输出b的时候，会报错，说b没有定义。（注意：not defined和undefined不一样，前者是完全没有定义，后者是定义了但是还不知道值）

这就说明当前VO中根本没有b的属性：

```
VO = {
  a: undefined
};
```

更重要的是，变量定义是`{DontDelete}`的（在ES5中为`[[configurable]]`），因此通过`var`定义的变量是无法通过delete删除的，但是在代码执行阶段添加的变量实际是属性，所以可以被delete

```javascript
a = 10;
console.log(a); // 10  在代码执行阶段被赋值
console.log(window.a); // 10
console.log(delete a); // true
console.log(window.a); // undefined
 
var b = 20;
console.log(b); // 20   由undefined -> 20
console.log(delete b); // false
console.log(window.b); // 20
```



## 参考文章

《You Don't Know Js》

 《Javascript高级程序设计》

[Javascript. The Core.#execution-context](http://dmitrysoshnikov.com/ecmascript/javascript-the-core/#execution-context)

[ECMA-262-3 in detail. Chapter 2. Variable object.](http://dmitrysoshnikov.com/ecmascript/chapter-2-variable-object/#phases-of-processing-the-context-code)

[javascript高级程序第三版学习笔记【执行环境、作用域】](http://www.cnblogs.com/pigtail/archive/2012/07/19/2570988.html)

[原生JS执行环境与作用域深入理解](http://blog.csdn.net/liujie19901217/article/details/52225025)

[理解Javascript_12_执行模型浅析](http://www.cnblogs.com/fool/archive/2010/10/16/1853326.html)
