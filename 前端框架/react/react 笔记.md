---
date: 2019-03-31
categories: 
- 前端框架
- react
title: react 笔记
---

## 定义组件

```
// 1. 传统写法
const App = React.createClass({});

// 2. es6 的写法
class App extends React.Component({});

// 3. stateless 的写法（我们推荐的写法）
const App = (props) => ({});
```

其中第1种是我们不推荐的写法，第2种是在你的组件涉及 react 的生命周期方法的时候采用这种写法，而第3种则是我们一般推荐的写法。详细内容可以参看[Stateless Functions](https://facebook.github.io/react/docs/reusable-components.html#stateless-functions)。

### 更新元素渲染

React 元素都是不可变的。当元素被创建之后，你是无法改变其内容或属性的。

目前更新界面的唯一办法是创建一个新的元素，然后将它传入 ReactDOM.render() 方法

```js
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>现在是 {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(
    element,
    document.getElementById('example')
  );
}
 
setInterval(tick, 1000);
```

**React `只会更新必要`的部分**

值得注意的是 React DOM 首先会比较元素内容先后的不同，而在渲染过程中只会更新改变了的部分。



## JSX

JSX 是一个看起来很像 XML 的 JavaScript 语法扩展。我们不需要一定使用 JSX，但它有以下优点：

- JSX 执行更快，因为它在编译为 JavaScript 代码后进行了优化。
- 它是类型安全的，在编译过程中就能发现错误。
- 使用 JSX 编写模板更加简单快速。



添加自定义属性需要使用` data- 前缀`。

### 独立文件

你的 React JSX 代码可以放在一个独立文件上，例如我们创建一个 `helloworld_react.js` 文件，代码如下：

```js
ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.getElementById('example')
);
```

然后在 HTML 文件中引入该 JS 文件：

```html
<body>
  <div id="example"></div>
<script type="text/babel" src="helloworld_react.js"></script>
</body>
```



### JavaScript 表达式

我们可以在 JSX 中使用 JavaScript 表达式。表达式写在``花括号 {} `中。

在 JSX 中不能使用 **if else** 语句，但可以使用 **conditional (三元运算)** 表达式来替代。以下实例中如果变量 **i** 等于 **1** 浏览器将输出 **true**, 如果修改 i 的值，则会输出 **false**.

在JavaScript中，表达式就是一个短语，Javascript解释器会将其计算出一个结果，常量就是最简单的一类表达式。常用的表达式有：

- 变量名；
- 函数定义表达式；
- 属性访问表达式；
- 函数调用表达式；
- 算数表达式；
- 关系表达式；
- 逻辑表达式；

需要注意的是，if语句以及for循环不是JavaScript表达式，不能直接作为表达式写在{}中，但可以先将其赋值给一个变量（变量是一个JavaScript表达式）:

```js
function NumberDescriber(props) {
  let description;
  if (props.number % 2 == 0) {
    description = <strong>even</strong>;
  } else {
    description = <i>odd</i>;
  }
  return <div>{props.number} is an {description} number</div>;
}
```

你可以使用引号将字符串字面量指定为属性值：

```
const element = <div tabIndex="0"></div>;
```

或者你可以将一个`JavaScript表达式`嵌在一个大括号中作为属性值：

```
const element = <img src={user.avatarUrl}></img>;1
```

这里用到的是JavaScript属性访问表达式，上面的代码将编译为：

```
const element = React.createElement("img", { src: user.avatarUrl });
```



## 组件

> 注意，原生 HTML 元素名以小写字母开头，而自定义的 React 类名以大写字母开头，比如 HelloMessage 不能写成 helloMessage。除此之外还需要注意组件类只能包含一个顶层标签，否则也会报错。

 如果我们需要向组件传递参数，可以使用 **this.props** 对象

```js
function HelloMessage(props) {
    return <h1>Hello {props.name}!</h1>;
}
 
const element = <HelloMessage name="Runoob"/>;
```



注意，在添加属性时， `class 属性需要写成 className` ，`for 属性需要写成 htmlFor` ，这是因为 class 和 for 是 JavaScript 的保留字。





### 组件组合

```js
function Name(props) {
	return <h1>网站名称：{props.name}</h1>;
}
function Url(props) {
	return <h1>网站地址：{props.url}</h1>;
}
function Nickname(props) {
	return <h1>网站小名：{props.nickname}</h1>;
}
function App() {
	return (
	<div>
		<Name name="菜鸟教程" />
		<Url url="http://www.runoob.com" />
		<Nickname nickname="Runoob" />
	</div>
	);
}

ReactDOM.render(
	 <App />,
	document.getElementById('example')
);
```





## props 和 state

state 和 props 主要的区别在于 **props** 是不可变的，而 state 可以根据与用户交互来改变。这就是为什么有些容器组件需要定义 state 来更新和修改数据。 **而子组件只能通过 props 来传递数据。**



### 默认props

你可以通过组件类的 `defaultProps` 属性为 props 设置默认值`HelloMessage.defaultProps = { name: 'Runoob' };` 

### props验证

> React.PropTypes 在 React v15.5 版本后已经移到了 **prop-types** 库。

Props 验证使用 **propTypes**，它可以保证我们的应用组件被正确使用，React.PropTypes 提供很多验证器 (validator) 来验证传入数据是否有效。当向 props 传入无效数据时，JavaScript 控制台会抛出警告。

```js
MyTitle.propTypes = {
  title: PropTypes.string
};
```

`React.PropTypes.string.isRequired`

整体说明：

```js
MyComponent.propTypes = {
    // 可以声明 prop 为指定的 JS 基本数据类型，默认情况，这些数据是可选的
   optionalArray: React.PropTypes.array,
    optionalBool: React.PropTypes.bool,
    optionalFunc: React.PropTypes.func,
    optionalNumber: React.PropTypes.number,
    optionalObject: React.PropTypes.object,
    optionalString: React.PropTypes.string,
 
    // 可以被渲染的对象 numbers, strings, elements 或 array
    optionalNode: React.PropTypes.node,
 
    //  React 元素
    optionalElement: React.PropTypes.element,
 
    // 用 JS 的 instanceof 操作符声明 prop 为类的实例。
    optionalMessage: React.PropTypes.instanceOf(Message),
 
    // 用 enum 来限制 prop 只接受指定的值。
    optionalEnum: React.PropTypes.oneOf(['News', 'Photos']),
 
    // 可以是多个对象类型中的一个
    optionalUnion: React.PropTypes.oneOfType([
      React.PropTypes.string,
      React.PropTypes.number,
      React.PropTypes.instanceOf(Message)
    ]),
 
    // 指定类型组成的数组
    optionalArrayOf: React.PropTypes.arrayOf(React.PropTypes.number),
 
    // 指定类型的属性构成的对象
    optionalObjectOf: React.PropTypes.objectOf(React.PropTypes.number),
 
    // 特定 shape 参数的对象
    optionalObjectWithShape: React.PropTypes.shape({
      color: React.PropTypes.string,
      fontSize: React.PropTypes.number
    }),
 
    // 任意类型加上 `isRequired` 来使 prop 不可空。
    requiredFunc: React.PropTypes.func.isRequired,
 
    // 不可空的任意类型
    requiredAny: React.PropTypes.any.isRequired,
 
    // 自定义验证器。如果验证失败需要返回一个 Error 对象。不要直接使用 `console.warn` 或抛异常，因为这样 `oneOfType` 会失效。
    customProp: function(props, propName, componentName) {
      if (!/matchme/.test(props[propName])) {
        return new Error('Validation failed!');
      }
    }
  }
}
```



## React事件

React 元素的事件处理和 DOM 元素类似。但是有一点语法上的不同:

- React 事件绑定属性的命名采用驼峰式写法，而不是小写。
- 如果采用 JSX 的语法你需要传入一个函数作为事件处理函数，而不是一个字符串(DOM 元素的写法)

HTML 通常写法是：

```html
<button onclick="activateLasers()">
  激活按钮
