---
title: ES6笔记
date: 2018-11-29
categories: 
- 前端技术
- javascript
tags: 
- javascript
- es6
init: 1
---

> 个人es6总结，大多是看阮老师的博客，总结了自己认为重要的部分


## let & const

let和const注意：只有在`{}`中才有作用域（if单语句不加{}  不能用let）

```javascript
if(1) let i = 1
Uncaught SyntaxError: Unexpected identifier
```

const表示常量必须在**定义的时候赋值**，且**不能修改**，否则报错

### 重复定义会报错

```javascript
// 报错
function () {
  let a = 10;
  var a = 1;
}

function () {
  var a = 10;
  let a = 1;
}

// 报错
function () {
  let a = 10;
  let a = 1;
}

function func(arg) {
  let arg; // 报错
}

function func(arg) {
  {
    let arg; // 不报错
  }
}
```



### for的特殊性

```javascript
for (var i = 0; i < 3; i++) {
  setTimeout(function(){console.log(i)},0)
}
//3 3 3 

for (let i = 0; i < 3; i++) {
  setTimeout(function(){console.log(i)},0)
}
//0 1 2 
```



`for`循环还有一个特别之处，就是循环语句部分是一个父作用域，而循环体内部是一个单独的子作用域。

```
for (let i = 0; i < 3; i++) {
  let i = 'abc';
  console.log(i);
}
```

### 无变量提升

`let`命令改变了语法行为，它所声明的变量一定要在声明后使用，否则报错。

```javascript
// var 的情况
console.log(foo); // 输出undefined
var foo = 2;

// let 的情况
console.log(bar); // 报错ReferenceError
let bar = 2;
```



### 暂时性死区（temporal dead zone，简称 TDZ）

只要块级作用域内存在`let`命令，它所声明的变量就“绑定”（binding）这个区域，不再受外部的影响。

```javascript
var tmp = 123;

if (true) {
  tmp = 'abc'; // ReferenceError
  let tmp;
}

if (true) {
  // TDZ开始
  tmp = 'abc'; // ReferenceError
  console.log(tmp); // ReferenceError

  let tmp; // TDZ结束
  console.log(tmp); // undefined

  tmp = 123;
  console.log(tmp); // 123
}
```

### typeof不再绝对安全

在没有`let`之前，`typeof`运算符是百分之百安全的，永远不会报错。现在这一点不成立了。如果一个变量用let申明，但是在typeof之后，typeof`运行时就会抛出一个`ReferenceError`。



### 立即执行函数不必要

块级作用域的出现，实际上使得获得广泛应用的立即执行函数表达式（IIFE）不再必要了。

```javascript
// IIFE 写法
(function () {
  var tmp = ...;
  ...
}());

// 块级作用域写法
{
  let tmp = ...;
  ...
}
```

### 不属于顶层对象

`let`命令、`const`命令、`class`命令声明的全局变量，不属于顶层对象的属性。也就是说，从ES6开始，全局变量将逐步与顶层对象的属性脱钩。

```javascript
var a = 1;
// 如果在Node的REPL环境，可以写成global.a
// 或者采用通用方法，写成this.a
window.a // 1

let b = 1;
window.b // undefined
```



### const本质

`const`实际上保证的，并不是变量的值不得改动，**而是变量指向的那个内存地址不得改动**。对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。但对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指针，`const`只能保证这个指针是固定的，**至于它指向的数据结构是不是可变的，就完全不能控制了**。因此，将一个对象声明为常量必须非常小心。

```javascript
const foo = {};

// 为 foo 添加一个属性，可以成功
foo.prop = 123;
foo.prop // 123

// 将 foo 指向另一个对象，就会报错
foo = {}; // TypeError: "foo" is read-only
```

上面代码中，常量`foo`储存的是一个地址，这个地址指向一个对象。不可变的只是这个地址，即不能把`foo`指向另一个地址，但对象本身是可变的，所以依然可以为其添加新属性。

下面是另一个例子。

```javascript
const a = [];
a.push('Hello'); // 可执行
a.length = 0;    // 可执行
a = ['Dave'];    // 报错
```

上面代码中，常量`a`是一个数组，这个数组本身是可写的，但是如果将另一个数组赋值给`a`，就会报错。

如果真的想将对象冻结，应该使用`Object.freeze`方法。

```javascript
const foo = Object.freeze({});

// 常规模式时，下面一行不起作用；
// 严格模式时，该行会报错
foo.prop = 123;
```

上面代码中，常量`foo`指向一个冻结的对象，所以添加新属性不起作用，严格模式时还会报错。

除了将对象本身冻结，对象的属性也应该冻结。下面是一个将对象彻底冻结的函数。

```javascript
var constantize = (obj) => {
  Object.freeze(obj);
  Object.keys(obj).forEach( (key, i) => {
    if ( typeof obj[key] === 'object' ) {
      constantize( obj[key] );
    }
  });
};
```



## 变量解构

## []数组型

只要**形式一样就能结构成功**，右边多没事，右边少就会有`undefined`

```javascript
let [foo, [[bar], baz]] = [1, [[2], 3]];
foo // 1
bar // 2
baz // 3

let [ , , third] = ["foo", "bar", "baz"];
third // "baz"

let [x, , y] = [1, 2, 3];
x // 1
y // 3

let [head, ...tail] = [1, 2, 3, 4];
head // 1
tail // [2, 3, 4]

