---
date: 2019-05-21
categories: 
- 前端技术
- javascript
title: 理解JS_作用域链
---
# 理解JS_作用域链

上一文我们说过：

> 作用域是根据名称查找变量的一套规则

> A *scope chain* is a *list of objects* that are searched for *identifiers* appear in the code of the context.

## 查找规则

在访问一个变量时，先在链表的第一个变量对象上查找，如果没有找到则继续在第二个变量对象上查找，直到搜索到全局执行环境的变量对象即window对象。这也就形成了作用域链（Scope Chain）的概念。 

## 作用域链（Scope Chain）

- 当代码在一个环境中执行时，会在变量对象VO中添加一个跟作用域相关的字段（`_parent_`），这个字段会指向调用它的的一个作用域链。作用域链的用途是保证对执行环境有权访问的所有变量和函数的有序访问。 
- 作用域链包含了执行环境栈中的**每个执行环境对应的变量对象**。通过作用域链，可以决定变量的访问和标识符的解析。 

还记得上一章我们说过EC中包含VO，this值和`[Scope]`，其中`[Scope]`存储的就是作用域链

```
activeExecutionContext = {
    VO: {...}, // or AO
    this: thisValue,
    Scope: [ // Scope chain
      // list of all variable objects
      // for identifiers lookup
    ] 
};
```

其中，Scope可以看作一个VO的数组（当然具体实现肯定不是这样）

```
var` `Scope = [VO1, VO2, ..., VOn]; ``// scope chain
```

有些资料是会将作用域链描述成层级对象链，通过`__parent__`字段链式引用，这样在实现上更加合理。

```
var VO1 = {__parent__: null, ... other data}; -->
var VO2 = {__parent__: VO1, ... other data}; -->
```

但是，这里不考虑实现细节，为了下面描述方便起见，我们还是把作用域链看作一个数组。



# 函数生命周期

一般来说，变量查找一般都是发生在函数体内的，因此跟函数的生命周期相关。

函数生命周期一般有两个部分：函数创建，函数激活

## 函数创建

当函数创建的时候，除了对应的GO/AO被创建，同时其中的**函数对象**会被添加一个`[[Scope]]`属性（注意这个属性不是执行环境中的作用域链，因为当前没有函数调用，所以也不存在AO），同时**填充其外层的VO**，最外层的函数填充的VO就是GO。

```javascript
var x = 10;
function foo() {    
  var y = 20;
  alert(x + y);
}
foo(); // 30
```
如上代码，在执行前，GO会被定义

```javascript
GlobalContext.GO = {
	x: undefined;
  foo: <reference to ``function``>
}
```

在**foo创建**的时候，会初始化foo的[[Scope]]：

```javascript
foo.[[Scope]] = [
  globalContext.VO // === Global
];

```

**注意：**[[Scope]]的默认值填充是在**创建**的时候而不是函数激活的时候，一直存在到函数销毁为止。哪怕一个函数从不使用，[[Scope]]也被存储在**函数对象**中了。

> *[[Scope]]* is a hierarchical chain of all *parent* variable objects, which are *above* the current function context; the chain is saved to the **function** at its **creation**.

## 函数激活

在函数激活的时候，会进行如下操作，将**当前AO**塞入当前函数[[Scope]]的最前面，形成一个引用，保存在作用域链中。因此GO始终都是作用域链的**最后一个对象。**

```javascript
// 此时Scope是真正的作用域链
fooContext.Scope = fooContext.AO + foo.[[Scope]]  
// 由于Scope可以看作数组，以下表达式更好理解 
fooContext.Scope = [AO].concat(foo.[[Scope]]);
```

注意：只有在这个时候，才是该函数作用域链真正生成。

## 函数结束

当函数运行结束，AO就会结束对应的[[Scope]]也



[[Scope]]的生命周期随着funtion的结束结束。

# 变量查找

在变量进行查找的时候，会从作用域链中**从前到后**依次进行变量的查找（如果当前的VO中没有找到则查找下一个VO直到查完所有的VO，最后抛出`ReferenceError`错误）

例如以下代码：

```javascript
var x = 10;
function foo() {
  var y = 20;  
  function bar() {
    var z = 30;
    alert(x +  y + z);
  }
  bar();
}
foo(); // 60
```
最后barContext中的Scope会形成以下结构：
```javascript
barContext.Scope = [
  barContext.AO,   // {z : 30}
  fooContext.AO,  //  {y: 20}
  globalContext.VO  // {x : 10}
];
```

当查询x，y，z的时候会进行以下查找操作，可以发现x进行了3次查找，y进行了2次，z只用了一次就找到了。

```javascript
- "x"
-- barContext.AO // not found
-- fooContext.AO // not found
-- globalContext.VO // found - 10

- "y"
-- barContext.AO // not found
-- fooContext.AO // found - 20

- "z"
-- barContext.AO // found - 30
```

也正是因为这种结构，最里面的函数拥有最全的变量

结论：