</button>
```

React 中写法为：

```html
<button onClick={activateLasers}>
  激活按钮
</button>
```

### 阻止默认行为

在 React 中另一个不同是你不能使用返回 **false** 的方式阻止默认行为， 你必须明确的使用 `preventDefault。`

例如，通常我们在 HTML 中阻止链接默认打开一个新页面，可以这样写：

```html
<a href="#" onclick="console.log('点击链接'); return false">
  点我
</a>
```

在 React 的写法为：

```js
function ActionLink() {
  function handleClick(e) {
    e.preventDefault();
    console.log('链接被点击');
  }
 
  return (
    <a href="#" onClick={handleClick}>
      点我
    </a>
  );
}
```

### 绑定this

```js
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};
 
    // 这边绑定是必要的，这样 `this` 才能在回调函数中使用
    this.handleClick = this.handleClick.bind(this);
  }
 
  handleClick() {
    this.setState(prevState => ({
      isToggleOn: !prevState.isToggleOn
    }));
  }
 
  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}
 
ReactDOM.render(
  <Toggle />,
  document.getElementById('example')
);
```

你必须谨慎对待 JSX 回调函数中的 this，类的方法默认是不会绑定 this 的。如果你忘记绑定 this.handleClick 并把它传入 onClick, 当你调用这个函数的时候 this 的值会是 undefined。

这并不是 React 的特殊行为；它是函数如何在 JavaScript 中运行的一部分。`通常情况下，如果你没有在方法后面添加 () ，例如 onClick={this.handleClick}，你应该为这个方法绑定 this。`



如果使用 bind 让你很烦，这里有两种方式可以解决。如果你正在使用实验性的属性初始化器语法，你可以使用属性初始化器来正确的绑定回调函数：

```js
class LoggingButton extends React.Component {
  // 这个语法确保了 `this` 绑定在  handleClick 中
  // 这里只是一个测试
  handleClick = () => {
    console.log('this is:', this);
  }
 
  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
}
```

如果你没有使用属性初始化器语法，你可以在回调函数中使用 箭头函数：

```js
class LoggingButton extends React.Component {
  handleClick() {
    console.log('this is:', this);
  }
 