let [x, y, ...z] = ['a'];
x // "a"
y // undefined
z // []
```

`...`表示rest元素数组。

### 右边可以是字符串

因为字符串是一个类数组，有length

```javascript
const [a, b, c, d, e] = 'hello';
a // "h"
b // "e"
```



### 右边不可遍历，出错

如果等号的右边不是数组（或者严格地说，**不是可遍历的结构**，参见《Iterator》一章），那么将会报错。

```javascript
// 报错
let [foo] = 1;
let [foo] = false;
let [foo] = NaN;
let [foo] = undefined;
let [foo] = null;
let [foo] = {};
```

事实上，只要某种数**据结构具有 Iterator 接口，都可以采用数组形式的解构赋值。**Generator是原生具有iterator接口的

```javascript
function* fibs() {
  let a = 0;
  let b = 1;
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

let [first, second, third, fourth, fifth, sixth] = fibs();
sixth // 5
```

### 解构允许默认值

ES6 内部使用严格相等运算符（`===`），判断一个位置是否有值。所以，如果一个数组成员**不严格**等于`undefined`，默认值是不会生效的。(**比如null**)

```javascript
let [foo = true] = [];
foo // true

let [x, y = 'b'] = ['a']; // x='a', y='b'
let [x, y = 'b'] = ['a', undefined]; // x='a', y='b'

let [x = 1] = [null];
x // null
```

## {}对象型

对象的解构与数组有一个重要的不同。数组的元素是按次序排列的，变量的取值由它的位置决定；而对象的属性没有次序，变量必须与属性同名，才能取到正确的值。

```javascript
let { bar, foo } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"

let { baz } = { foo: "aaa", bar: "bbb" };
baz // undefined
```

如果变量名与属性名不一致，必须写成下面这样。

```javascript
var { foo: baz } = { foo: 'aaa', bar: 'bbb' };
baz // "aaa"

let obj = { first: 'hello', last: 'world' };
let { first: f, last: l } = obj;
f // 'hello'
l // 'world'
```

这实际上说明，对象的解构赋值是下面形式的简写（**前面是模式，后面是真实变量**）

```javascript
let { foo: foo, bar: bar } = { foo: "aaa", bar: "bbb" };
```

### 不在声明使用需要加括号

由于`{}`的二义性，所以在`{}`开头的特殊语句，需要加上`()`

```javascript
let foo;
({foo} = {foo: 1}); // 成功


let baz;
{bar: baz} = {bar: 1}; //报错
```

### 嵌套对象

和数组一样，解构也可以用于嵌套结构的对象。

```
let obj = {
  p: [
    'Hello',
    { y: 'World' }
  ]
};

let { p: [x, { y }] } = obj;
x // "Hello"
y // "World"
```

注意，这时`p`是**模式**，不是变量，因此不会被赋值。

### 允许默认值

```javascript
var {x = 3} = {};
x // 3

var {x, y = 5} = {x: 1};
x // 1
y // 5
```

### 类数组

类似数组的对象都有一个`length`属性，因此还可以对这个属性解构赋值。

```javascript
let {length : len} = 'hello';
len // 5

let arr = [1, 2, 3];
let {0 : first, [arr.length - 1] : last} = arr;
first // 1
last // 3
```

### 数字字符串

解构赋值时，如果等号右边是数值和布尔值，**则会先转为对象。**

```javascript
let {toString: s} = 123;
s === Number.prototype.toString // true

let {toString: s} = true;
s === Boolean.prototype.toString // true
```

上面代码中，数值和布尔值的包装对象都有`toString`属性，因此变量`s`都能取到值。

解构赋值的规则是，只要等号右边的值不是对象或数组，就先将其转为对象。由于`undefined`和`null`无法转为对象，所以对它们进行解构赋值，都会报错。

```javascript
let { prop: x } = undefined; // TypeError
let { prop: y } = null; // TypeError
```

## 用途

### 交换变量

写法不仅简洁，而且易读，语义非常清晰。

```javascript
let x = 1;
let y = 2;
[x, y] = [y, x];
```

### 从函数返回多个值

```javascript
// 返回一个数组
function example() {
  return [1, 2, 3];
}
let [a, b, c] = example();

// 返回一个对象
function example() {
  return {
    foo: 1,
    bar: 2
  };
}
let { foo, bar } = example();
```

### 函数参数的定义

解构赋值可以方便地将一组参数与变量名对应起来。

```javascript
// 参数是一组有次序的值
function f([x, y, z]) { ... }
f([1, 2, 3]);

// 参数是一组无次序的值
function f({x, y, z}) { ... }
f({z: 3, y: 2, x: 1});
```

### 提取JSON数据

解构赋值对提取JSON对象中的数据，尤其有用。

```javascript
let jsonData = {
  id: 42,
  status: "OK",
  data: [867, 5309]
};

let { id, status, data: number } = jsonData;

console.log(id, status, number);
// 42, "OK", [867, 5309]
```

### 参数默认值

```javascript
jQuery.ajax = function (url, {
  async = true,
  beforeSend = function () {},
  cache = true,
  complete = function () {},
  crossDomain = false,
  global = true,
  // ... more config
}) {
  // ... do stuff
};
```

指定参数的默认值，就避免了在函数体内部再写`var foo = config.foo || 'default foo';`这样的语句。



### 遍历Map结构

任何部署了Iterator接口的对象，都可以用`for...of`循环遍历。Map结构原生支持Iterator接口，配合变量的解构赋值，获取键名和键值就非常方便。

```javascript
var map = new Map();
map.set('first', 'hello');
map.set('second', 'world');

for (let [key, value] of map) {
  console.log(key + " is " + value);
}
// first is hello
// second is world
```

如果只想获取键名，或者只想获取键值，可以写成下面这样。

```javascript
// 获取键名
for (let [key] of map) {
  // ...
}

// 获取键值
for (let [,value] of map) {
  // ...
```



# 字符串

## unicode字符问题

### 增加了四字节unicode表示

es5有`\uxxxx`表示unicode，但是，这种表示法只限于码点在`\u0000`~`\uFFFF`之间的字符。超出这个范围的字符，必须用两个双字节的形式表示。es6加入了**大括号表示**

```javascript
'\u{1F680}' === '\uD83D\uDE80'
```



### 码点表示

charPointAt();

String.fromCodePoint();

这些都是基于码点的，可以完整返回四字节Unicode。



### for of遍历

ES6为字符串添加了遍历器接口，使得字符串可以被`for...of`循环遍历。

```javascript
for (let codePoint of 'foo') {
  console.log(codePoint)
}
// "f"
// "o"
// "o"
```

除了遍历字符串，这个遍历器最大的优点是可以识别大于`0xFFFF`的码点，传统的`for`循环无法识别这样的码点。





## 扩展方法

### includes(), startsWith(), endsWith()

传统上，JavaScript只有`indexOf`方法，可以用来确定一个字符串是否包含在另一个字符串中。ES6又提供了三种新方法。

- **includes()**：返回布尔值，表示是否找到了参数字符串。
- **startsWith()**：返回布尔值，表示参数字符串是否在源字符串的头部。
- **endsWith()**：返回布尔值，表示参数字符串是否在源字符串的尾部。

```
var s = 'Hello world!';

s.startsWith('Hello') // true
s.endsWith('!') // true
s.includes('o') // true

```

这三个方法都**支持第二个参数，表示开始搜索的位置。**

```
var s = 'Hello world!';

s.startsWith('world', 6) // true
s.endsWith('Hello', 5) // true
s.includes('Hello', 6) // false

```

上面代码表示，使用第二个参数`n`时，`endsWith`的行为与其他两个方法有所不同。它针对前`n`个字符，而其他两个方法针对从第`n`个位置直到字符串结束。



### repeat()

`repeat`方法返回一个新字符串，表示将原字符串重复`n`次。

```
'hello'.repeat(2) // "hellohello"
'na'.repeat(0) // ""
```

参数如果是小数，会被取整。

```
'na'.repeat(2.9) // "nana"

```

如果`repeat`的参数是负数或者`Infinity`，会报错。

```
'na'.repeat(Infinity)
// RangeError
'na'.repeat(-1)
// RangeError

```

但是，如果参数是0到-1之间的小数，则等同于0，这是因为会先进行取整运算。0到-1之间的小数，取整以后等于`-0`，`repeat`视同为0。

```
'na'.repeat(-0.9) // ""

```

参数`NaN`等同于0。

```
'na'.repeat(NaN) // ""

```

如果`repeat`的参数是字符串，则会先转换成数字。

```
'na'.repeat('na') // ""
'na'.repeat('3') // "nanana"
```

### padStart和padEnd（ES2017）

ES2017 引入了字符串补全长度的功能（chrome可以使用了，node v6.3.1没实现该api）

如果某个字符串不够指定长度，会在头部或尾部补全。`padStart()`用于头部补全，`padEnd()`用于尾部补全。

```
'x'.padStart(5, 'ab') // 'ababx'
'x'.padStart(4, 'ab') // 'abax'

'x'.padEnd(5, 'ab') // 'xabab'
'x'.padEnd(4, 'ab') // 'xaba'
```

上面代码中，`padStart`和`padEnd`一共接受两个参数，第一个参数用来指定字符串的最小长度，第二个参数是用来补全的字符串。

**特点**：

1. 如果补串超长，自动截去超过部分  
2. 如果原字符串长度>=最小长度，则返回原字符串
3. 如果忽略第二参数，默认用空格补

```javascript
'abc'.padStart(10, '0123456789')  // '0123456abc'
'xxx'.padStart(2, 'ab') // 'xxx'
'x'.padStart(4) // '   x'
```



`padStart`的常见用途是为数值补全指定位数。另一个用途是提示字符串格式。

```javascript
'1'.padStart(10, '0') // "0000000001"
'123456'.padStart(10, '0') // "0000123456"
'12'.padStart(10, 'YYYY-MM-DD') // "YYYY-MM-12"
'09-12'.padStart(10, 'YYYY-MM-DD') // "YYYY-09-12"
```



## 模版字符串

我觉得es6在字符串里引入的最大的变化就是模板字符串，通过`` 表示，模板字符串有几大特性：

### 可以换行

```javascript
$('#list').html(`
<ul>
  <li>first</li>
  <li>second</li>
</ul>
`.trim());
```

### 可以嵌入js变量

通过`${}`可以嵌入js对象，允许使用运算符，可以使用函数（返回值）

```javascript
  //传统写法
'User '
      + user.name
      + ' is not authorized to do '
      + action
      + '.'

//ES6写法
`User ${user.name} is not authorized to do ${action}.`);


function fn() {
  return "Hello World";
}

`foo ${fn()} bar`
// foo Hello World bar

function fn2() {}
`test ${fn2()}`
// test undefined
`${fn2}`
// function fn2() {}


var x = 1;
var y = 2;

`${x} + ${y} = ${x + y}`
// "1 + 2 = 3"

`${x} + ${y * 2} = ${x + y * 2}`
// "1 + 4 = 5"
```

如果变量没申明，则会报错

```javascript
// 变量place没有声明
var msg = `Hello, ${place}`;
// 报错
```

### 需要转义

如果在模板字符串中需要使用反引号，则前面要用反斜杠转义。

```javascript
var greeting = `\`Yo\` World!`;
```

### 【高级】模板嵌套

模板字符串甚至还能嵌套。

```javascript
const tmpl = addrs => `
  <table>
  ${addrs.map(addr => `
    <tr><td>${addr.first}</td></tr>
    <tr><td>${addr.last}</td></tr>
  `).join('')}
  </table>
`;

const data = [
    { first: '<Jane>', last: 'Bond' },
    { first: 'Lars', last: '<Croft>' },
];

console.log(tmpl(data));
// <table>
//
//   <tr><td><Jane></td></tr>
//   <tr><td>Bond</td></tr>
//
//   <tr><td>Lars</td></tr>
//   <tr><td><Croft></td></tr>
//
// </table>
```



### 【高级】标签模板

实际就是将模板跟在函数名后，将会调用函数来执行这个模板

```javascript
var a = 5;
var b = 10;

tag`Hello ${ a + b } world ${ a * b }`;
// 等同于
tag(['Hello ', ' world ', ''], 15, 50);
```

`tag`函数的第一个参数是一个数组，该数组的成员是模板字符串中**那些没有变量替换的部分**，也就是说，变量替换只发生在数组的第一个成员与第二个成员之间、第二个成员与第三个成员之间，以此类推。

`tag`函数的其他参数，都是模板字符串各个变量被替换后的值。由于本例中，模板字符串含有两个变量，因此`tag`会接受到`value1`和`value2`两个参数。

`tag`函数所有参数的实际值如下。

- 第一个参数：`['Hello ', ' world ', '']`
- 第二个参数: 15
- 第三个参数：50

[具体看这里](http://es6.ruanyifeng.com/#docs/string#标签模板)

利用标签模板，**理论上可以在javascript里面加载别的语言代码：**

```javascript
function java(...){
  
}

java`
class HelloWorldApp {
  public static void main(String[] args) {
    System.out.println(“Hello World!”); // Display the string.
  }
}
`
HelloWorldApp.main();
```







### String.raw调用标签模板

单独使用标签模板无法自动把里面的参数转换成字符串，所以ES6原生提供了一个`String.raw`方法，往往用来充当模板字符串的处理函数，返回**一个斜杠都被转义（即斜杠前面再加一个斜杠）的字符串**，对应于替换变量后的模板字符串。

```javascript
String.raw`Hi\n${2+3}!`;
// "Hi\\n5!"

String.raw`Hi\u000A!`;
// 'Hi\\u000A!'
```

如果原字符串的斜杠已经转义，那么`String.raw`不会做任何处理。

```javascript
String.raw`Hi\\n`
// Hi\\n
//也就是"Hi\n"     //这里\n已经是转义过的，否则会换行，比如看下面的例子

`Hi\n${2+3}!`;
"Hi
5!"
```

由于它会自动转换斜杠，所以利于用来方便下一步做字符串。



## 正则

加入了大量特性都是跟unicode有关的，增加了一个`flags`属性返回正则的修饰符比如`gi`，加入了新的粘连（sticky）修饰符`y`,`y`修饰符的作用与`g`修饰符类似，也是全局匹配，后一次匹配都从上一次匹配成功的下一个位置开始。不同之处在于，`g`修饰符只要剩余位置中存在匹配就可，而`y`修饰符确保匹配必须从剩余的第一个位置开始，这也就是“粘连”的涵义。

```javascript
var s = 'aaa_aa_a';
var r1 = /a+/g;
var r2 = /a+/y;

r1.exec(s) // ["aaa"]
r2.exec(s) // ["aaa"]

r1.exec(s) // ["aa"]
r2.exec(s) // null

var s = 'aaa_aa_a';
var r = /a+_/y;

r.exec(s) // ["aaa_"]
r.exec(s) // ["aa_"]
```

进一步说，`y`修饰符号隐含了头部匹配的标志`^`。

```
/b/y.exec('aba')
// null

```

上面代码由于不能保证头部匹配，所以返回`null`。`y`修饰符的设计本意，就是让头部匹配的标志`^`在全局匹配中都有效。与`y`修饰符相匹配，ES6的正则对象多了`sticky`属性，表示是否设置了`y`修饰符。

```
var r = /hello\d/y;
r.sticky // true
```

### RegExp构造函数

在ES5中，RegExp构造函数的参数有两种情况。

第一种情况是，参数是字符串，这时第二个参数表示正则表达式的修饰符（flag）。

```javascript
var regex = new RegExp('xyz', 'i');
// 等价于
var regex = /xyz/i;
```

第二种情况是，参数是一个正则表示式，这时会返回一个原有正则表达式的拷贝。

```javascript
var regex = new RegExp(/xyz/i);
// 等价于
var regex = /xyz/i;
```

但是，ES5不允许此时使用第二个参数，添加修饰符，否则会报错。

```javascript
var regex = new RegExp(/xyz/, 'i');
// Uncaught TypeError: Cannot supply flags when constructing one RegExp from another
```

ES6改变了这种行为。如果RegExp构造函数第一个参数是一个正则对象，那么可以使用第二个参数指定修饰符。而且，返回的正则表达式会忽略原有的正则表达式的修饰符，只使用新指定的修饰符。

```javascript
new RegExp(/abc/ig, 'i').flags
// "i"
```

上面代码中，原有正则对象的修饰符是`ig`，它会被第二个参数`i`覆盖。

## 数字

### 八进制和二进制

ES6 提供了二进制和八进制数值的新的写法，分别用前缀`0b`（或`0B`）和`0o`（或`0O`）表示。从 ES5 开始，在严格模式之中，八进制就不再允许使用前缀`0`表示，ES6 进一步明确，要使用前缀`0o`表示。

```javascript
0b111110111 === 503 // true
0o767 === 503 // true
```

如果要将`0b`和`0o`前缀的字符串数值转为十进制，要使用`Number`方法。

```javascript
Number('0b111')  // 7
Number('0o10')  // 8
```

### Number.isFinite(), Number.isNaN()

ES6在Number对象上，新提供了`Number.isFinite()`和`Number.isNaN()`两个方法。

它们与传统的全局方法`isFinite()`和`isNaN()`的区别在于，传统方法先调用`Number()`将非数值的值转为数值，再进行判断，而这两个新方法**只对数值有效**，`Number.isFinite()`对于非数值一律返回`false`, `Number.isNaN()`只有对于`NaN`才返回`true`，非`NaN`一律返回`false`

```javascript
isFinite(25) // true
isFinite("25") // true
Number.isFinite(25) // true
Number.isFinite("25") // false

isNaN(NaN) // true
isNaN("NaN") // true
Number.isNaN(NaN) // true
Number.isNaN("NaN") // false
Number.isNaN(1) // false
```

可以这样部署isFinite()，isNaN类似

```javascript
(function (global) {
  var global_isFinite = global.isFinite;

  Object.defineProperty(Number, 'isFinite', {
    value: function isFinite(value) {
      return typeof value === 'number' && global_isFinite(value);
    },
    configurable: true,
    enumerable: false,
    writable: true
  });
})(this);
```

### Number.parseInt(), Number.parseFloat()

ES6将全局方法`parseInt()`和`parseFloat()`，移植到Number对象上面，行为完全保持不变。这样做的目的，**是逐步减少全局性方法，使得语言逐步模块化。**

```javascript
// ES5的写法
parseInt('12.34') // 12
parseFloat('123.45#') // 123.45

// ES6的写法
Number.parseInt('12.34') // 12
Number.parseFloat('123.45#') // 123.45

Number.parseInt === parseInt // true
Number.parseFloat === parseFloat // true
```

### Number.isInteger()

`Number.isInteger()`用来判断一个值是否为整数。需要注意的是，在JavaScript内部，整数和浮点数是同样的储存方法，所以**3和3.0被视为同一个值。**

```
Number.isInteger(25) // true
Number.isInteger(25.0) // true
Number.isInteger(25.1) // false
Number.isInteger("15") // false
Number.isInteger(true) // false
```

ES5可以通过下面的代码，部署`Number.isInteger()`。

```javascript
(function (global) {
   ……
  Object.defineProperty(Number, 'isInteger', {
    value: function isInteger(value) {
      return typeof value === 'number' && isFinite(value) &&
        value > -9007199254740992 && value < 9007199254740992 &&
        floor(value) === value;
    },
    ……
  });
})(this);
```

### Number.EPSILON

ES6在Number对象上面，新增一个极小的常量`Number.EPSILON`。

```javascript
Number.EPSILON
// 2.220446049250313e-16
Number.EPSILON.toFixed(20)
// '0.00000000000000022204'
```

引入一个这么小的量的目的，**在于为浮点数计算，设置一个误差范围**。我们知道浮点数计算是不精确的。但是如果这个误差能够小于`Number.EPSILON`，我们就可以认为得到了正确结果。因此，`Number.EPSILON`的实质是一个可以接受的误差范围。

### 安全整数和Number.isSafeInteger()

JavaScript能够准确表示的整数范围在`-2^53`到`2^53`之间（不含两个端点），超过这个范围，无法精确表示这个值。上面代码中，超出2的53次方之后，一个数就不精确了。ES6引入了`Number.MAX_SAFE_INTEGER`和`Number.MIN_SAFE_INTEGER`这两个常量，用来表示这个范围的上下限。

```javascript
Math.pow(2, 53) // 9007199254740992

9007199254740992  // 9007199254740992
9007199254740993  // 9007199254740992

Math.pow(2, 53) === Math.pow(2, 53) + 1
// true
Number.MAX_SAFE_INTEGER === Math.pow(2, 53) - 1
// true
Number.MAX_SAFE_INTEGER === 9007199254740991
// true

Number.MIN_SAFE_INTEGER === -Number.MAX_SAFE_INTEGER
// true
Number.MIN_SAFE_INTEGER === -9007199254740991
// true
```

上面代码中，可以看到JavaScript能够精确表示的极限。`Number.isSafeInteger()`则是用来判断一个整数是否落在这个范围之内。

## Math扩展

### Math.trunc()

`Math.trunc`方法用于去除一个数的小数部分，返回整数部分（原理就是根据正负调用floor和ceil），对于非数值，`Math.trunc`内部使用`Number`方法将其先转为数值。对于空值和无法截取整数的值，返回NaN。

```javascript
Math.trunc(4.1) // 4
Math.trunc(-4.1) // -4
Math.trunc(-0.1234) // -0
Math.trunc('123.456')// 123
Math.trunc(NaN);      // NaN
Math.trunc('foo');    // NaN
Math.trunc();         // NaN
```

### Math.sign()

`Math.sign`方法用来判断一个数到底是**正数、负数、还是零**。

它会返回五种值。

- 参数为正数，返回+1；
- 参数为负数，返回-1；
- 参数为0，返回0；
- 参数为-0，返回-0;
- 其他值，返回NaN。

### Math.cbrt()

`Math.cbrt`方法用于计算一个数的**立方根** Math.pow(Math.abs(x), 1/3)



### Math.clz32()

JavaScript的整数使用32位二进制形式表示，`Math.clz32`方法返回一个数的32位无符号整数形式**有多少个前导0**。`clz32`这个函数名就来自”count leading zero bits in 32-bit binary representations of a number“（计算32位整数的前导0）的缩写。左移运算符（`<<`）与`Math.clz32`方法直接相关。

```javascript
Math.clz32(0) // 32
Math.clz32(1) // 31
Math.clz32(1 << 1) // 30
Math.clz32(1 << 2) // 29
Math.clz32(1 << 29) // 2
```

### Math.hypot()

`Math.hypot`方法返回所有参数的平方和的平方根。

```javascript
Math.hypot(3, 4);        // 5
Math.hypot(3, 4, 5);     // 7.0710678118654755
Math.hypot();            // 0
Math.hypot(NaN);         // NaN
Math.hypot(3, 4, 'foo'); // NaN
Math.hypot(3, 4, '5');   // 7.0710678118654755
Math.hypot(-3);          // 3
```

### 对数方法

**（1） Math.expm1()**   == Math.exp(x) - 1;

`Math.expm1(x)`返回ex - 1，即`Math.exp(x) - 1`。

**（2）Math.log1p()**  ==  Math.log(1 + x);

`Math.log1p(x)`方法返回`1 + x`的自然对数，即`Math.log(1 + x)`。如果`x`小于-1，返回`NaN`。

**（3）Math.log10()** == Math.log(x) / Math.LN10;

`Math.log10(x)`返回以10为底的`x`的对数。如果`x`小于0，则返回NaN。

**（4）Math.log2()** == Math.log(x) / Math.LN2;

`Math.log2(x)`返回以2为底的`x`的对数。如果`x`小于0，则返回NaN。



### 三角函数

ES6新增了6个三角函数方法。

- `Math.sinh(x)` 返回`x`的双曲正弦（hyperbolic sine）
- `Math.cosh(x)` 返回`x`的双曲余弦（hyperbolic cosine）
- `Math.tanh(x)` 返回`x`的双曲正切（hyperbolic tangent）
- `Math.asinh(x)` 返回`x`的反双曲正弦（inverse hyperbolic sine）
- `Math.acosh(x)` 返回`x`的反双曲余弦（inverse hyperbolic cosine）
- `Math.atanh(x)` 返回`x`的反双曲正切（inverse hyperbolic tangent）

### ！指数运算符

指数运算符可以与等号结合，形成一个新的赋值运算符（`**=`）。

```javascript
2 ** 3 // 8

let a = 1.5;
a **= 2;
// 等同于 a = a * a;
```



## 数组

### ! Array.from()

`Array.from`方法用于将两类对象转为真正的数组：类似数组的对象（array-like object）和可遍历（iterable）的对象（包括ES6新增的数据结构Set和Map）。

下面是一个类似数组的对象，`Array.from`将它转为真正的数组。

```javascript
let arrayLike = {
    '0': 'a',
    '1': 'b',
    '2': 'c',
    length: 3
};

// ES5的写法
var arr1 = [].slice.call(arrayLike); // ['a', 'b', 'c']

// ES6的写法
let arr2 = Array.from(arrayLike); // ['a', 'b', 'c']
```

实际应用中，常见的类似数组的对象是DOM操作返回的NodeList集合，以及函数内部的`arguments`对象。`Array.from`都可以将它们转为真正的数组。再使用`forEach`方法。

```javascript
// NodeList对象
let ps = document.querySelectorAll('p');
Array.from(ps).forEach(function (p) {
  console.log(p);
});

// arguments对象
function foo() {
  var args = Array.from(arguments);
  // ...
}
```

只要是部署了Iterator接口的数据结构，`Array.from`都能将其转为数组。

```javascript
Array.from('hello')
// ['h', 'e', 'l', 'l', 'o']

let namesSet = new Set(['a', 'b'])
Array.from(namesSet) // ['a', 'b']
```

上面代码中，字符串和Set结构都具有Iterator接口，因此可以被`Array.from`转为真正的数组。



值得提醒的是，扩展运算符（`...`）也可以将某些数据结构转为数组。扩展运算符背后调用的是遍历器接口（`Symbol.iterator`），如果一个对象没有部署这个接口，就无法转换。

```javascript
// arguments对象
function foo() {
  var args = [...arguments];
}

// NodeList对象
[...document.querySelectorAll('div')]
```

`Array.from`方法则是还支持类似数组的对象。所谓类似数组的对象，本质特征只有一点，即必须有`length`属性。因此，任何有`length`属性的对象，都可以通过`Array.from`方法转为数组，而此时扩展运算符就无法转换。

```javascript
Array.from({ length: 3 });
// [ undefined, undefined, undefined ]
```

上面代码中，`Array.from`返回了一个具有三个成员的数组，每个位置的值都是`undefined`。扩展运算符转换不了这个对象。`Array.from`还可以接受第二个参数，作用类似于数组的`map`方法，用来对每个元素进行处理，将处理后的值放入返回的数组。

```javascript
Array.from(arrayLike, x => x * x);
// 等同于
Array.from(arrayLike).map(x => x * x);
// 等同于
Array.prototype.map.call(arrayLike),x => x * x);