- **内部环境可以通过作用域链访问所有的外部环境**，但是外部环境不能访问内部环境中的任何变量和函数。 
- **标识符解析**（变量名或函数名搜索）是沿着作用域链**一级一级地搜索标识符的过程**。搜索过程始终从作用域链的前端开始，然后逐级地向后（全局执行环境）回溯，直到找到标识符为止，或者找到全局执行环境也没找到，undefined。



## 变量屏蔽

正是因为这样，当前函数内的变量总是会最先被找到，从而显示出的效果就是屏蔽了函数外的同名变量，这样就像形成了一个**无形的域**。



# 效率优化

知道了变量的这种查找规则后，我们知道变量的解析是有代价的，而读写全局变量通常是最慢的（因为在最作用域的最末端） ，因此《高性能Javascript》给出的经验法则是：

> 尽可能的使用局部变量，如果某个跨作用域的变量被访问了一次以上，则把它存储为局部变量

最常见的例子就是把document这类经常访问的全局变量存储为局部变量

```javascript
const doc = document;
const bd = doc.body
const link = doc.getElementByTagName('a');
```



## 动态作用域

在Javascript中有三种方式可以动态的改变作用域

1. try-catch语句的catch块；
2. with语句
3. eval语句

其实就是执行以下语句

```
Scope = withObject|catchObject + AO|VO + [[Scope]]
```



## with

with的原理是新增一个作用域将with包裹的变量提升到第一位。例如刚才的document，我们可以使用with

```javascript
...
with(document) {
  const bd = body;   // body只用查找1次
  const link = bd.getElementByTagName('a');  // bd 反而需要查找2次
}

```

看上去更高效，其实不然，因为此时**所有的局部变量的作用域变成了二级作用域**，所以整体解析代价反而更大。

## Try-catch

try-catch语句和with的作用差不多，catch会将error变量提升到第一位。

```javascript
try {

} catch(err) {
 ...
}
```

## eval





## 特殊的例子

```
var x = 10, y = 10;
  
with ({x: 20}) {
  
  var x = 30, y = 30;
  
  alert(x); // 30
  alert(y); // 30
}
  
alert(x); // 10
alert(y); // 30
```

看看这个，是不是看的懂发生了什么？

首先：

```javascript
globalContext.Scope = [
	globalContext.VO = { x: 10, y: 10 }
]
```

进入with中，由于变量定义早在定义期就完成了，所以这里只是简单的变量查找和赋值：

```javascript
withContext.Scope = [
	withContext.VO = { x: 20 -> 30 },  // 修改了with中的x
	globalContext.VO = { x: 10, y: 10 -> 30}  // 修改了GO中的y
]
```

所以此时输出 30 30，退出with之后，再输出就是 10 30

```globalContext.VO = { x: 10, y: 10 }
GlobalContext.Scope = [
	globalContext.VO = { x: 10, y: 30 }
]
```

但是如果es5之后的let关键词有局部左右域的概念：

```javascript
var x = 10, y = 10;
  
with ({x: 20}) {
  
  let x = 30, y = 30;
  
  alert(x); // 30
  alert(y); // 30
}
  
alert(x); // 10
alert(y); // 10
```





# 闭包

闭包算是直接跟`[[Scope]]`相关的，我们先来看一个例子：

## 假闭包

```javascript
var x = 10;

function foo() {
  return x;
}

function bar() {
  var x = 20;
  return foo(); 
};

bar()  // 10, but not 20
```

这里会发现输出是10不是20这是为什么呢？因为作用域链的默认填充是在函数创建的时候填充的，而不是在函数激活的时候才填充。因为foo是在GEC中创建的，当foo创建的时候，会将GO填充。

```javascript
foo.Scope = [
  globalContext.VO  // {x : 10}
]
```

而在bar中只是简单的调用了foo（此时foo是在bar中向上查找到的），并没有创建foo。

```javascript
barContext.Scope = [
	barContext.AO // { x: 20}
  globalContext.VO // {x: 10, foo: <Reference ..>, bar: <Reference ..> }
]
```

所以最后foo被调用的时候，它的Scope如下：

```javascript
fooContext.Scope = [
	fooContext.AO // {}
  globalContext.VO // {x: 10, foo: <Reference ..>, bar: <Reference ..> }
]
```

## 真正的闭包

我们都知道闭包的作用是用来创建一个独立的作用域，上面的方式明显无法使用bar中的变量，来看一个真正的闭包长什么样：

```javascript
var x = 10;

function bar() {
  var x = 20;
  function foo() {
    return x;
  }
 return foo();
}
 
bar(); // 20
 
```

跟上面的例子不同的是，此时我们在bar中定义的foo，此时由于foo是在bar中创建的，因此最后foo的作用域链如下：

```
fooContext.Scope = [
	fooContext.AO // {}
	barContext.AO // {x: 20}
  globalContext.VO // {x: 10, foo: <Reference ..>, bar: <Reference ..> }
]
```

这样，我们就顺利创建了一个封闭的作用域，在bar定义的变量无法在外面直接访问，但是我们通过返回一个函数间接的访问到bar内的变量。

这样有一些明显的好处：

- `不同函数定义的变量彼此独立 `，可以很好的避免变量全局污染的问题。
- 可以很好的维护私有变量，避免直接操作数据，只允许特定的操作对私有变量进行操作。



