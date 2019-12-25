---
tags: 
- javascript
- mongodb
- 数据库
date: 2018-10-31
categories: 
- 前端技术
- javascript
title: 细嚼慢咽 Mongoose5
---
# 细嚼慢咽 Mongoose 5 之 What？Why？How？

![20180918153726921916050.png](https://img.hksite.cn/20180918153726921916050.png?imageMogr2/thumbnail/750x)

# 前言

由于Mongoose一直没有中文文档，加上现在市面上充斥着太多“快速上手”，很多中文文档都只盲目介绍了Mongoose的API用法（但是现在升级后，很多API发生了变化），没有做详细介绍，导致很多人用了半天的mongoose，还是对其一知半解（比如我！），因此写了这篇文章，从以下三个角度聊聊Mongoose。

- **是什么（what?）**
- **为什么？（why?）**
- **怎么用（how?）**
  - **快速上手**
  -  **再深一点（这才是细嚼慢咽的关键哦）**

本文阅读可快，可慢全凭喜好。但本着贪多嚼不烂的想法，还是希望客观慢慢品尝。

# 是什么（what）？

首先，必须要知道Mongoose是什么

> Mongoose（狐獴）就是图上这个小可爱。（学名：*Suricata suricatta）*，头尾长42-60厘米，是一种小型的[哺乳动物](https://baike.baidu.com/item/%E5%93%BA%E4%B9%B3%E5%8A%A8%E7%89%A9/62197)……

好像有点跑题。当然，今天介绍的Mongoose不是真的小动物，而是一款在Node.js环境下对[MongoDB API](https://www.mongodb.com/) 进行便捷操作的**对象模型工具**（所以理论上，我默认你是需要大概懂MongoDB数据库才会来用这个的）。

要特别强调的是，我这里说它是对象模型工具，说明Mongoose的操作是**以对象为单位的**。同时，由于Mongoose升级后（**差不多**）全面支持Promise，可以很方便的对接`async/await`语法。因此接下来的我的代码都会默认使用`Promise`形式。

# 为什么（Why）？

我们知道，mongoDB官网提供了一个Node.js环境下的MongoAPI，那为什么我还需要使用Mongoose呢？当然是因为它提供了一些官方不能提供的功能，如它官网介绍的：

> Let's face it, **writing MongoDB validation, casting and business logic boilerplate is a drag**.

可以看出，Mongoose主要是减轻我们在写数据验证，业务逻辑模板的负担。同时，由于Mongoose一直强调它是一个mongoDB对象建模工具，因此在这里就必须要提出Mongoose的核心，也是mongoose困扰人已久的一个东西——**模式**和**模型**。

##  模式？模型？

对于**Mongoose**的新人来说，最头疼的就是它里面有关模式（`Schema`），模型（`Model`）的概念。**Mongoose**的一切都始于一个模式。这点和MongoDB基于Collection的思路可能不太一致，因为本身MongoDB是**[无模式](https://www.mongodb.com/blog/post/why-schemaless)**的，也正是因为它的无模型，导致其使用起来极具灵活性。

那Mongoose为什么要定义一个模型？[Why Define a Mongoose Schema?](http://billpatrianakos.me/blog/2016/07/07/mongodb-and-mongoose-why-define-a-schema/) 给出了解释：

> have you ever wondered how to pull these random documents out and figure out which properties were saved to document?  **The truth is that for 90% of your web applications you’ll be using model classes and you’ll have a standard set of fields you’ll be expecting to be saved to your database**

模式有模式的好处，生活中绝大部分的数据是遵循一定的模式的，模式能让我们**规范数据的表现形式，不至于太随心所欲**。同时模式给我们提供了很多**数据验证**的便利（这就像我们讨论JavaScript弱类型和TypeScript强类型是一样一样的）。

**同时，由于Mongoose使用的依然MongoDB而非关系型数据库，因此它没有完全失去其天生的灵活性，它依然开放了让你可以定义任意字段的能力。**

```javascript
// Defining a schema on a Mongoose model
// that allows storage of arbitrary fields
// Mongoose.schema(schemaObject, options)
var User = Mongoose.schema({
  username: String,
  password: String
}, {
  strict: false
});
```

注意到上面代码的第二个字段，你把`strict`设置为`false`，你就能在这个模型中加入任意字段了（**但是还是不建议这样做，因为这意味着你需要自己去做数据的验证**）。

如果你实在不习惯使用 **模式**，可以使用MongoDB官方的API。

> 与其说Mongoose定义模式是多此一举的，不如说，它在灵活性的基础上，提供了更安全的模型化操作对象的方式。

我个人感觉：使用Mongoose你更有操作对象的感觉。

## 模式 VS 模型

我们已经知道了模式是有其优势的，**那为什么我还需要模型？这两个有什么区别？**我一直在想如何去比喻模式和模型的关系。

官方的定义：

> Each schema defines the shape of the documents within that collection.

- **模式**

模式它是一个 **抽象的概念**。其实可以把**模式**考虑成**设计规范** ，定义了设计图纸应该如何画，默认单位是多少，如果没有该规范，那每个人画的设计图纸就是各式各样，没法真正投入建造。

![2018091815372617383884.png](https://img.hksite.cn/2018091815372617383884.png?imageMogr2/thumbnail/750x)

- **模型**

模型相对于模式是具体的概念，可以把**模型**考虑成**设计图纸** ：**按照设计规范**画出一个图纸，任何人根据该规范就可以把房子造出来。

![20180918153726160689595.png](https://img.hksite.cn/20180918153726160689595.png?imageMogr2/thumbnail/750x)

- **实例**

而模型的实例才是真正的我们住的房子，在MongoDB里也就是document

![20180918153726193616690.png](https://img.hksite.cn/20180918153726193616690.png?imageMogr2/thumbnail/750x)

## 多此一举？

你可能会说，何必多此一举，把模式和模型合起来不就好了嘛？很多人喜欢这样写：

```javascript
const studentModel = mongoose.model('Student', new mongoose.Schema({ name: String, sex: String});
```

这样写也可以，但是这个前提是你确定你的模式只在这一个模型中使用。之所以有个模式这个抽象概念，就像设计规范， **它从来不是说只能在一张图纸中使用**，你可以利用这个规范绘制出很多符合规范的图纸。

有时候你可能会有这样的需求，基本一样的数据结构，但是我需要建多个不同Collection，或者稍微修改一下模式，新建一个新的Collection，这时候把模式独立出来，你就会发现省事很多了。

```javascript
const personSchema = new mongoose.Schema({ name: String, sex: String});
const studentModel = mongoose.model('Student', personSchema);
const studentModel = mongoose.model('OtherStudent', personSchema);

const teacherSchema = personSchema.clone();
teacherSchema.add({ teach: String});
const teacherModel = mongoose.model('Teacher', teacherSchema);
```

## 举例

再来举一个例子：假设，我们建立了一个宠物猫咪乐园，我们需要去招集一批宠物喵成为我们的会员。但是呢，我们的宣传员他没见过喵（多么神奇的生物）。于是，我们绞尽脑汁，合计出一个规范：所有的宠物猫应该满足三个属性`name`，`type`， `meow`。也就是说，宠物喵必须要有名字，必须有一个品种，同时它必须要会“喵喵”叫！

```javascript
const catSchema = new mongoose.Schema({ name: String, type: String，meow: Boolean});
```

好了，现在我们把规范告诉他，但是他说他记性不好。怕忘记，因此我们制定了一个叫《宠物猫咪指南1.0》的手册

```javascript
const CatGuide = mongoose.model('Animal', catSchema);
```

好了，宣传员开心的出去了，开始到处撩猫去了：

```javascript
const kitty1 = new Cat({ name: 'mimi', type: 'American Shorthair', meow: true });
const kitty2 = new Cat({ name: 'kaddy', type: 'American Shorthair', meow: true });
const doggy = new Cat({ name: 'doggy', type: 'Dog!!', meow: false });
……
```

（但是这个不靠谱的，拿着手册也能找错，找了一只穿猫咪衣服的狗！！？）

![20180918153726356643437.png](https://img.hksite.cn/20180918153726356643437.png?imageMogr2/thumbnail/750x)

因此，我们只把那两只猫保存到数据库里。

```
kitty1.save().then(() => console.log('meow'));
kitty2.save().then(() => console.log('meow'));
```

知道了他们之间的关系，剩下的就好办了。就是编程了，编程能解决的事情都是简单的事情。

# 怎么用（How）？

## 快速开始

mongoose的使用流程很简单：

```
连接 ——> 定义模式 ——> 生成Model ——> 生成Model实例
```

其中，`连接`，`Model`，`实例`是实际跟数据库打交道的，他们之间的关系如下：

![2018092715380170324788.png](https://img.hksite.cn/2018092715380170324788.png?imageMogr2/thumbnail/750x)

通过一个很简单的例子，我们就可以上手操作MongoDB，然后配合[官方API](https://mongoosejs.com/docs/api.html)，熟练的人可以直接跳过了，但是如果你想了解更多细节，可以继续看下去，看看每个操作具体还可以怎么配置。

```javascript
// step 1 引入mongoose
const mongoose = require('mongoose');

// setp2 建立连接
mongoose.connect('mongodb://localhost/test');

// step3 建立模式
const catSchema = new mongoose.Schema({ name: String, type: String });

// step4 生成模型
const Cat = mongoose.model('Cat', catSchema);

// step5.1 模型直接操作document
Cat.create({ name: 'mimi', type: 'American Shorthair' }).then(...)

// or
                                                              
// step5.2 生成实例，操作document
const kitty = new Cat({ name: 'mimi', type: 'American Shorthair' });
kitty.save().then(() => console.log('meow'));
```

## 连接

我们都知道，使用数据库的第一步是需要连上数据库，Mongoose提供了一个很简单的方式连接：`connect`。

```javascript
mongoose.connect(url, [options]);
```

> 注意，现在使用url需要加上 { useNewUrlParser: true }选项，否则可能会报错。 
>
> mongoose.connect(url, { useNewUrlParser: true });

其中url参数的完整形式，跟mongoDB[连接字符串](https://docs.mongodb.com/manual/reference/connection-string/)的一致：

`mongodb://username:password@host:port/database?options...`

默认情况下，用户名和密码可以省略：

```
mongoose.connect('mongodb://localhost/db1');
```

如果还需要传递用户名、密码，则可以使用如下方式

```
mongoose.connect('mongodb://username:password@host:port/database?options...');
```

connect()方法还接受一个选项对象options，该对象将传递给底层驱动程序。这里所包含的**所有选项优先于连接字符串中传递的选项**：

- `bufferCommands` : 这个选项会关闭Mongoose的[缓存机制](http://mongoosejs.com/docs/faq.html#callback_never_executes) ，等下会说。
- `user`/`pass` ：用户名和密码The username and password for authentication. These options are mongoose-specific, they are equivalent to the MongoDB driver's `auth.user` and `auth.password` options.
- `autoIndex` ：通常MongoDB会自动生成索引，但是有时候如果你不需要，可以把这个选项设置为false
- `dbName` ：制定数据库名，一般是没办法在连接字符串中指定的情形（ `mongodb+srv` 语法连接到 [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)）还

还有一些选项是用来配置Mongoose的，例如，我之前说的`useNewUrlParser ` ，还有` pooleSize`， `bufferMaxEntries`，`connectTimeoutMS`， `socketTimeoutMS`…… 这些都是比较高级的配置，一般人用不到。如果真的需要用到，可以翻[官方文档](https://mongoosejs.com/docs/connections.html#options)

## 模式

我们知道，模式其的是规范的作用，因此定义模式主要是定义我们数据的表现形式以及做一些格式约定，方便自动校验。例如，我创建了一个学生的模式，它规定了name和age字段的类型：

```javascript
const studentSchema = new mongoose.Schema({
  name: String,
  age: Number,
});
```

### 基本类型

模式有以下10种基本类型，如果只使用基本类型，就输入以下字段就行

- `String` | `Number` | `Date` | Buffer | `Boolean` | `Schema.Types.Mixed` | `Schema.Types.ObjectId` | `Array` | `Schema.Types.Decimal128` | `Map`（新加入的ES6的Map）

> 注意：
>
> 1. 如果是Array，可以使用[]代替，你可以可以规定Array内部数据类型，例如数字数组：[Number]
>
> 2. 很多人困惑为什么没有Object类型，如果你想定义对象，使用`Schema.Types.Mixed`就行了。Mixed类型支持多种类型混合，比如数组套对象，对象套数组……

### 额外约束

但是，只规定了基本类型还不够，我想做一些额外的约束，比如**姓名长度**，**年龄大小**。因此每种类型还有很多配置项，如果要使用额外的约束，需要使用Object形式，同时，类型通过`type`引入：

```javascript
const studentSchema = new mongoose.Schema({
  name: { type: String, minlength: 2 },
  age: { type: Number, min: 12, max: 16},
});
```

这里只介绍一些比较常用的，更多使用说明参考[官方手册](https://mongoosejs.com/docs/schematypes.html)：

**所有类型可用：**

- `required`: boolean or function, 很好理解，如果为真就表示该字段必须要存在
- `default`: Any or function, 设置默认值
- `validate`: function, adds a [validator function](https://mongoosejs.com/docs/validation.html#built-in-validators) for this property
- `get`/`set` : function,  使用[`Object.defineProperty()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty).
- `alias`: string, mongoose >= 4.10.0 only. Defines a [virtual](https://mongoosejs.com/docs/guide.html#virtuals) with the given name that gets/sets this path.

**字符串 String：** 

- `lowercase`/`upppercase`: boolean, 是否做大小写处理 

- `trim`: boolean, 是否去除首尾空格
- `match`: RegExp, 只有满足特定正则的值才能被接受
- `enum`: Array, 只有数组内的值才被接受
- `minlength`/ `maxLength`: Number, 最小、大长度

**数字 Number / 日期 Date**

- `min` / `max`: Number / Date (根据类型），必须满足的最大值/最小值

> 注意： Date使用的是内置的Date，但是你使用setMonth等内置的方法，不会被Mongoose不会识别到，因此使用`doc.save()`不会有效果。你必须设置`doc.markModified(pathToYourDate)`。

```javascript
const Assignment = mongoose.model('Assignment', { name: String, dueDate: Date });
const a = new Assignment({ name: 'test', dueDate: Date.now() });
a.save().then(callback);
Assignment.findOne({ name: 'test' }, 'name dueDate', (err, doc) => {
  doc.dueDate.setMonth(3);
  doc.save(callback); // THIS DOES NOT SAVE YOUR CHANGE
  doc.markModified('dueDate');
  doc.save(callback); // works
});
```

## 模型

有了模式之后，就可以生成模型了，生成模型很简单，就使用`model`函数就行了。

```javascript
// step3 建立模式
const catSchema = new mongoose.Schema({ name: String, type: String });

// step4 生成模型
const Cat = mongoose.model('Cat', catSchema);
```

> 注意，虽然所有的例子都是mongoose.model，但是实际上model是Connect原型的方法：[Connection.prototype.model()](https://mongoosejs.com/docs/api.html#connection_Connection-model)，即一个**连接实例才有model方法**。（具体的可以看后面的 **再深一点**章节）

其中，model第一个参数就是collection名称（Mongoose会自动 **复数化**，想修改名称也请看后面章节），同时model会把Schema里的内容**复制一份**

> 官方原文是说复制一份 
>
> *The `.model()` function makes a copy of `schema`. Make sure that you've added everything you want to `schema` before calling `.model()`!*
>
> 但是我实际测试，感觉还是引用，在调用model方法之后，通过`add（）`, `remove()`方法修改Schema依然会影响之前创建的model。

```javascript
const catSchema = new mongoose.Schema({ name: String, type: String });
const Cat = mongoose.model('Cat', catSchema);
catSchema.remove('type');   // 在创建model后修改Schema会影响之前的model
const kitty = new Cat({ name: 'mimi2', type: 'American Shorthair' });
kitty.save().then(() => console.log('meow'));  // type字段不会存储到数据库
```

## 实例

根据之前的图可以知道，model的实例就是文档，有一些比较方便的操作方法：

- get(path,opt) : 获取文档的某个字段，这个函数比较好的是做数据处理，第个参数可以将该字段转换成其他类型：«Schema|String|Number|Buffer|*»

  ```javascript
  doc.get('age') // 47
  
  // dynamic casting to a string
  doc.get('age', String) // "47"
  ```

- save()：顾名思义保存文档到数据库

- 

```javascript
var Tank = mongoose.model('Tank', yourSchema);

var small = new Tank({ size: 'small' });
small.save(function (err) {
  if (err) return handleError(err);
  // saved!
});
```



## CRUD操作

> 由于对文档的操作通常是在"集合"这个维度，因此大部分的操作都是在**model**上完成的，除了一些单文档的操作可以在文档这个维度，通过**实例**完成

### 新增文档[s]

文档保存有三种方法，除了之前介绍的save方法，还可以使用`模型`的下面两种方法。

- model.`create`(object) : **其实就是new model.save()的简写**
- model.`insertMany`([object]) : 可以批量增加，**从内存写到数据库比较实用**

区别就像实例的`save`是猫主人自己跑过来报名。`create`，`insertMany`就是我们工作人员替用户直接录入。

```javascript
const catSchema = new mongoose.Schema({ name: String, type: String });

const Cat = mongoose.model('Cat', catSchema);
Cat.create({ name: 'mimi', type: 'American Shorthair' }).then(...)  // 插入单条文档
Cat.insertMany([{ name: 'mimi', type: 'American Shorthair' }...]).then(...)  // 插入多条文档
```

### 查找文档

> Mongoose的查删改的使用方法基本大同小异，这里重点介绍一下查询，剩下的更新和删除主要介绍方法，不会具体介绍怎么使用

跟查询有关的方法有…一堆：

- [`Model.find()`](https://mongoosejs.com/docs/api.html#model_Model.find)
- [`Model.findById()`](https://mongoosejs.com/docs/api.html#model_Model.findById)
- [`Model.findByIdAndDelete()`](https://mongoosejs.com/docs/api.html#model_Model.findByIdAndDelete)
- [`Model.findByIdAndRemove()`](https://mongoosejs.com/docs/api.html#model_Model.findByIdAndRemove)
- [`Model.findByIdAndUpdate()`](https://mongoosejs.com/docs/api.html#model_Model.findByIdAndUpdate)
- [`Model.findOne()`](https://mongoosejs.com/docs/api.html#model_Model.findOne)
- [`Model.findOneAndDelete()`](https://mongoosejs.com/docs/api.html#model_Model.findOneAndDelete)
- [`Model.findOneAndRemove()`](https://mongoosejs.com/docs/api.html#model_Model.findOneAndRemove)
- [`Model.findOneAndUpdate()`](https://mongoosejs.com/docs/api.html#model_Model.findOneAndUpdate)

大部分都大同小意，ByID那一批我排除了，实际用途不大，很多时候我们都不知道文档的_id，然后findOne跟find的差别就是，一个是查询所有满足条件的文档，一个是只返回第一个，因此我这里只讲一个find的用法：

```javascript
Modle.find.(conditions, [projection], [options], [callback] )
```

- conditions «Object» : 查询条件（完全跟mongoDB手册一致）
- [projection] «Object|String»  ： 选取字段，和下面的select方法完全一致（我推荐使用链式调用） 
- [options] «Object» ： 查询配置（在**再深一点**章节再介绍）
- [callback] «Function»  ： 推荐使用Promise写发，不写callback

> 一般来说，查找MongoDB中的某个文档，是需要一个查询条件（condition）的，这个查询条件可以简单可以复杂，由于完全跟[mongoDB手册](https://docs.mongodb.com/manual/tutorial/query-documents/)一致，本文毕竟不是《MongoDB入门》这里就不做过多叙述，如果后续有时间会把整理的MongoDB手册发上来， **下面的例子也会说明一些**。

这里重点说一下Mongoose自身提供的一些比较使用的方法，这里举官方一个例子来说明一下：

通常一个**标准的MongoDB的查询**大概是这样（其中Person是一个model），Mongoose完全支持这种查询方式：

```javascript
Person.
  find({    // 这里写查询条件
    occupation: /host/,   // 查询条件可以用正则
    'name.last': 'Ghost',   //  限定name.last 
    age: { $gt: 17, $lt: 66 },   // 限定年龄范围
    likes: { $in: ['vaporizing', 'talking'] }   // 限定喜好必须要在这个数组中
  }).
  limit(10).    // 限定取出的文档条数
  sort({ occupation: -1 }).  // 针对某个字段排序，1表示正序，-1表示逆序
  select({ name: 1, occupation: 1 }).  // 选择取出的字段，1表示取出，0表示不取出，如果不需要id，需要显式写{_id : 0}
  exec(callback);  // 这里使用then也行，完全支持Promise，但是查询不是Promise，这个要注意，后面会说
```

但是Mongoose把 **常用的方法都提取出来了**，例如：where，gt，lt，in……（[完整API文档](https://mongoosejs.com/docs/api.html#Query)），因此更推荐使用下面这种方式：

```javascript
// Using query builder
Person.
  find({ occupation: /host/ }).
  where('name.last').equals('Ghost').
  where('age').gt(17).lt(66).
  where('likes').in(['vaporizing', 'talking']).
  limit(10).
  sort('-occupation').
  select('name occupation').
  exec(callback);
```

> 要强调一下，查询支持promise一些语法，但是它不是promise。这个坑必须要记得！！ **千万不要将callback形式和promise混用！**
>
> 例如，下面的query可以执行三次！并不会因为一次then或者callback结束。 **说明then只是一个执行方法**，查询并不是真正的Promise。
>
> ```javascript
> const q = MyModel.updateMany({}, { isDeleted: true }, function() {
>   console.log('Update 1');
> });
> q.then(() => console.log('Update 2'));
> q.then(() => console.log('Update 3'));
> ```

如果要统计查询的文档数据，可以使用**countDocuments()**，用法和MongoDB的count()一致

```javascript
Person.find(...)
.count()
.then() // 这里获得的是一个数字
```

>  注意： Mongoose的`count()`已经被废弃，需要统计个数得用countDocuments

### 更新文档

更新文档的方法有下面三个，condition的使用方法和find的方法一**完全一致**（包括where那些查询方法都可以使用），只是要注意， **update只是更新字段（符合Schema），但是replace是完全的用新doc来替换。** 

- [`Model.replaceOne(conditions, doc, [options], [callback] )`](https://mongoosejs.com/docs/api.html#model_Model.replaceOne)
- [`Model.updateMany(conditions, doc, [options], [callback] )`](https://mongoosejs.com/docs/api.html#model_Model.updateMany)
- [`Model.updateOne(conditions, doc, [options], [callback] )`](https://mongoosejs.com/docs/api.html#model_Model.updateOne)

### 删除文档

删除文档主要是两个方法，使用方法完全和查询一致。

- [`Model.deleteMany(condition)`](https://mongoosejs.com/docs/api.html#model_Model.deleteMany)
- [`Model.deleteOne(condition)`](https://mongoosejs.com/docs/api.html#model_Model.deleteOne)



# 再深一点

## 细品连接

### 缓存机制

根据上面的例子，你会发现一个奇怪的现象， **我们并没有判断连接是否成功就进行了操作？**这是因为Mongoose内部缓存了函数调用。

> Mongoose lets you start using your models immediately, without waiting for mongoose to establish a connection to MongoDB

这很cool，很方便，我们不用去关心连接成功再去增加文档，否则你必须要这样：

```javascript
mongoose
    .connect('mongodb://localhost/test')
    .then(() => {
        const catSchema = new mongoose.Schema({ name: String, type: String });
        const Cat = mongoose.model('Cat', catSchema);
        const kitty = new Cat({ name: 'mimi', type: 'American Shorthair' });
        kitty.save().then(() => console.log('meow'));
    })
    .catch(err => console.log(err));
```

但是这样会带来一个问题就是你如果不建立连接，进行操作也不会收到任何提示。

```javascript
var MyModel = mongoose.model('Test', new Schema({ name: String }));
// Will just hang until mongoose successfully connects
MyModel.findOne(function(error, result) { /* ... */ });

setTimeout(function() {
  mongoose.connect('mongodb://localhost:27017/myapp');
}, 60000);
```

所以一定要养成良好的喜欢，在做**查询，删除**等操作的时候，判断一下是否已经连接。判断连接可以使用下面提的 connection对象的**连接状态码**

### connection对象

说实话，刚开始使用Mongoose的时候我总是充满疑惑，一开始我认为Mongoose应该是一个类，它代表一次连接，通过new的方式可以创建一个数据库的连接。但是Mongoose不是，它直接通过`connect`方法就创建了一个连接，就能直接操作数据库上的集合，那说明这个Mongoose只是一个实例（的确也是）。那如果Mongoose只是一个实例，它就等于那一个连接吗？那为什么mongoose上面没有操作数据库的方法？而且我要如何创建多个连接呢？一个个问题在脑海中回荡，脑子里早已一团浆糊，于是，还是回到了官方文档找答案。

其实，每次执行`mongoose.connect`，**实际就是创建了一个Connection对象**（这个对象可以通过`mongoose.connection`获得）这个对象才是真正对应的一个**数据库**的连接。因此，所有的数据库操作其实都是在**connect对象**中，例如：[createCollection()](https://mongoosejs.com/docs/api.html#connection_Connection-createCollection)， [dropCollection()](https://mongoosejs.com/docs/api.html#connection_Connection-dropCollection), [dropDatabase()](https://mongoosejs.com/docs/api.html#connection_Connection-dropDatabase) ……

这个connection对象同时可以监听几个常用事件:`connected`，`disconnected`，`error`。

```javascript
const connection = mongoose.connection;
connection.on('connected', () => {
  console.log('Mongoose connection open');
  connection.createCollection('test').then(() => console.log('add collection')),
});

```

#### 连接状态码

为了方便查看对象的状态，connection也提供了一个`readyState`属性，可以方便判断当前连接状态，通过Mongoose.STATES能获取readyState的所有取值：

```json
{ '0': 'disconnected',
  '1': 'connected',
  '2': 'connecting',
  '3': 'disconnecting',
  '99': 'uninitialized',
  disconnected: 0,
  connected: 1,
  connecting: 2,
  disconnecting: 3,
  uninitialized: 99 }
```

所以，理论上你可以这样判断连接是否建立

```javascript
if (Mongoose.STATES[connection.readyState] === 'connected') ....
```

#### 多个连接

同时，如果你想创建多个连接，你可以使用`mongoose.createConnection()`的方式创建一个新的连接，参数什么的和connect一致，剩下的操作一致。例如以下这个官方例子：

```javascript
var mongoose = require('mongoose');
var db = mongoose.createConnection(..);
db.model('Venue', new Schema(..));
var Ticket = db.model('Ticket', new Schema(..));
var Venue = db.model('Venue');
```

## 细品模式

### 虚属性

虚属性（[Virtuals](https://mongoosejs.com/docs/guide.html#virtuals)）是Mongoose提供的一个很有用的功能，它可以方便我们存储一些**不需要存储在数据库中的数据**。例如，我存储一个用户的姓名，只需要存名和姓:

```javascript
  const personSchema = new Schema({
    name: {
      first: String,
      last: String
    }
  });
  const Person = mongoose.model('Person', personSchema);
  const voidsky = new Person({
    name: { first: 'void', last: 'sky' }
  });
```

但是我在读出的时候，需要打印用户的全名，当然我们可以这样做：

```javascript
console.log(voidsky.name.first + ' ' + voidsky.name.last); // void sky
```

但是虚属性提供了一个更方便的方式：

```javascript
personSchema.virtual('fullName').get(function () {
  return this.name.first + ' ' + this.name.last;
});

console.log(voidsky.fullName);
```

如果你用过一些前端框架，你可以把它当作计算属性。但是这个属性不会被持久化到MongoDB中，虚函数有get和set的方法，熟悉setter和getter的就应该知道，一样一样的。

虚属性还提供了一个很有用的[Aliases](https://mongoosejs.com/docs/guide.html#aliases)字段，它可以把Schema的某个属性完全跟一个虚属性进行连接（set和get的绑定），你操作这两个属性的效果完全一致，**这个好处是你可以在存储的时候节约空间，但是获取数据的时候，使用语义化的描述** 。只是Aliases定义的属性不会被持久化到数据库。这里看官方案例：

```javascript
const personSchema = new Schema({
  n: {
    type: String,
    alias: 'name'   // 注意这里，等于创建了一个name的虚属性,绑定到n
  }
});
const Person = mongoose.model(personSchema);

const person = new Person({ name: 'Val' });
console.log(person); // { n: 'Val' }
console.log(person.toObject({ virtuals: true })); // { n: 'Val', name: 'Val' }
console.log(person.name); // "Val"

person.name = 'Not Val';
console.log(person); // { n: 'Not Val' }
```



### 子模式

schema是可以嵌套的，如果我们需要定义一个复杂的结构，可以使用嵌套的方式（通常子模式会配置成`{ _id: false }`，具体可以参考下面的模式配置）

```javascript
const childSchema = new Schema({
  n: {
    type: String,
    alias: 'name'
  }
}, { _id: false });

const parentSchema = new Schema({
  // If in a child schema, alias doesn't need to include the full nested path
  c: childSchema,
  name: {
    f: {
      type: String,
      // Alias needs to include the full nested path if declared inline
      alias: 'name.first'
    }
  }
});
```

### 模式配置

schema是有很多配置的，你可以在定义Schema的时候配置，或者使用set方法。

```javascript
new Schema({..}, options);

// or

const schema = new Schema({..});
schema.set(option, value);
```

相关配置可以参考[文档](https://mongoosejs.com/docs/guide.html#options)，这里举例几个可能会用到的：

- **colleciton** 这个配置可以修改collection的名字（默认是使用model的复数化名称，见下面）

```javascript
const dataSchema = new Schema({..}, { collection: 'data' });
const dataModel = mongoose.model('othername', dataSchema);
```

### 模式方法



（未完待续）





### 额外推荐：可视化客户端

当然，你可以通过命令行查看结果，但是更多人还是习惯使用图形化的界面，这里推荐一个MongoDB的可视化客户端：[robot 3T](https://robomongo.org/download)，这就是原来大名鼎鼎的RoboMongo。全平台都支持。要是之前我还真不想推荐它，因为在mac上显示实在难受，不支持retina屏幕。

![201809181537253809983.png](https://img.hksite.cn/201809181537253809983.png?imageMogr2/thumbnail/750x)

但是现在，已经好了很多

![20180918153725385399749.png](https://img.hksite.cn/20180918153725385399749.png?imageMogr2/thumbnail/750x)