Array.from([1, 2, 3], (x) => x * x)
// [1, 4, 9]

let spans = document.querySelectorAll('span.name');

// map()
let names1 = Array.prototype.map.call(spans, s => s.textContent);

// Array.from()
let names2 = Array.from(spans, s => s.textContent)
```

`Array.from()`可以将各种值转为真正的数组，并且还提供`map`功能。这实际上意味着，只要有一个原始的数据结构，你就可以先对它的值进行处理，然后转成规范的数组结构，进而就可以使用数量众多的数组方法。

```javascript
Array.from({ length: 2 }, () => 'jack')
// ['jack', 'jack']
```

`Array.from()`的另一个应用是，将字符串转为数组，然后返回字符串的长度。因为它能正确处理各种Unicode字符，可以避免JavaScript将大于`\uFFFF`的Unicode字符，算作两个字符的bug。

```javascript
function countSymbols(string) {
  return Array.from(string).length;
}
```

### Array.of()

`Array.of`方法用于将一组值，转换为数组。

```javascript
Array.of(3, 11, 8) // [3,11,8]
Array.of(3) // [3]
Array.of(3).length // 1
```

这个方法的主要目的，是弥补数组构造函数`Array()`的不足。因为参数个数的不同，会导致`Array()`的行为有差异。

```javascript
Array() // []
Array(3) // [, , ,]
Array(3, 11, 8) // [3, 11, 8]
```

上面代码中，`Array`方法没有参数、一个参数、三个参数时，返回结果都不一样。只有当参数个数不少于2个时，`Array()`才会返回由参数组成的新数组。参数个数只有一个时，实际上是指定数组的长度。

`Array.of`基本上可以用来替代`Array()`或`new Array()`，并且不存在由于参数不同而导致的重载。它的行为非常统一。

```javascript
Array.of() // []
Array.of(undefined) // [undefined]
Array.of(1) // [1]
Array.of(1, 2) // [1, 2]
```

`Array.of`总是返回参数值组成的数组。如果没有参数，就返回一个空数组。

`Array.of`方法可以用下面的代码模拟实现。

```javascript
function ArrayOf(){
  return [].slice.call(arguments);
}
```

### 数组实例的copyWithin()

数组实例的`copyWithin`方法，在当前数组内部，将指定位置的成员复制到其他位置（会覆盖原有成员），然后返回当前数组。也就是说，**使用这个方法，会修改当前数组。**

```javascript
Array.prototype.copyWithin(target, start = 0, end = this.length)
```

它接受三个参数。

- target（必需）：从该位置开始替换数据。
- start（可选）：从该位置开始读取数据，默认为0。**如果为负值，表示倒数。**
- end（可选）：到该位置前停止读取数据，默认等于数组长度。**如果为负值，表示倒数。**

这三个参数都应该是数值，如果不是，会自动转为数值。

```javascript
[1, 2, 3, 4, 5].copyWithin(0, 3)
// [4, 5, 3, 4, 5]
```

上面代码表示将从3号位直到数组结束的成员（4和5），复制到从0号位开始的位置，结果覆盖了原来的1和2。

下面是更多例子。

```javascript
// 将3号位复制到0号位
[1, 2, 3, 4, 5].copyWithin(0, 3, 4)
// [4, 2, 3, 4, 5]

