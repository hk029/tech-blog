---
date: 2019-04-10
categories: 
- 后端技术
- Nodejs
tags:
- Nodejs
- npm
title: npm和package.json那些不为常人所知的小秘密
---

> 也许你已经跟成百上千个工程打过交道了，也许你已经看过无数的package.json了，但是，你是不是从来没有真正的理解过它，是不是还有什么小诀窍你是从来没有了解过的？作者最近就在做一个跟它有关的项目，因此把相关文档从头浏览了一遍，在下面列出一些笔者觉得有用的tips，也许对你有用也不一定哦~

本文会根据笔者的实践和文档阅读情况不定期更新，觉得可能有用可以收藏备用。
# 目录

- [目录](#%e7%9b%ae%e5%bd%95)
- [强大的命令功能](#%e5%bc%ba%e5%a4%a7%e7%9a%84%e5%91%bd%e4%bb%a4%e5%8a%9f%e8%83%bd)
  - [查询脚本](#%e6%9f%a5%e8%af%a2%e8%84%9a%e6%9c%ac)
  - [传递参数](#%e4%bc%a0%e9%80%92%e5%8f%82%e6%95%b0)
  - [生命周期钩子](#%e7%94%9f%e5%91%bd%e5%91%a8%e6%9c%9f%e9%92%a9%e5%ad%90)
  - [使用环境变量](#%e4%bd%bf%e7%94%a8%e7%8e%af%e5%a2%83%e5%8f%98%e9%87%8f)
    - [package.json](#packagejson)
    - [configuration](#configuration)
    - [一些常用的脚本配置](#%e4%b8%80%e4%ba%9b%e5%b8%b8%e7%94%a8%e7%9a%84%e8%84%9a%e6%9c%ac%e9%85%8d%e7%bd%ae)
    - [npx](#npx)
- [内置命令（脚本）](#%e5%86%85%e7%bd%ae%e5%91%bd%e4%bb%a4%e8%84%9a%e6%9c%ac)
  - [与script相关](#%e4%b8%8escript%e7%9b%b8%e5%85%b3)
  - [其他实用的命令](#%e5%85%b6%e4%bb%96%e5%ae%9e%e7%94%a8%e7%9a%84%e5%91%bd%e4%bb%a4)
- [乱七八糟的版本号](#%e4%b9%b1%e4%b8%83%e5%85%ab%e7%b3%9f%e7%9a%84%e7%89%88%e6%9c%ac%e5%8f%b7)
  - [版本匹配](#%e7%89%88%e6%9c%ac%e5%8c%b9%e9%85%8d)
  - [node-semver](#node-semver)
- [指定项目运行环境](#%e6%8c%87%e5%ae%9a%e9%a1%b9%e7%9b%ae%e8%bf%90%e8%a1%8c%e7%8e%af%e5%a2%83)
- [config](#config)
- [安装非NPM上发布的包](#%e5%ae%89%e8%a3%85%e9%9d%9enpm%e4%b8%8a%e5%8f%91%e5%b8%83%e7%9a%84%e5%8c%85)
- [dependencies &amp; devDependencies &amp; peerDependencies](#dependencies-amp-devdependencies-amp-peerdependencies)
- [更新包](#%e6%9b%b4%e6%96%b0%e5%8c%85)
    - [安装npm-check-updates](#%e5%ae%89%e8%a3%85npm-check-updates)
    - [ncu命令](#ncu%e5%91%bd%e4%bb%a4)
- [参考文档：](#%e5%8f%82%e8%80%83%e6%96%87%e6%a1%a3)

# 强大的命令功能

如果你没使用过script，那你可算是从来没手动编辑过package.json。script作为package.json里最强大的功能，它赋予你新增脚本的能力。特别是从[`npm@2.0.0`](https://blog.npmjs.org/post/98131109725/npm-2-0-0) 之后，你可以用自定义参数执行脚本。甚至有人宣言，[有了npm你可以停止使用Grunt和Gulp了](https://www.keithcirkel.co.uk/why-we-should-stop-using-grunt) 。

## 查询脚本

你可以通过输入`npm run`查看所有的命令列表（虽然你也可以直接查看package.json）

![image-20181023103906319](https://ws2.sinaimg.cn/large/006tNbRwly1fwhyhkq0k7j312q0vc7mt.jpg)

##自定义脚本

例如我们最常用的`npm start`，`npm run dev` ……，这些脚本都是可以用户自定义的，只用在`scripts`中写相应的**shell脚本**，可以快速的帮助我们`编写打包，启动脚本`。

```
"scripts": {
    "build": "webpack --config build.js",
    "start": "node index.js",
    "test": "tap test/*.js"
}
```

然后你可以通过`npm run ...`执行 ***注意，start命令可以不用run，后面会讲***

同时，执行脚本的时，npm会**临时**自动将目录的`node_modules/.bin`加入`PATH`变量。这意味着，可以使用node_modules中任何脚本，而无需添加`node_modules/.bin`前缀。比如，当前项目的依赖里面有 Mocha，只要直接写`mocha test`就可以了。

例如执行tap命令，你可以直接写：

```
"scripts": {"test": "tap test/\*.js"}
```
而不是
```
"scripts": {"test": "node_modules/.bin/tap test/\*.js"}  
```

## 传递参数

如果我们在执行`npm run xxx` 操作的时候想给里面的脚本传参数可以使用`—`，如下所示：

```javascript
 "scripts": {
    "test": "mocha test/",
    "test:xunit": "npm run test -- --reporter xunit"
  }
```

这种设置对于组合一些高级配置的命令是非常有用的。

```javascript
“scripts”: {
    "lint": "jshint **.js",
	"lint:checkstyle": "npm run lint -- --reporter checkstyle > checkstyle.xml"
}
```

## 生命周期钩子

这里借用很多框架的生命周期钩子的概念，其实npm也在不同的生命周期 [提供了一些钩子](https://docs.npmjs.com/misc/scripts)，可以方便你在项目运行的不同时间点进行一些脚本的编写。

它的钩子分为两类：`pre-` 和 `post-` ，前者是在脚本运行前，后者是在脚本运行后执行。**所有的命令脚本都可以使用钩子（包括自定义的脚本）**。

例如：运行npm run build，会按以下顺序执行：

`npm run prebuild` -->  `npm run build` -->  `npm run postbuild`

`pre`脚本和`post`脚本也是出口代码敏感(exit-code-sensitive) 的，这意味着如果您的`pre`脚本以非零出口代码退出，那么NPM将立即停止，并且不运行后续脚本。

通常你可以在`pre`脚本上执行一些准备工作，在`post`脚本上执行一些后续操作。

```
"clean": "rimraf ./dist && mkdir dist",
"prebuild": "npm run clean",
"build": "cross-env NODE_ENV=production webpack"
```

另外，还有很多额外的生命周期钩子，可以方便使用，例如[husky](https://www.npmjs.com/package/husky)  和 [pre-commit](https://www.npmjs.com/package/pre-commit) 包提供了有关git的commit的生命周期钩子。

## 使用环境变量

[根据官网的介绍](https://docs.npmjs.com/misc/scripts) ，在"scripts"中编写的脚本还可以方便使用一些内置变量，这些内置变量会在Node运行的时候放在`process.env`下，如果是shell脚本，就直接使用环境变量`$…`，  **这对你编写一些脚本工具特别有用。**

### package.json

package.json内的所有的配置项都可以通过`npm_package_`前缀拿到，例如

```bash
"show": "echo $npm_package_name && echo $npm_package_version"
```

如果是使用node：

```bash
"show": "node ./show.js"
```

```javascript
// show.js
const { log } = console;
log(process.env.npm_package_name);
log(process.env.npm_package_version);
```

![image-20181024114103465](https://ws1.sinaimg.cn/large/006tNbRwly1fwq16f98t0j312q0vctp7.jpg)

嵌套的属性也可以通过`_`代替`.`进行嵌套显示：`$npm_package_scripts_start`

### configuration

配置参数放在`npm_config_`前缀的环境中（你可以通过 `npm config set` 设置一些配置变量，下面介绍config的时候会介绍)

例如：

![image-20181030105015315](https://ws1.sinaimg.cn/large/006tNbRwly1fwq46c2wbbj312q0vctrr.jpg)

### 一些常用的脚本配置

这里引用阮老师的一些配置，可以看到配合一定的插件，npm可是实现一些很实用的功能

```

// 删除目录
"clean": "rimraf dist/*",

// 本地搭建一个 HTTP 服务
"serve": "http-server -p 9090 dist/",

// 打开浏览器
"open:dev": "opener http://localhost:9090",

// 实时刷新
 "livereload": "live-reload --port 9091 dist/",

// 构建 HTML 文件
"build:html": "jade index.jade > dist/index.html",

// 只要 CSS 文件有变动，就重新执行构建
"watch:css": "watch 'npm run build:css' assets/styles/",

// 只要 HTML 文件有变动，就重新执行构建
"watch:html": "watch 'npm run build:html' assets/html",

// 部署到 Amazon S3
"deploy:prod": "s3-cli sync ./dist/ s3://example-com/prod-site/",

// 构建 favicon
"build:favicon": "node scripts/favicon.js",
```

### npx

npm v5.2.0 之后还引入了npx，引入这个命令的目的是为了提升开发者使用包内提供的命令行工具的体验。（在node_modules中，所有可执行文件，也就是package中带bin的，都会放在node_modules/.bin中）

举例：使用`create-react-app`创建一个react项目。

老方法：

```
npm install -g create-react-app   // 实际就是把package.json中的bin命令连接到了/usr/local/bin中
create-react-app my-app  
```

npx方式：

```
npx create-react-app my-app // 执行本node_modules/.bin中的对应命令
```

这条命令会临时安装 `create-react-app` 包，命令完成后`create-react-app` 会删掉，不会出现在 global 中。下次再执行，还是会重新临时安装。

npx 会帮你执行依赖包里的二进制文件。

举例来说，之前我们可能会写这样的命令：

```
npm i -D webpack
./node_modules/.bin/webpack -v
```

如果你对 bash 比较熟，可能会写成这样：

```
npm i -D webpack
`npm bin`/webpack -v
```

有了 npx，你只需要这样：

```
npm i -D webpack
npx webpack -v
```

也就是说 npx 会自动查找当前依赖包中的可执行文件，如果找不到，就会去 PATH 里找。如果依然找不到，就会帮你安装！

npx 甚至支持运行远程仓库的可执行文件：

```
npx github:piuccio/cowsay hello
```

再比如 npx http-server 可以一句话帮你开启一个静态服务器！（第一次运行会稍微慢一些）

```
npx http-server
```

指定node版本来运行`npm scripts`：

```
npx -p node@8 npm run build
```

**主要特点：**

1、临时安装可执行依赖包，不用全局安装，不用担心长期的污染。
 2、可以执行依赖包中的命令，安装完成自动运行。
 3、自动加载node_modules中依赖包，不用指定$PATH。
 4、可以指定node版本、命令的版本，解决了不同项目使用不同版本的命令的问题。



#  内置命令（脚本）

npm自带了 [数十个内置命令](https://docs.npmjs.com/cli/)，这些命令都可以直接通过npm执行，除了`install`，还有很多实用的

![image-20181023105039767](https://ws1.sinaimg.cn/large/006tNbRwly1fwq16hirutj31kw0xa7du.jpg)

## 与script相关

其中有几条和我们刚才说过的script十分相关，我们可以通过在`scripts`中改写命令执行。

- start , `npm run start`的简写，如果不在script中配置start，那么`npm start`默认执行`node server.js`
- test ，`npm run test`的简写，执行自定义test脚本，没有默认行为。
- stop，`npm run stop`的简写
- restart，`npm run stop && npm run restart && npm run start`的简写

![image-20181023110627346](https://ws2.sinaimg.cn/large/006tNbRwly1fwq16gn1lrj312q0vc7k9.jpg)

## 其他实用的命令

我们经常用的是`install`，但是其实还有很多命令很实用：

- `npm -l`  列举所有npm自带的命令简介，然后通过`npm help`可以详细查看某个命令
- `npm search` 快速查询npm中的相关包（和我们去npm官网查是一样的）
- `npm root` 查看全局的`node_modules`目录
- [npm audit fix](https://docs.npmjs.com/cli/audit) 这个命令很实用，自动扫描您的项目漏洞，并自动安装任何兼容更新到脆弱的依赖

- `npm restart` 重新启动模块

- `npm prune` 移除当前不在package.json中但是存在node_modules中的依赖

- `npm repo <package>` 浏览器端打开项目地址（GitHub）， **省去打开浏览器查找的操作！**

- `npm docs <packge>` 查看项目文档，同上

- `npm home <package>` 在浏览器端查看项目（项目主页），同上

- `npm search <string>` 查找包含该字符串的依赖包

- `npm view <package> [field]\[--json]`列出依赖信息，包括历史版本，可以指定field来查看某个具体信息，比如（versions) 可以添加–json参数输出全部结果

  ![image-20181023111220672](https://ws1.sinaimg.cn/large/006tNbRwly1fwq46pdfkjj317s11y4qp.jpg)

# 乱七八糟的版本号

我相信只要打开package.json，第一眼就会被里面一堆乱七八糟的数字给晃得晕头转向，随便给你晃一眼：

```
  { "foo" : "1.0.0 - 2.9999.9999"
  , "bar" : ">=1.0.2 <2.1.2"
  , "baz" : ">1.0.2 <=2.3.4"
  , "boo" : "^2.0.1"
  , "qux" : "<1.0.0 || >=2.3.1 <2.4.5 || >=2.5.2 <3.0.0"
  , "lal" : "git://……"
  , "asd" : "http://asdf.com/asdf.tar.gz"
  , "til" : "~1.2"
  , "abc" : "haha/abc"
  , "elf" : "~1.2.3"
  , "two" : "2.x"
  , "thr" : "3.3.x"
  ……
  }
```

光是看到数字就已经晕了，还有`~`，`<>`，`^`这么多符号。npm官网推荐使用语义化的版本好，一般按如下规则：

首先版本按照**大版本.次要版本.小版本**，一般来说初始版本是`1.0.0`：

- **小版本：**修改bug或其他小的改动
- **次要版本：**增加了新的特性不改变已有特性
- **大版本：**大的变动，可能影响了向后的兼容性

## 版本匹配

> - **指定版本**：比如`1.2.2`，安装时只安装指定版本。
> - **~次要版本不变**：比如`~1.2.2`，表示安装1.2.x的最新版本（不低于1.2.2），但是不安装1.3.x，也就是说安装时不改变大版本号和次要版本号。
> - **^大版本不变**：比如`ˆ1.2.2`，表示安装1.x.x的最新版本（不低于1.2.2），但是不安装2.x.x，也就是说安装时不改变大版本号。**需要注意的是，如果大版本号为0，则插入号的行为与波浪号相同**，这是因为此时处于开发阶段，即使是次要版本号变动，也可能带来程序的不兼容。
> - **latest**：安装最新版本。

## node-semver

如果你需要在程序中做版本匹配，手写是不是很麻烦（我之前还真的自己用正则写了一个，超麻烦）其实npm提供了一个现成的匹配版本号的工具：
https://github.com/npm/node-semver#functions

# 指定项目运行环境

很多时候，我们的项目只能在特定的环境下执行，在其他环境下可能会报错，因此我们需要在package.json中限制用户执行项目的环境：

`engines`指明了该项目所需要的node.js版本

```
"engines": {"node" : ">= 4.0.0","npm": ">= 3.0.0"}
```

`os`指定了用户执行的操作系统：

```
"os": [ "darwin", "linux", "!win32" ],
```

`cpu`可以指定包运行的cpu架构

```
  "cpu": [ "x64", "!arm" ]
```

# config

之前我们说过npm有个配置文件，里面的变量可以通过`$npm_config_`拿到，而这个配置文件通常在：

- 项目config file (/path/to/my/project/.npmrc) ： 通常要自己创建
- 用户config file (~/.npmrc)
- 全局config file ($PREFIX/etc/npmrc)
- npm内置config file (/path/to/npm/npmrc)

通过以下命令可以查看config信息：

```
npm config ls/list 
```

![123](https://ws2.sinaimg.cn/large/006tNbRwly1fwq46suydnj31ia0wtwoe.jpg)

npm有很多默认配置，可以通过以下命令查看，可以去官网查看这些命令的[详细信息](https://docs.npmjs.com/misc/config#default-configs)

```
npm config ls -l
```

![image-20181030115814087](https://ws3.sinaimg.cn/large/006tNbRwly1fwq46vony0j312q0vc7mc.jpg)

通过以下命令可以修改配置

```
npm config set key value
npm set key value

npm config get key
npm get key
```

当然，也可以通过`$npm_config_key = `来修改，或者在node中通过``process.env.npm_config_key = `来修改，

数组值是通过在键名后面添加“[]”来指定的。例如：
```javascript
key[] = "first value"
key[] = "second value"
```


# 安装非NPM上发布的包

通常，我们安装的包都是在npm官网上，通过版本标明。但是，如果我想使用没上传到npm上的包怎么办？其实你可以直接加网址或git地址。[官网明确](https://docs.npmjs.com/getting-started/packages#what-is-a-package-)了以下类型的包都是可以的：

a) 包含一个由package.json文件描述的程序的文件夹。

b) 包含（a）的gzipped tarball 。

c）解析为（b）的URL。

d） **\<name>@\<version>** : 在registry上发布的(c) 

e）**\<name>@\<tag>** : 能指向（d）。

f）**\<name>**：  具有latest标签，且满足（e）。

g）**git url**，当clone时，得到（a）。

只要满足以上条件，你的包不用发布到npm上都能使用。其中，git url可以是以下形式：

```
git://github.com/user/project.git#commit-ish
git+ssh://user@hostname:project.git#commit-ish
git+http://user@hostname/project/blah.git#commit-ish
git+https://user@hostname/project/blah.git#commit-ish
```

其中，**commit-ish** 可以是任意的**tag**，**branch**，**sha**。



# dependencies & devDependencies & peerDependencies

众所周知，package.json里有一堆dpendencies字段，他们像亲兄弟一样，总是结伴同行。很多人容易弄混他们到底是什么关系。

由于这里面内容挺多的，会整理在另一篇文章中，后续这里会放上链接



# 更新包

我们知道npm自带的npm update可以根据pacaage.json的版本号更新包，**但是你需要手动的更新版本号**，因此出现了升级插件`npm-check-updates`，可以自动搜索当前包的更新情况，并且修改pacage.json

### 安装npm-check-updates

```
$ npm install -g npm-check-updates
```

### ncu命令

ncu是npm-check-updates的缩写命令

```
$ ncu  -v  #查询版本号
```

```
$ ncu    #直接输入ncu可以查看所有需要更新的包
```

![image-20181030113416839](https://ws2.sinaimg.cn/large/006tNbRwly1fwq46yzqkbj31ia11y7wh.jpg)

```
$ ncu -u  # 更新所有的包，并修改package.json文件
```

![image-20181030113457081](https://ws4.sinaimg.cn/large/006tNbRwly1fwq473bfcnj31ia11y7wh.jpg)

```
$ ncu -f regex # 只匹配特定的正则格式的包
```

```
$ ncu -g # 更新全局包
```

![image-20181030113548646](https://ws1.sinaimg.cn/large/006tNbRwly1fwq476kn05j31ia11y4qp.jpg)



# 参考文档：

https://docs.npmjs.com/misc/scripts

https://docs.npmjs.com/cli/run-script

https://docs.npmjs.com/files/package.json

https://stackoverflow.com/questions/18875674/whats-the-difference-between-dependencies-devdependencies-and-peerdependencies

http://javascript.ruanyifeng.com/nodejs/packagejson.html#toc1

https://www.keithcirkel.co.uk/how-to-use-npm-as-a-build-tool/