  render() {
    //  这个语法确保了 `this` 绑定在  handleClick 中
    return (
      <button onClick={(e) => this.handleClick(e)}>
        Click me
      </button>
    );
  }
}
```

使用这个语法有个问题就是每次 LoggingButton 渲染的时候都会创建一个不同的回调函数。在大多数情况下，这没有问题。**然而如果这个回调函数作为一个属性值传入低阶组件，这些组件可能会进行额外的重新渲染。我们通常建议在构造函数中绑定或使用属性初始化器语法来避免这类性能问题。**



## 向事件处理 程序传递参数

通常我们会为事件处理程序传递额外的参数。例如，若是 id 是你要删除那一行的 id，以下两种方式都可以向事件处理程序传递参数：

```
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

上述两种方式是等价的。

上面两个例子中，参数 e 作为 React 事件对象将会被作为第二个参数进行传递。通过箭头函数的方式，事件对象必须显式的进行传递，但是通过 bind 的方式，事件对象以及更多的参数将会被隐式的进行传递。

值得注意的是，通过 bind 方式向监听函数传参，在类组件中定义的监听函数，事件对象 e 要排在所传递参数的后面，例如:

```js
class Popper extends React.Component{
    constructor(){
        super();
        this.state = {name:'Hello world!'};
    }
    
    preventPop(name, e){    //事件对象e要放在最后
        e.preventDefault();
        alert(name);
    }
    
    render(){
        return (
            <div>
                <p>hello</p>
                {/* 通过 bind() 方法传递参数。 */}
                <a href="https://reactjs.org" onClick={this.preventPop.bind(this,this.state.name)}>Click</a>
            </div>
        );
    }
}
```



## React条件渲染

### 函数

使用 JavaScript 操作符 if 或条件运算符来创建表示当前状态的元素，然后让 React 根据它们来更新 UI。

```js
function Greeting(props) {
  const isLoggedIn = props.isLoggedIn;
  if (isLoggedIn) {
    return <UserGreeting />;
  }
  return <GuestGreeting />;
}
ReactDOM.render(
  // 尝试修改 isLoggedIn={true}:
  <Greeting isLoggedIn={false} />,
  document.getElementById('example')
);
```

### &&

在 JavaScript 中，true && expression 总是返回 **expression**，而 false && expression 总是返回 **false**。因此，如果条件是 **true**，&& 右侧的元素就会被渲染，如果是 **false**，React 会忽略并跳过它。

```js
function Mailbox(props) {
  const unreadMessages = props.unreadMessages;
  return (
    <div>
      <h1>Hello!</h1>
      {unreadMessages.length > 0 &&
        <h2>
          您有 {unreadMessages.length} 条未读信息。
        </h2>
      }
    </div>
  );
}
```

### 三目运算符

```js
render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      {isLoggedIn ? (
        <LogoutButton onClick={this.handleLogoutClick} />
      ) : (
        <LoginButton onClick={this.handleLoginClick} />
      )}
    </div>
  );
}
```



### 阻止渲染

在极少数情况下，你可能希望隐藏组件，即使它被其他组件渲染。让 render 方法返回 null 而不是它的渲染结果即可实现。

```js
function WarningBanner(props) {
  if (!props.warn) {
    return null;
  }
 
  return (
    <div className="warning">
      警告!
    </div>
  );
}
```





# React 组件生命周期

在本章节中我们将讨论 React 组件的生命周期。

组件的生命周期可分成三个状态：

- Mounting：已插入真实 DOM
- Updating：正在被重新渲染
- Unmounting：已移出真实 DOM

生命周期的方法有：

- **componentWillMount** `在渲染前调用`,在客户端也在服务端。
- **componentDidMount** : `在第一次渲染后调用`，只在客户端。之后组件已经`生成了对应的DOM结构`，可以通过**this.getDOMNode()来进行访问**。 如果你想和其他JavaScript框架一起使用，可以在这个方法中调用setTimeout, setInterval或者发送AJAX请求等操作(防止异部操作阻塞UI)。
- **componentWillReceiveProps** 在组件接收到一个新的 prop (更新后)时被调用。这个方法在初始化render时不会被调用。
- **shouldComponentUpdate** 返回一个布尔值。`在组件接收到新的props或者state时被调用`。在初始化时或者使用forceUpdate时不被调用。 
  可以在你确认不需要更新组件时使用。