// -2相当于3号位，-1相当于4号位
[1, 2, 3, 4, 5].copyWithin(0, -2, -1)
// [4, 2, 3, 4, 5]

// 将3号位复制到0号位
[].copyWithin.call({length: 5, 3: 1}, 0, 3)
// {0: 1, 3: 1, length: 5}

// 将2号位到数组结束，复制到0号位
var i32a = new Int32Array([1, 2, 3, 4, 5]);
i32a.copyWithin(0, 2);
// Int32Array [3, 4, 5, 4, 5]

// 对于没有部署TypedArray的copyWithin方法的平台
// 需要采用下面的写法
[].copyWithin.call(new Int32Array([1, 2, 3, 4, 5]), 0, 3, 4);
```



### Array.fill

方法使用给定值，填充一个数组(**初始化数组**)

```javascript
['a', 'b', 'c'].fill(7)
// [7, 7, 7]

new Array(3).fill(7)
// [7, 7, 7]
```

上面代码表明，`fill`方法用于空数组的初始化非常方便。数组中已有的元素，会被全部抹去。`fill`方法还可以接受第二个和第三个参数，用于指定填充的起始位置和结束位置。

```javascript
['a', 'b', 'c'].fill(7, 1, 2)
// ['a', 7, 'c']
```

上面代码表示，`fill`方法从1号位开始，向原数组填充7，到2号位之前结束。



### 数组实例的entries()，keys()和values()

ES6提供三个新的方法——`entries()`，`keys()`和`values()`——用于遍历数组。它们都返回一个**遍历器对象**，可以用`for...of`循环进行遍历，唯一的区别是`keys()`是对键名的遍历、`values()`是对键值的遍历，`entries()`是对键值对的遍历。

注：values在chrome中暂时不可用

```javascript
for (let index of ['a', 'b'].keys()) {
  console.log(index);
}
// 0
// 1