## 通过Function构造函数

有个比较特殊的，就是通过Function构造的函数，其[[Scope]]永远只有GO

```
var x = 10;
  
function foo() {
  
  var y = 20;
  
  function barFD() { // FunctionDeclaration
    alert(x);
    alert(y);
  }
  
  var barFE = function () { // FunctionExpression
    alert(x);
    alert(y);
  };
  
  var barFn = Function('alert(x); alert(y);');
  
  barFD(); // 10, 20
  barFE(); // 10, 20
  barFn(); // 10, "y" is not defined
  
}
  
foo();
```



## Object.prototype作用域查询

考虑到原型链的特性：

> 如果没有在当前对象上找到相应属性，则会在对象的原型链进行查找。

所以，考虑到部分实现上，GO的原型链也是Object，当我们定义Object.prototype，我们就可以直接在全局访问到

```javascript
Object.prototype.x = 10;
console.log(x);  // 10
```

所以以下代码会输出10

```javascript
function foo() {
  alert(x);
}
  
Object.prototype.x = 10;
  
foo(); // 10
```

但是AO不存在prototype，所以

```javascript
function foo() {
  
  var x = 20;
  
  function bar() {
    alert(x);
  }
  
  bar();
}
  
Object.prototype.x = 10;
  
foo(); // 20
```





**注意**：

在访问变量时，就必须存在一个可见性的问题(内层环境可以访问外层中的变量和函数，而外层环境不能访问内层的变量和函数)。更深入的说，当访问一个变量或调用一个函数时，JavaScript引擎将不同执行环境中的变量对象按照规则构建一个链表，
![your text](http://img.hksite.cn/1500299451878)
图片来自于[理解Javascript_12_执行模型浅析](http://www.cnblogs.com/fool/archive/2010/10/16/1853326.html)

作用域链图，清楚的表达了执行环境与作用域的关系(一一对应的关系)，作用域与作用域之间的关系(链表结构，由上至下的关系)。 

```js
//红宝书代码
var color = "blue";
function changeColor(){
  var anotherColor = "red";
  function swapColors(){
    var tempColor = anotherColor;
    anotherColor = color;
    color = tempColor;
    // 这里可以访问color, anotherColor, 和 tempColor
  }
  // 这里可以访问color 和 anotherColor，但是不能访问 tempColor
  swapColors();
}
changeColor();
// 这里只能访问color
console.log("Color is now " + color);
```

上述代码一共包括三个执行环境：`全局执行环境`、`changeColor()的局部执行环境`、`swapColors()的局部执行环境`。

- 全局环境有一个变量color和一个函数changecolor();
- changecolor()函数的局部环境中具有一个anothercolor属性和一个swapcolors函数，当然，changecolor函数中可以访问自身以及它外围（即全局环境）中的变量;
- swapcolor()函数的局部环境中具有一个变量tempcolor。在该函数内部可以访问上面的两个环境（changecolor和window）中的所有变量，因为那两个环境都是它的父执行环境。 

上述代码的作用域链如下图所示： 

![your text](http://img.hksite.cn/1500299544873)





## 执行环境与作用域的区别与联系

- 执行环境为**全局执行环境**和**局部执行环境**，局部执行环境是函数执行过程中创建的。 （还有一个eval环境）
- 作用域链是**基于执行环境的变量对象**的，由所有执行环境的变量对象(对于函数而言是活动对象，因为在函数执行环境中，变量对象是不能直接访问的，此时由活动对象(activation object,缩写为AO)扮演VO(变量对象)的角色。)共同组成。 
- 当代码在一个环境中执行时，会创建变量对象的一个作用域链。作用域链的用途：**是保证对执行环境有权访问的所有变量和函数的有序访问**。作用域链的**前端**，始终都是当前执行的代码所在环境的**变量对象**。



## 没有块级作用域

es6有了，用let，详细的去看es6的[let&const](../es6/let&const.md)



## 延长作用域链

1. try-catch语句的catch块；
2. with语句



看下面实例：

```js
(function bildUrl(){
 var qs = "?debug=true";
    with(location){
         var url = href + qs;    
     }
   alert(url)
})()
```

with会把location对象的所有属性和方法**包含到变量对象**中，并**加入到作用域链的顶部**。此时访问href实际上就是location.href。

但新声明的“url”变量会加入到最近的执行环境的变量对象里。试下面例子，说明url是可以访问到的，只是此时是undefined

```js
(function bildUrl(){
            var qs = "?debug=true"; 
            if (!url)
            {
                alert("这里可以看到url"); //可以正常显示url已经被声明并且被扫描进函数的变量对象
            }
            
            try{
                if (!a)
                {
                    alert(1);// 报错因为a根本就不存在
                }
            }
            catch(e){
                console.log("作用域链被延长了吧");
            }
            
            with(location){
                var url = href + qs;    
            }
            
        })()
```

对catch语句来说，会创建一个新的变量对象加入到作用域链的顶部，其中**包含的是被抛出的错误对象的声明。**
需要说明的是，ie8之前的版本有个不符合标准的地方，就是被势出的错误对象会被加入到执行环境的变量对象。