- **componentWillUpdate**在组件接收到新的props或者state但还没有render时被调用。在初始化时不会被调用。
- **componentDidUpdate** 在组件完成更新后立即调用。在初始化时不会被调用。
- **componentWillUnmount**在组件`从 DOM 中移除的时候立刻被调用`。

> React 组件的数据可以通过 componentDidMount 方法中的 Ajax 来获取，当从服务端获取数据时可以将数据存储在 state 中，再用 this.setState 方法重新渲染 UI。当使用异步加载数据时，在组件卸载前使用 componentWillUnmount 来取消未完成的请求。







## React 列表渲染

组件接收数组参数，每个列表元素分配一个 key，不然会出现警告 a key should be provided for list items，意思就是需要包含 key

```js
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li key={number.toString()}>
      {number}
    </li>
  );
  return (
    <ul>{listItems}</ul>
  );
}
 
const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('example')
);
```

如果列表可以重新排序，我们不建议使用索引来进行排序，因为这会导致渲染变得很慢。

JSX 允许在大括号中嵌入任何表达式，所以我们可以在 map() 中这样使用：

```js
function NumberList(props) {
  const numbers = props.numbers;
  return (
    <ul>
      {numbers.map((number) =>
        <ListItem key={number.toString()}
                  value={number} />
 
      )}
    </ul>
  );
}
```





# React组件API



- **设置状态**：`setState`(object nextState[, function callback])

  - **setState()并不会立即改变this.state，而是创建一个即将处理的state**。setState()并`不一定是同步的`，为了提升性能React会批量执行state和DOM渲染。

    setState()总是会触发一次组件重绘，除非在shouldComponentUpdate()中实现了一些条件渲染逻辑。

- （全体）替换状态：`replaceState`(object nextState[, function callback])

  - **nextState**，将要设置的新状态，该状态会替换当前的**state**。
  - **callback**，可选参数，回调函数。该函数会在**replaceState**设置成功，且组件重新渲染后调用。

  **replaceState()**方法与**setState()**类似，但是方法只会保留**nextState**中状态，`原state不在nextState中的状态都会被删除。`

- 设置属性：`setProps`(object nextProps[, function callback])

  - 设置组件属性，并`重新渲染组件`。
  - **props**相当于组件的数据流，它总是会从父组件向下传递至所有的子组件中。当和一个外部的JavaScript应用集成时，我们可能会需要向组件传递数据或通知**React.render()**组件需要重新渲染，可以使用**setProps()**
  - `更新组件`，我可以在节点上再次调用**React.render()**，也可以通过**setProps()**方法改变组件属性，触发组件重新渲染。

- 替换属性：`replaceProps`(object nextProps[, function callback])

  - **replaceProps()**方法与**setProps**类似，但它会删除原有 props。

- **强制更新**：`forceUpdate`([function callback])

  - callback，可选参数，回调函数。该函数会在组件**render()方法调用后调用**。
  - `forceUpdate()方法会使组件调用自身的render()方法重新渲染组件，组件的子组件也会调用自己的render()`。但是，组件重新渲染时，依然会读取this.props和this.state，如果状态没有改变，那么React只会更新DOM。
  - forceUpdate()方法适用于this.props和this.state之外的组件重绘（如：修改了this.state后），通过该方法通知React需要调用render()
  - **一般来说，应该尽量避免使用forceUpdate()**，而仅从this.props和this.state中读取状态并由React触发render()调用。

- **获取DOM节点**：DOMElement `findDOMNode`()

  - 返回值：DOM元素DOMElement
  - 如果组件已经挂载到DOM中，该方法返回对应的本地浏览器 DOM 元素。当**render**返回**null** 或 **false**时，**this.findDOMNode()**也会返回**null**。从DOM 中读取值的时候，该方法很有用，如：获取表单字段的值和做一些 DOM 操作。

- 判断组件挂载状态：bool `isMounted`()

  - 返回值：**true**或**false**，表示组件是否已挂载到DOM中
  - **isMounted()**方法用于判断组件是否已挂载到DOM中。可以使用该方法保证了**setState()**和**forceUpdate()**在异步场景下的调用不会出错。



## Ref

React 支持一种非常特殊的属性 **Ref** ，你可以用来绑定到 render() 输出的任何组件上。

这个特殊的属性允许你引用 render() 返回的相应的支撑实例（ backing instance ）。这样就可以确保在任何时间总是拿到正确的实例。

### 使用方法

绑定一个 ref 属性到 render 的返回值上：

```html
<input ref="myInput" />
```

在其它代码中，通过 this.refs 获取支撑实例:

```js
var input = this.refs.myInput;   // 获取到input实例
var inputValue = input.value;
var inputRect = input.getBoundingClientRect();
```

 