for (let elem of ['a', 'b'].values()) {
  console.log(elem);
}
// 'a'
// 'b'

for (let [index, elem] of ['a', 'b'].entries()) {
  console.log(index, elem);
}
// 0 "a"
// 1 "b"
```

如果不使用`for...of`循环，可以手动调用遍历器对象的`next`方法，进行遍历。

```javascript
let letter = ['a', 'b', 'c'];
let entries = letter.entries();
console.log(entries.next().value); // [0, 'a']
console.log(entries.next().value); // [1, 'b']
console.log(entries.next().value); // [2, 'c']
```

### 数组实例的includes()

`Array.prototype.includes`方法返回一个布尔值，表示某个数组是否包含给定的值，与字符串的`includes`方法类似。该方法属于**ES7**，但Babel转码器已经支持。用来替换indexof，它有两个缺点，一是不够语义化，它的含义是找到参数值的第一个出现位置，所以要去比较是否不等于-1，表达起来不够直观。二是，它内部使用严格相当运算符（===）进行判断，这会导致对`NaN`的误判。

```javascript
[1, 2, 3].includes(2);     // true
[1, 2, 3].includes(4);     // false
[1, 2, NaN].includes(NaN); // true

[NaN].indexOf(NaN)// -1
```

该方法的第二个参数表示搜索的起始位置，默认为0。如果第二个参数为负数，则表示倒数的位置，如果这时它大于数组长度（比如第二个参数为-4，但数组长度为3），则会重置为从0开始。

```javascript
[1, 2, 3].includes(3, 3);  // false
[1, 2, 3].includes(3, -1); // true
```

下面代码用来检查当前环境是否支持该方法，如果不支持，部署一个简易的替代版本。

```javascript
const contains = (() =>
  Array.prototype.includes
    ? (arr, value) => arr.includes(value)
    : (arr, value) => arr.some(el => el === value)   //这样还是没办法避免NaN问题，如果需要判断这个还需加强
)();
contains(["foo", "bar"], "baz"); // => false
```

另外，Map和Set数据结构有一个`has`方法，需要注意与`includes`区分。

- Map结构的`has`方法，是用来查找键名的，比如`Map.prototype.has(key)`、`WeakMap.prototype.has(key)`、`Reflect.has(target, propertyKey)`。
- Set结构的`has`方法，是用来查找值的，比如`Set.prototype.has(value)`、`WeakSet.prototype.has(value)`。

### 空位置（坑）

数组的某一个位置没有任何值。比如，`Array`构造函数返回的数组都是空位。

```
Array(3) // [, , ,]

```

上面代码中，`Array(3)`返回一个具有3个空位的数组。

注意，空位不是`undefined`，一个位置的值等于`undefined`，依然是有值的。空位是没有任何值，`in`运算符可以说明这一点。

```javascript
0 in [undefined, undefined, undefined] // true
0 in [, , ,] // false
```

上面代码说明，第一个数组的0号位置是有值的，第二个数组的0号位置没有值。

ES5对空位的处理，已经很不一致了，大多数情况下会忽略空位。

- `forEach()`, `filter()`, `every()` 和`some()`都会跳过空位。
- `map()`会跳过空位，但会保留这个值
- `join()`和`toString()`会将空位视为`undefined`，而`undefined`和`null`会被处理成空字符串。

```javascript
// forEach方法
[,'a'].forEach((x,i) => console.log(i)); // 1

// filter方法
['a',,'b'].filter(x => true) // ['a','b']

// every方法
[,'a'].every(x => x==='a') // true

// some方法
[,'a'].some(x => x !== 'a') // false

// map方法
[,'a'].map(x => 1) // [,1]

// join方法
[,'a',undefined,null].join('#') // "#a##"

// toString方法
[,'a',undefined,null].toString() // ",a,,"
```

ES6则是明确将空位转为`undefined`。

`Array.from`方法会将数组的空位，转为`undefined`，也就是说，这个方法不会忽略空位。

```javascript
Array.from(['a',,'b'])
// [ "a", undefined, "b" ]
```

扩展运算符（`...`）也会将空位转为`undefined`。

```javascript
[...['a',,'b']]
// [ "a", undefined, "b" ]
```

`copyWithin()`会连空位一起拷贝。

```javascript
[,'a','b',,].copyWithin(2,0) // [,"a",,"a"]
```

`fill()`会将空位视为正常的数组位置。

```javascript
new Array(3).fill('a') // ["a","a","a"]
```

`for...of`循环也会遍历空位。

```javascript
let arr = [, ,];
for (let i of arr) {
  console.log(1);
}
// 1
// 1
```

上面代码中，数组`arr`有两个空位，`for...of`并没有忽略它们。如果改成`map`方法遍历，空位是会跳过的。

`entries()`、`keys()`、`values()`、`find()`和`findIndex()`会将空位处理成`undefined`。

```javascript
// entries()
[...[,'a'].entries()] // [[0,undefined], [1,"a"]]

// keys()
[...[,'a'].keys()] // [0,1]

// values()
[...[,'a'].values()] // [undefined,"a"]

// find()
[,'a'].find(x => true) // undefined

