---
date: 2019-03-06
tags: 
- javascript
- umi
- react
categories: 
- 前端框架
- react
title: React (Umi+dva) 项目开发实践经验和踩坑指南
---
要时刻记住umi是约定高于配置的，因此在构建项目的时候，我们一般都会按特定的约定去构建，
> 一个良好的约定可以节省大量的时间

# 项目结构划分

很多童鞋在搭建项目的时候，往往忽略项目结构的划分，实际上合理的项目划分往往能够提供规范的项目搭建思路。 在 umi/dva 架构的项目中，我们推荐的目录基本结构为：（可以参考我们这边内部使用的[基于umi的cms的脚手架](https://g.hz.netease.com/cloudmusic-frontend/common-tech-frontend/react-cms/create-react-cms)，包含了通用配置，并可以自动生成页面，导航，面包屑等。也可以使用create-umi[脚手架](https://umijs.org/zh/guide/create-umi-app.html)创建）

![image-20190328144626576](http://img.hksite.cn/2019-03-28-064628.png)

## /mock

不用说，mock数据，但是在网易这边的项目可以使用nei去做mock

## /src

Umi默认有两种源文件查找方法：

- 使用src作为其源文件所在地。
- 或不要/src这一层，将/page等文件夹直接放在根目录。

> 坑1： 有些项目的目录约定不是/src，如何**修改默认的项目地址？**
>
> 答：根目录下的`.env`文件中可以通过设置`APP_ROOT=app/web`，将`src`改成`app/web`，通过这种方式，无需修改package.json中的umi命令（会自动去app/web中查找配置等）！！

## [/src]/config

该目录放配置相关的内容，umi的配置有两种方式：

- `/config`文件夹中放配置文件
- 根目录`.umirc.js`文件

我个人推荐使用**文件夹**放配置文件的方式，可以根据不同环境采用不同的配置：config.local.js / config.test.js / config.prop.js。

配置的相关内容，可以[参考文档](https://umijs.org/config/)，基本全了。除了umi-creator脚手架生成的那些，还可以考虑加上：

```js
 // 等同于webpack里的alias，你可以在模块中通过`@/...`去定位对应资源
 alias: {   
        '@': path.resolve('src')
 },
 // 如果需要定制antD的主题，请在这里配置就好
 theme: {
        'primary-color': '#ff4b44',
        'card-actions-background': '#f5f8fa'
 },
// 不喜欢css modules的可以考虑加上这个
disableCSSModules: true,
// 线上环境除非路由前端完全能控制，不然建议还是使用hash模式，不然路由会容易打不到前端页面
history: 'hash'
```

config.local.js

```js
// 本地调试应该都需要加上proxy代理吧？方便前后端联调
proxy {
    '/api/*': {
        	target: 'http://xxx.xxx.xxx.xxx',
            changeOrigin: true
    },'
}
```

config.prop.js

```js
// 下面两个配置按需配置
//  前者决定build后资源实际输出地址
//  后者决定html中对umi.js/umi.css资源的访问路径
outputPath: '../public',
publicPath: '/hawkeye/public/',

```

## [/src]/layout

layout布局是很重要的一块，/layout这个目录决定的是整体的结构，**它是对全局路由生效的！**，所以你的全局布局（header，footer，一级菜单）基本都是在/layout/index.js[x]中定义的。同时，一些全局的方法（**权限**判定，**全局消息**提示），全局的配置（LocaleProvider）也可以放在这里面写。

除了/layout之外，在每个`page`下，还可以定义该page的局部布局`_layout.js[x]`，这个布局只会影响**该路由下**所有页面的布局。（二级菜单之类的可以在这里面写）参考：[嵌套路由](https://umijs.org/zh/guide/router.html#%E5%B5%8C%E5%A5%97%E8%B7%AF%E7%94%B1)

## [/src]/page

> 注：这个目录负责**路由**，项目编译后，该目录下会自动生成`.umi/`，`.umi-production/`，所以请把这两个目录放到`.gitignore`中去。

page目录负责所有的页面信息，根据约定式路由的方式组织，其中如果包含目录，以目录名为该路由名，否则以`name`.jsx为该**子**路由名，详细看下面的举例。

**注：**index.js[x]为特殊页面，它代表该目录。

### name

对应`/name`的路由

### $name

对应动态路由`/:name`。例如你创建一个嵌套路由，目录`/aaa/$name`，那么url当为`/aaa/xxx`时候，都会打到`/aaa/$name/index.js[x]`下。

### name$

对应带`?查询参数`的动态路由

### 举例

```shell
+ pages/
  + $post/
    - index.js
    - comments.js
  + users/
    - $id.js
  	- index.js
  + search$/
  	- index.js
```

生成的路由：

```js
[
  { path: '/', component: './pages/index.js' },
  { path: '/:post/', component: './pages/$post/index.js' },
  { path: '/:post/comments', component: './pages/$post/comments.js' },
  { path: '/users/:id', component: './pages/users/$id.js' },
  { path: '/search?', component: './pages/search/index.js' }, 
]
```

>  坑1：约定式路由的默认根目录都是`/`，如果你的域名是子域名形式：`www.test.com/app`，就要注意了，此时最简单通过nginx，`proxy_pass http://…` 最后不要加`/`。然后页面所有的Link的地址加上前缀，也推荐配置hash模式（参考上面config），就可以不做任何修改。
>
> 不使用nginx的话，你需要修改不少东西：
>
> ```javascript
> // 决定react-router的base
> base: '/path/to/your/app/root',
> // 决定你静态资源的路径
> publicPath: '/path/to/your/app/root/public'，
> ```

## [/src]/**/models

> 注：如果你开启了dva（一般都会开启），/src下面所有的models目录都会自动自动注册。具体的注册规则可以看[文档](https://umijs.org/zh/guide/with-dva.html#model-%E6%B3%A8%E5%86%8C)
>
> ![image-20190304195331029](http://img.hksite.cn/2019-03-04-115331.png)

简而言之，所有models目录都负责数据层，会自动注册，**并且/models的层次结构直接决定访问权限**，最外层的/models是全局models，而page内的models只局限于该page使用。

# 整体数据流

![Umi](http://img.hksite.cn/2019-03-05-080853.png)

# model(dva)实践和坑

> 由于生成一个页面可能包含models，自己手动创建很麻烦，因此这里给大家提供了一个cli创建页面的工具：[umi-page-creator](https://www.npmjs.com/package/umi-page-creator)，可以一键「创建页面 和 model」的通用模板，方便快速生成页面，同时可以自动生成conncect, PropTypes验证的功能。

由于采用dva的写法，这里重点参考dva的文档。写过redux的应该不陌生，这里是将redux + redux-saga结合起来了，同时通过约定的方式，取消了action的type写法，**reducers和effects中的方法名直接就是其type字段**。

![image-20190305110737562](http://img.hksite.cn/2019-03-05-030737.png)

下面图基本说明了dva的各种方法的调用关系。

![dva](http://img.hksite.cn/2019-03-05-081132.png)

例子：

```javascript
import * as userFetch from '@/service/user'

export default {
    namespace: 'user',

    state: {
        init: true,
        login: false,
        username: '',
        role: '',
    },

    effects: {
        * getUser(_, { call, put }) {
            const response = yield call(userFetch.getUser);
            const { code, data } = response.data;
            if (code === 200) {
                if (typeof data === 'object') {
                    yield put({
                        type: 'setState',
                        payload: { ...data, login: true, init: false }
                    });
                }
            } else if (code === 302) {
                yield put({
                    type: 'setState',
                    payload: { login: false, init: false }
                });
            }
        }
    },

    reducers: {
        setState(state, { payload }) {
            return {
                ...state,
                ...payload
            };
        }
    },

    subscriptions: {
        setup(props) {
            const { dispatch, history } = props;
            return history.listen(() => {
                dispatch({ type: 'getUser' });
            });
        }
    }
};
```

## dva可能的坑

> 坑1: 在当前models文件中，无需namespace字段，可以直接put/dispatch({type: 'getUser'})。但是在此文件外，**任何地方需要触发该models中的action，需要namespace字段**，例：put/dispatch({type: 'users/getUser'})
>
> 坑2: 在effect和reducer中**不要操作view**，不要操作dom节点，因为此时很可能节点都未挂载上，连window都不一定存在。正确的做法应该是models中修改数据后，某个组件/页面监听该数据做出变化。如果是监听路由或者按键，事件，**请在subscriptions中操作**。
>
> 坑3: subscriptions文档少的可怜，会有很多坑，例如：**如何订阅其他事件，socket，屏幕事件……？**如何在subscriptions**获取当前state的值？**如何**取消订阅？**这些我专门会在下面的subscriptions讲解

### subscriptions

这个subscriptions的坑我准备单独拿出来说，因为真的是比较麻烦的一点，官方基本没给文档，就一句话略过，可参考的例子也比较少。

通过我这段时间的实践，发现虽然叫订阅，但是和传统的订阅的概念不太一样，它只不过是提供了一个加载入口，每次有变化的时候，**subscriptions中的所有函数都会顺序执行！**，可以让你在这个入口启动监听操作，并触发一些action（你可以把它理解为react的didMount生命周期，虽然不太恰当）。

正是因为subscriptions的存在，所以特别注意，你原本习惯的获取数据的位置可能要发生变化了。**原本放在didMount里的一些操作，可能需要移动到subscriptions里来了。**

例如`document.addEventListener`，`history.listen`操作都可以在这个地方触发。

```javascript
subscriptions: {
	// 监听路由变化
     setup({ history, dispatch }) {
      // 监听 history 变化，当进入 `/` 时触发 `load` action
      return history.listen(({ pathname }) => {
        if (pathname === '/') {
          dispatch({ type: 'load' });
          socket.io('')  // 初始化socket
        }
      });
    },
    // 监听socket变化
    socketEvent(dispatch) {  
      socket.on('connect', () => { dispatch({ type: 'connected' }) });
      socket.on('disconnect', () => { dispatch({ type: 'disconnect' }) });
      socket.on('message', msg => { dispatch({ type: 'message', payload:msg }) });
    },
    // 监听按键变化
    keyEvent(dispatch) {  
      key('⌘+up, ctrl+up', () => { dispatch({type:'add'}) });
    }
  }
```

其中`setup`,`keyEvent`，`socketEvent`这种取名都是随意的，没有任何讲究。只是传入参数中，一般就包含`history, dispatch`这两个，history可以用来监听路由变化，dispatch用来发送action。

**那么如何在subscriptions中拿到当前models的state呢？** 

答：很遗憾，[不行](https://github.com/dvajs/dva/issues/1600)，但是可以采用一个折衷方案。因为subsciptions可以触发effect，**而在effect中是可以使用select获取任何models里的state的，因此，你可以把一些操作放到effect中处理**（例如如果发现某些值存在，就放弃发送请求）。或者，你可以采用一些持久化的操作。把一些值持久化，或者做全局变量，放在models中（例如一个全局的socket对象），虽然不是很好，但是确实可以解决问题。

```javascript
let socket = null;

export default {
    namespace: 'socket',

    state: {
    }
    ...
}
```

**如何在subscriptions中解除订阅呢？**

答：将解除订阅的方法给**返回**就好了（这也是React Hook的做法）

```javascript
({ dispatch, history }) => unsubscribe
```

还记得setup返回一个`history.listen`吗？它的返回值就是一个unlisten函数。

```javascript
setup({ history, dispatch }) {
      return history.listen(({ pathname }) => {
       ……
      });
    },
```

# page的实践

尽量使用stateless functions方式组织Component（[原因](http://kms.netease.com/article/6228)），而且现在出现了React Hook（可以[参考文章](http://kms.netease.com/article/7362)），用function的方式写组件更加得心应手，而且方便逻辑功能的拆解。

Page页（即/src/page目录下的带有名字的文件或目录下的index文件）尽量只做container，即它通过连接组装各个组件，同时**负责组件间数据传递**。而页内组件（page目录下的components目录），做受控(展示)组件，只通过props接受参数，**不直接关联model**。

如果是非常通用的组件，那就放到`/src/components`目录下

```
+ pages/
  + users/
  	+ components/
  		+ form.jsx
    - index.jsx
    - index.less
```

当然，如果页面足够简单，一个page页足以，当然是全写在里面就好了

```
+ pages/
  + users/
    - index.jsx
    - index.less
```

这样的好处显而易见：

- 减少组件本身跟 model 的耦合，减少后期维护的烦恼。
- 让项目的数据处理更加集中
- 方便抽取公用组件

## 连接view和model

普通的组件用dva自带的connect进行连接就行了，

```javascript
import { connect } from 'dva';

...

function mapStateToProps(state, ownProps) {
  return {
    users: state.users,
  };
}
export default connect(mapStateToProps)(App);

```

用es6简写

```javascript
export default connect(({ users }) => ({
    users,
}))(App);
```

对于需要用到路由的组件，需要再包一层路由

```javascript
export default withRouter(
    connect((prop) => {
        const {
            routing, users
        } = prop;
        return {
            users,
            location: routing.location
        };
    })(App)
);
```

# Service设计

每个service只处理一类相关请求，每个接口以一个方法返回，方便models调用，当你的request方法封装的足够好的时候，service的代码量其实非常小。

```javascript
import { postRequest, query, BASE } from '@/utils/request';

const base = `${BASE}/msp/api/service`;

/**
 * 获取服务客户端列表
 * @param {Number} serviceId
 * @param {Object} page
 */
export function consumerList(methodId) {
    return query(`${base}/consumer/list/get`, {
        methodId
    });
}

/**
 * 服务检索接口
 * @param {String} keyword
 * @param {Object} page
 * @param {Number} domainId
 */
export function search(keyword, page, domainId = 0) {
    return postRequest(`${base}/search`, { keyword, page, domainId });
}

/**
 * 服务详情页接口
 * @param {Number} serviceId
 */
export function detail(serviceId) {
    return query(`${base}/detail`, { serviceId });
}

```



# 配合antD

如果你使用antD，可能会遇到一问题，这里有一些你可能会遇到的问题：

1. 修改配色，根据[官网的介绍](<https://ant.design/docs/react/customize-theme-cn#Ant-Design-%E7%9A%84%E6%A0%B7%E5%BC%8F%E5%8F%98%E9%87%8F>)，可以修改全局的样式变量

![image-20190404162520500](http://img.hksite.cn/2019-04-04-082530.png)

你只需要直接在umi的配置文件中加上`theme`字段就行了：

```
export default {
    plugins: [
        [
          ……
        ]
    ],
    theme: {
        'primary-color': '#61A6E8'
    },
};

```

2. 修改特定组件的样式

我们经常需要覆盖某个组件的样式，此时你有两个选择：在global.less中修改

```
body {
    margin: 0;
    .ant-breadcrumb {
        a {
            color: red !important;
        }
    }
}
```

或者在特定的页面index.less中修改，此时记得加上`:global`，这是cssModule里的一个配置，不然会通过cssModule编译成哈希串

```
.container {
    display: block;
    :global {
        .ant-breadcrumb {
            a {
                color: red;
            }
        }
    }
}
```



# 登录状态判断和跳转

一般来说，我们需要在用户进入页面的时候判断用户的登录情况，如果未登录跳到登录页面。（假设我们判断发起的请求是userInfo），我们有两种处理方法：

1. 在model里发起请求，然后page里根据请求结果进行操作（跳转或者通过Layout切换请求登录的页面）

```javascript
subscriptions: {
        setup(pay) {
            const { dispatch, history } = pay;
            return history.listen(() => {
                dispatch({ type: 'getUserInfo' });
            });
        }
    }
```

```javascript
 * getUserInfo(_, { call, put, select }) {
     const info = yield select(props => props.global.userInfo);
     // 防止重复请求
     if (!info || !info.email) {
       const response = yield call(serviceMap.user.userInfo);
       const { code, data } = response.data;
       if (code === 200) {
         yield put({
           type: 'setUserInfo',
           payload: { data }
         });
       } else if (code === 302) {
         yield put({
           type: 'global/setMessage',
           payload: { type: 'warn', msg: '请登录' }
         });
       }
     }
 }
```

但是这有一个问题，就是用户会先看到界面然后再跳转。当然如果你本来就是要在界面弹出一个用户提示可以采用这种模式。

2. umi提供了运行时配置（修改`/src/app.js`），其中有个render方法，这个方法会拦截最后的render过程，在这之前你可以进行登录状态的判断和跳转。

```javascript
export function render(oldRender) {
    userInfo().then((res) => {
        const { data } = res;
        const { code } = data;
        // 说明没登录，进行跳转
        if (code === 302) {
            console.log('not login');
            const redirect = window.location.href;
            window.location.href = `/api/skynet/login?redirect=${redirect}`;
        }
      	window.userInfo = data.data;
        oldRender();  // 否则继续渲染
    });
}
```