// findIndex()
[,'a'].findIndex(x => true) // 0
```

由于空位的处理规则非常不统一，所以建议避免出现空位。

# 函数

## 默认值

在ES6之前，不能直接为函数的参数指定默认值，只能采用变通的方法。这种写法的缺点在于，**如果参数`y`赋值了，但是对应的布尔值为`false`，则该赋值不起作用。**为了避免这个问题，通常需要先判断一下参数`y`是否被赋值，如果没有，再等于默认值。

```javascript
function log(x, y) {
  y = y || 'World';
  console.log(x, y);
}
log('Hello') // Hello World
log('Hello', 'China') // Hello China
log('Hello', '') // Hello World           //这里不符合预计

//改成
function log(x, y) {
  if (typeof y === 'undefined') {
     y = 'World';
  }
  console.log(x, y);
}
```

ES6 允许为函数的参数设置默认值，即直接写在参数定义的后面。

```javascript
function log(x, y = 'World') {
  console.log(x, y);
}
log('Hello') // Hello World
log('Hello', 'China') // Hello China
log('Hello', '') // Hello
```

ES6 的写法还有两个好处：首先，阅读代码的人，可以立刻意识到哪些参数是可以省略的，不用查看函数体或文档；其次，有利于将来的代码优化，即使未来的版本在对外接口中，彻底拿掉这个参数，也不会导致以前的代码无法运行。

另外，一个容易忽略的地方是，**如果参数默认值是变量，那么参数就不是传值的**，而是每次都重新计算默认值表达式的值。也就是说，参数默认值是惰性求值的。

```javascript
let x = 99;
function foo(p = x + 1) {
  console.log(p);
}

foo() // 100

x = 100;
foo() // 101
```

上面代码中，参数`p`的默认值是`x + 1`。这时，每次调用函数`foo`，都会重新计算`x + 1`，而不是默认`p`等于 100。

### 与解构结合用

参数默认值可以与解构赋值的默认值，结合起来使用。

```javascript
function foo({x, y = 5}) {
  console.log(x, y);
}

foo({}) // undefined, 5
foo(1,2) // undefined, 5    //只要第一个参数不是一个对象就不会赋值
foo({x: 1}) // 1, 5
foo({x: 1, y: 2}) // 1, 2
foo() // TypeError: Cannot read property 'x' of undefined
```

上面的写法不能省略参数，如果结合函数参数的默认值，就可以省略第二个参数。**这时，就出现了双重默认值。**

```javascript
function foo({x, y = 5} = {}) {
  console.log(x, y);
}
foo() // undefined, 5
```

**注意：**

再请问下面两种写法有什么差别？

```javascript
// 写法一  使用这种
function m1({x = 0, y = 0} = {}) {
  return [x, y];
}

// 写法二  缺点是如果传入了参数，哪怕是空{},初始化就失效
function m2({x, y} = { x: 0, y: 0 }) {
  return [x, y];
}
```

上面两种写法都对函数的参数设定了默认值，区别是写法一函数参数的默认值是空对象，但是设置了对象解构赋值的默认值；写法二函数参数的默认值是一个有具体属性的对象，但是没有设置对象解构赋值的默认值。

```javascript
// 函数没有参数的情况
m1() // [0, 0]
m2() // [0, 0]

// x和y都有值的情况
m1({x: 3, y: 8}) // [3, 8]
m2({x: 3, y: 8}) // [3, 8]

// x有值，y无值的情况
m1({x: 3}) // [3, 0]
m2({x: 3}) // [3, undefined]

// x和y都无值的情况
m1({}) // [0, 0];
m2({}) // [undefined, undefined]

m1({z: 3}) // [0, 0]
m2({z: 3}) // [undefined, undefined]
```

### 参数默认值的位置

通常情况下，定义了默认值的参数，**应该是函数的尾参数**。因为这样比较容易看出来，到底省略了哪些参数。**如果非尾部的参数设置默认值，实际上这个参数是没法省略的。**除非显式输入`undefined`。如果传入`undefined`，将触发该参数等于默认值，`null`则没有这个效果。

```javascript
// 例一
function f(x = 1, y) {
  return [x, y];
}

f() // [1, undefined]
f(2) // [2, undefined])
f(, 1) // 报错
f(undefined, 1) // [1, 1]
```

指定了默认值以后，函数的`length`属性，将返回没有指定默认值的参数个数。也就是说，指定了默认值后，`length`属性将失真。这是因为`length`属性的含义是，**该函数预期传入的参数个数**。

```javascript
(function (a) {}).length // 1
(function (a = 5) {}).length // 0
(function (a, b, c = 5) {}).length // 2
```

如果设置了默认值的参数不是尾参数，那么`length`属性也不再计入后面的参数了。

```javascript
(function (a = 0, b, c) {}).length // 0
(function (a, b = 1, c) {}).length // 1
```

### 作用域

一旦设置了参数的默认值，函数进行声明初始化时，**参数会形成一个单独的作用域（context）**。等到初始化结束，这个作用域就会消失。这种语法行为，在不设置参数默认值时，是不会出现的。

```javascript
var x = 1;

function f(x, y = x) {
  console.log(y);
}

f(2) // 2
```

上面代码中，参数`y`的默认值等于变量`x`。调用函数`f`时，参数形成一个单独的作用域。在这个作用域里面，默认值变量`x`指向第一个参数`x`，而不是全局变量`x`，所以输出是`2`。

再看下面的例子。

```javascript
let x = 1;
function f(y = x) {   //如果不定义x会报错
  let x = 2;
  console.log(y);
}
f() // 1
```

上面代码中，函数`f`调用时，参数`y = x`形成一个单独的作用域。这个作用域里面，变量`x`本身没有定义，所以指向外层的全局变量`x`。函数调用时，函数体内部的局部变量`x`影响不到默认值变量`x`。



面是一个更复杂的例子。

```javascript
var x = 1;
function foo(x, y = function() { x = 2; }) {
  var x = 3;   //和第一个参数x不一样
  y();   //改变的是第一个参数x
  console.log(x); 
}

foo() // 3
x // 1
```

```javascript
var x = 1;
function foo(x, y = function() { x = 2; }) {
  x = 3;    //和第一个参数x一致
  y();      //改变了第一个参数x
  console.log(x);  //2
}

foo() // 2
x // 1
```

### 应用

利用参数默认值，**可以指定某一个参数不得省略，如果省略就抛出一个错误。**

```javascript
function throwIfMissing() {
  throw new Error('Missing parameter');
}

function foo(mustBeProvided = throwIfMissing()) {
  return mustBeProvided;
}

foo()
// Error: Missing parameter
```

上面代码的`foo`函数，如果调用的时候没有参数，就会调用默认值`throwIfMissing`函数，从而抛出一个错误。



另外，可以将参数默认值设为`undefined`，表明这个参数是可以省略的。

```javascript
function foo(optional = undefined) { ··· }
```

## rest参数

ES6 引入 rest 参数（形式为`...变量名`），用于获取函数的多余参数，这样就不需要使用`arguments`对象了。rest 参数搭配的变量是一个数组，该变量将多余的参数放入**数组**中。

```
function add(...values) {
	values //[2,5,3]
}

add(2, 5, 3) // 10
```

**由于rest参数本来就是数组，所以它不需要做数组转换就可以用数组的方法**(sort，forEach……)

```javascript
// arguments变量的写法
function sortNumbers() {
  return Array.prototype.slice.call(arguments).sort();
}

// rest参数的写法
const sortNumbers = (...numbers) => numbers.sort();
```

注意，rest 参数之后不能再有其他参数（即只能是最后一个参数），否则会报错。

```javascript
// 报错
function f(a, ...b, c) {
  // ...
}
```

函数的`length`属性，不包括 rest 参数。

```javascript
(function(a) {}).length  // 1
(function(...a) {}).length  // 0
(function(a, ...b) {}).length  // 1
```

### 扩展运算符

扩展运算符（spread）是三个点（`...`）。它好比 **rest 参数的逆运算**，将**一个数组转为用逗号分隔的参数序列**。

```javascript
console.log(...[1, 2, 3])
// 1 2 3

console.log(1, ...[2, 3, 4], 5)
// 1 2 3 4 5

[...document.querySelectorAll('div')]
// [<div>, <div>, <div>]
```

该运算符主要用于函数调用。

```javascript
function push(array, ...items) {
  array.push(...items);   //把items转换成序列
}

function add(x, y) {
  return x + y;
}

var numbers = [4, 38];
add(...numbers) // 42
```

上面代码中，`array.push(...items)`和`add(...numbers)`这两行，都是函数的调用，它们的都使用了扩展运算符。该运算符将一个数组，变为参数序列。扩展运算符与正常的函数参数可以结合使用，非常灵活。

```javascript
function f(v, w, x, y, z) { }
var args = [0, 1];
f(-1, ...args, 2, ...[3]);
```

### ！扩展运算符作用

1. **替代apply**

   由于扩展运算符可以展开数组，所以不再需要`apply`方法，将数组转为函数的参数了。

```javascript
// 1. 求数组最大值

// ES5的写法
Math.max.apply(null, [14, 3, 77])

// ES6的写法
Math.max(...[14, 3, 77])

// 2. 给Date传参

// ES5
new (Date.bind.apply(Date, [null, 2015, 1, 1]))
// ES6
new Date(...[2015, 1, 1]);


// 3. 添加数组到尾部

// ES5的写法
var arr1 = [0, 1, 2];
var arr2 = [3, 4, 5];
Array.prototype.push.apply(arr1, arr2);
// ES6的写法
arr1.push(...arr2);

// 4. 合并数组

// ES5
arr1.concat(arr2, arr3);

// ES6
[...arr1, ...arr2, ...arr3]
```

1. 扩展运算符可以与**解构赋值**结合起来，**用于生成数组。**如果将扩展运算符用于数组赋值，**只能放在参数的最后一位**，否则会报错。

```javascript
// ES5
a = list[0], rest = list.slice(1)
// ES6
[a, ...rest] = list

const [first, ...rest] = [1, 2, 3, 4, 5];
first // 1
rest  // [2, 3, 4, 5]

const [first, ...middle, last] = [1, 2, 3, 4, 5];
// 报错
```

1. **字符串**

扩展运算符还可以将字符串转为真正的数组。

```javascript
[...'hello']
// [ "h", "e", "l", "l", "o" ]
```

上面的写法，有一个重要的好处，**那就是能够正确识别32位的Unicode字符。**

```javascript
'x\uD83D\uDE80y'.length // 4
[...'x\uD83D\uDE80y'].length // 3
```

上面代码的第一种写法，JavaScript会将32位Unicode字符，识别为2个字符，采用扩展运算符就没有这个问题。因此，正确返回字符串长度的函数，可以像下面这样写。

```javascript
function length(str) {
  return [...str].length;
}

length('x\uD83D\uDE80y') // 3
```

凡是涉及到操作32位Unicode字符的函数，都有这个问题。因此，最好都用扩展运算符改写。

```javascript
let str = 'x\uD83D\uDE80y';

str.split('').reverse().join('')
// 'y\uDE80\uD83Dx'

[...str].reverse().join('')
// 'y\uD83D\uDE80x'
```

上面代码中，如果不用扩展运算符，字符串的`reverse`操作就不正确

1. **转换所有带Iterator接口的对象**

   扩展运算符内部调用的是数据结构的Iterator接口，因此只要具有Iterator接口的对象，都可以使用扩展运算符，比如Map结构。

```javascript
let map = new Map([
  [1, 'one'],
  [2, 'two'],
  [3, 'three'],
]);

let arr = [...map.keys()]; // [1, 2, 3]
```

Generator函数运行后，返回一个遍历器对象，因此也可以使用扩展运算符。

```javascript
var go = function*(){
  yield 1;
  yield 2;
  yield 3;
};

[...go()] // [1, 2, 3]
```

上面代码中，变量`go`是一个Generator函数，执行后返回的是一个遍历器对象，对这个遍历器对象执行扩展运算符，就会将内部遍历得到的值，转为一个数组。

如果对没有`iterator`接口的对象，使用扩展运算符，将会报错。

```javascript
var obj = {a: 1, b: 2};
let arr = [...obj]; // TypeError: Cannot spread non-iterable object
```

## name 属性

函数的`name`属性，返回该函数的函数名。

```javascript
function foo() {}
foo.name // "foo"
```

这个属性早就被浏览器广泛支持，但是直到 ES6，才将其写入了标准。

需要注意的是，ES6 对这个属性的行为做出了一些修改。如果将一个匿名函数赋值给一个变量，ES5 的`name`属性，会返回空字符串，而 ES6 的`name`属性会返回实际的函数名。

```javascript
var f = function () {};

// ES5
f.name // ""

// ES6
f.name // "f"
```

上面代码中，变量`f`等于一个匿名函数，ES5 和 ES6 的`name`属性返回的值不一样。

如果将一个具名函数赋值给一个变量，则 ES5 和 ES6 的`name`属性都返回这个具名函数原本的名字。

```javascript
const bar = function baz() {};

// ES5
bar.name // "baz"

// ES6
bar.name // "baz"
```

`Function`构造函数返回的函数实例，`name`属性的值为`anonymous`。

```javascript
(new Function).name // "anonymous"
```

`bind`返回的函数，`name`属性值会加上`bound`前缀。

```javascript
function foo() {};
foo.bind({}).name // "bound foo"

(function(){}).bind({}).name // "bound "
```

## 箭头函数

如果没有参数，必须要用一个括号，只有一个参数，不用写括号，返回值不用写return

```javascript
var f = v => v;
//等于
var f = function(v) {
  return v;
};
var f = () => 5;
var sum = (num1, num2) => num1 + num2;
```

### 返回对象

如果只是返回对象，必须要加一个括号，否则编译器认不出

```javascript
var getTempItem = id => ({ id: id, name: "Temp" });
```

### 与解构

```javascript
const full = ({ first, last }) => first + ' ' + last;

// 等同于
function full(person) {
  return person.first + ' ' + person.last;
}
```

### 简化回调

```javascript
// 正常函数写法
[1,2,3].map(function (x) {
  return x * x;
});

// 箭头函数写法
[1,2,3].map(x => x * x);

// 正常函数写法
var result = values.sort(function (a, b) {
  return a - b;
});

// 箭头函数写法
var result = values.sort((a, b) => a - b);
```

### 与rest结合

```javascript
const numbers = (...nums) => nums;

numbers(1, 2, 3, 4, 5)
// [1,2,3,4,5]
```

### this

箭头函数有几个使用注意点。

（1）函数体内的`this`对象，就是定义时所在的对象，而不是使用时所在的对象。

（2）**不可以当作构造函数**，也就是说，不可以使用`new`命令，否则会抛出一个错误。

（3）不可以使用`arguments`对象，该对象在函数体内不存在。如果要用，**可以用Rest参数代替**。

（4）不可以使用`yield`命令，因此箭头函数**不能用作Generator函数**。

箭头函数可以让`this`指向固定化，这种特性很有利于封装回调函数。下面是一个例子，DOM事件的回调函数封装在一个对象里面。

```javascript
var handler = {
  id: '123456',

  init: function() {
    document.addEventListener('click',
      event => this.doSomething(event.type), false);   //如果不用箭头函数那this指向document
  },

  doSomething: function(type) {
    console.log('Handling ' + type  + ' for ' + this.id);
  }
};
```

所以，箭头函数转成ES5的代码如下。

```javascript
// ES6
function foo() {
  setTimeout(() => {
    console.log('id:', this.id);
  }, 100);
}

// ES5
function foo() {
  var _this = this;    //这里引用了外面的this
 
  setTimeout(function () {
    console.log('id:', _this.id);
  }, 100);
}
```

另外，由于箭头函数没有自己的this，所以当然也就不能用call()、apply()、bind()这些方法去改变this的指向。

```javascript
(function() {
  return [
    (() => this.x).bind({ x: 'inner' })()
  ];
}).call({ x: 'outer' });
// ['outer']
```

### arguments,super,target

除了`this`，以下三个变量在箭头函数之中也是不存在的，指向外层函数的对应变量：`arguments`、`super`、`new.target`。

```javascript
function foo() {
  setTimeout(() => {
    console.log('args:', arguments);
  }, 100);
}

foo(2, 4, 6, 8)
// args: [2, 4, 6, 8]
```

## 尾调用优化

```javascript
function f(x){
  return g(x);
}
```

尾调用不一定出现在函数尾部，只要是最后一步操作即可。

```javascript
function f(x) {
  if (x > 0) {
    return m(x)
  }
  return n(x);
}
```

以下不是尾调用

```javascript
// 情况一
function f(x){
  let y = g(x);
  return y;
}

// 情况二
function f(x){
  return g(x) + 1;  // ==  var tmp = g(x); return tmp+1;
}

// 情况三
function f(x){
  g(x);    //  g(x);   return undefined;
}
```

**尾调用由于是函数的最后一步操作，所以不需要保留外层函数的调用帧**，因为调用位置、内部变量等信息都不会再用到了，只要直接用内层函数的调用帧，取代外层函数的调用帧就可以了。

```javascript
function f() {
  let m = 1;
  let n = 2;
  return g(m + n);
}
f();

// 等同于
function f() {
  return g(3);
}
f();

// 等同于
g(3);
```

上面代码中，如果函数g不是尾调用，函数f就需要保存内部变量m和n的值、g的调用位置等信息。但由于调用g之后，函数f就结束了，所以执行到最后一步，完全可以删除 f(x) 的调用帧，只保留 g(3) 的调用帧。

这就叫做“尾调用优化”（Tail call optimization），即只保留内层函数的调用帧。如果所有函数都是尾调用，那么完全可以做到每次执行时，调用帧只有一项，这将大大节省内存。这就是“尾调用优化”的意义。

**注意，只有不再用到外层函数的内部变量，内层函数的调用帧才会取代外层函数的调用帧，否则就无法进行“尾调用优化”。**

```javascript
function addOne(a){
  var one = 1;
  function inner(b){
    return b + one;
  }
  return inner(a);
}
```

上面的函数不会进行尾调用优化，因为内层函数`inner`用到了外层函数`addOne`的内部变量`one`。



## 尾递归

递归非常耗费内存，因为需要同时保存成千上百个调用帧，很容易发生“栈溢出”错误（stack overflow）。但**对于尾递归来说，由于只存在一个调用帧，所以永远不会发生“栈溢出”错误。**

```javascript
function factorial(n) {
  if (n === 1) return 1;
  return n * factorial(n - 1);
}

factorial(5) // 120
```

上面代码是一个阶乘函数，计算n的阶乘，最多需要保存n个调用记录，复杂度 O(n) 。

如果改写成尾递归，只保留一个调用记录，复杂度 O(1) 。

```javascript
function factorial(n, total) {
  if (n === 1) return total;
  return factorial(n - 1, n * total);
}

factorial(5, 1) // 120
```

还有一个比较著名的例子，就是计算fibonacci 数列，也能充分说明尾递归优化的重要性

如果是非尾递归的fibonacci 递归方法

```javascript
function Fibonacci (n) {
  if ( n <= 1 ) {return 1};
  return Fibonacci(n - 1) + Fibonacci(n - 2);
}

Fibonacci(10); // 89
// Fibonacci(100)
// Fibonacci(500)
// 堆栈溢出了
```

如果我们使用尾递归优化过的fibonacci 递归算法

```javascript
function Fibonacci2 (n , ac1 = 1 , ac2 = 1) {
  if( n <= 1 ) {return ac2};
  return Fibonacci2 (n - 1, ac2, ac1 + ac2);
}

Fibonacci2(100) // 573147844013817200000
Fibonacci2(1000) // 7.0330367711422765e+208
Fibonacci2(10000) // Infinity
```

由此可见，“尾调用优化”对递归操作意义重大，所以一些函数式编程语言将其写入了语言规格。**ES6也是如此，第一次明确规定，所有ECMAScript的实现，都必须部署“尾调用优化”。**这就是说，在ES6中，只要使用尾递归，就不会发生栈溢出，相对节省内存。

### 尾递归优化的实现

尾递归优化只在严格模式下生效，那么正常模式下，或者那些不支持该功能的环境中，有没有办法也使用尾递归优化呢？回答是可以的，就是自己实现尾递归优化。

它的原理非常简单。尾递归之所以需要优化，原因是调用栈太多，造成溢出，那么只要减少调用栈，就不会溢出。怎么做可以减少调用栈呢？就是采用“循环”换掉“递归”。

下面是一个正常的递归函数。

```javascript
function sum(x, y) {
  if (y > 0) {
    return sum(x + 1, y - 1);
  } else {
    return x;
  }
}

sum(1, 100000)
// Uncaught RangeError: Maximum call stack size exceeded(…)
```

上面代码中，`sum`是一个递归函数，参数`x`是需要累加的值，参数`y`控制递归次数。一旦指定`sum`递归100000次，就会报错，提示超出调用栈的最大次数。

蹦床函数（trampoline）可以将递归执行转为循环执行。

```javascript
function trampoline(f) {
  while (f && f instanceof Function) {
    f = f();
  }
  return f;
}
```

上面就是蹦床函数的一个实现，它接受一个函数`f`作为参数。只要`f`执行后返回一个函数，就继续执行。注意，这里是返回一个函数，然后执行该函数，而不是函数里面调用函数，这样就避免了递归执行，从而就消除了调用栈过大的问题。

然后，要做的就是将原来的递归函数，改写为每一步返回另一个函数。

```javascript
function sum(x, y) {
  if (y > 0) {
    return sum.bind(null, x + 1, y - 1);
  } else {
    return x;
  }
}
```

上面代码中，`sum`函数的每次执行，都会返回自身的另一个版本。

现在，使用蹦床函数执行`sum`，就不会发生调用栈溢出。

```javascript
trampoline(sum(1, 100000))
// 100001
```

蹦床函数并不是真正的尾递归优化，下面的实现才是。

```javascript
function tco(f) {
  var value;
  var active = false;
  var accumulated = [];

  return function accumulator() {
    accumulated.push(arguments);
    if (!active) {
      active = true;
      while (accumulated.length) {
        value = f.apply(this, accumulated.shift());
      }
      active = false;
      return value;
    }
  };
}

var sum = tco(function(x, y) {
  if (y > 0) {
    return sum(x + 1, y - 1)
  }
  else {
    return x
  }
});

sum(1, 100000)
// 100001
```

上面代码中，`tco`函数是尾递归优化的实现，它的奥妙就在于状态变量`active`。默认情况下，这个变量是不激活的。一旦进入尾递归优化的过程，这个变量就激活了。然后，每一轮递归`sum`返回的都是`undefined`，所以就避免了递归执行；而`accumulated`数组存放每一轮`sum`执行的参数，总是有值的，这就保证了`accumulator`函数内部的`while`循环总是会执行。这样就很巧妙地将“递归”改成了“循环”，而后一轮的参数会取代前一轮的参数，保证了调用栈只有一层。





# 对象

## 属性简洁表达

ES6允许**直接写入变量和函数，作为对象的属性和方法**。这样的书写更加简洁。

```javascript
var foo = 'bar';
var baz = {foo};
baz // {foo: "bar"}
```

属性名直接是变量名，属性值是变量值

```javascript
var birth = '2000/01/01';

var Person = {
  name: '张三',
  //等同于birth: '2000/01/01';
  birth,
  // 等同于hello: function ()...
  hello() { console.log('我的名字是', this.name); }
};
```

CommonJS模块输出变量，就非常合适使用简洁写法。

```javascript
var ms = {};

function getItem (key) {
  return key in ms ? ms[key] : null;
}

function setItem (key, value) {
  ms[key] = value;
}

function clear () {
  ms = {};
}

module.exports = { getItem, setItem, clear };
// 等同于
module.exports = {
  getItem: getItem,
  setItem: setItem,
  clear: clear
};
```

注意，简洁写法的属性名总是字符串，这会导致一些看上去比较奇怪的结果。

```javascript
var obj = {
  class () {}   //class是字符串，所以不会因为它属于关键字，而导致语法解析报错。
};
// 等同于
var obj = {
  'class': function() {}    
};
```

如果某个方法的值是一个Generator函数，前面需**要加上星号。**

```javascript
var obj = {
  * m(){
    yield 'hello world';
  }
};
```



## 字面量中可用[]

ES6 允许字面量定义对象时，用方法二（表达式）作为对象的属性名，**即把表达式放在方括号内**。

```javascript
let propKey = 'foo';

let obj = {
  [propKey]: true,
  ['a' + 'bc']: 123
};
//Object {foo: true, abc: 123}
```

表达式还可以用于定义方法名。

```javascript
let obj = {
  ['h' + 'ello']() {
    return 'hi';
  }
};

obj.hello() // hi
```

**注意**，属性名表达式与简洁表示法，不能同时使用，会报错。

```javascript
// 报错
var foo = 'bar';
var bar = 'abc';
var baz = { [foo] };

// 正确
var foo = 'bar';
var baz = { [foo]: 'abc'};
var baz = {foo}
```

**注意**，属性名表达式如果是一个对象，默认情况下会自动将对象转为字符串`[object Object]`，这一点要特别小心。

```javascript
const keyA = {a: 1};
const keyB = {b: 2};

const myObject = {
  [keyA]: 'valueA',
  [keyB]: 'valueB'
};

myObject // Object {[object Object]: "valueB"}
//[keyA]和[keyB]得到的都是[object Object]，所以[keyB]会把[keyA]覆盖掉，而myObject最后只有一个[object Object]属性。
```