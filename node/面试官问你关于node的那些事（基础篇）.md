![](https://user-gold-cdn.xitu.io/2020/6/21/172d4ca6487bfe5a?w=900&h=383&f=png&s=124039)
> 前沿：文章的起源，是树酱的朋友在最近面试中对部分岗位中对`node`掌握程度要求感到很“慌
> ”，当然`node`已渐渐从很多公司招聘的“加分项”转变为“强指标”，“慌”的原因无非是因为平时项目无需用到或者说少用node，再加上我们的知识吸收，大多都是碎片化的知识拼接起来，缺少一个完整体系化的梳理，借此机会，重新梳理一下，当然更多的还需要你参与实战，本文是基础篇。

### 1.饭前点心 🥧
> 👨提问：最近Deno很火，会不会替代node的替代品，学node是不是没有前途？

莫慌，Node依旧是社区热捧的服务器端 JavaScript 运行环境，Deno的出现其实本质上是完善现阶段的Node（新轮子）,包括原生支持TS、安全性、支持ES Module浏览器模块、等特征。万变不离其宗，虽然有了Deno，将来可能就不需要 Node.js，但是新事物总是需要不断推演和考验后，所以这一点而言，Node短时间内很难被替换，毕竟背后依附着强大社区的支撑

关于 Deno 更多了解，你可以看

- [Deno 正式发布，彻底弄明白和 node 的区别](https://juejin.im/post/5ebcad19f265da7bb07656c7#heading-10)
- [Deno 运行时入门教程：Node.js 的替代品](http://www.ruanyifeng.com/blog/2020/01/deno-intro.html)


### 2.正餐 🍔

> 通过一些node常见的问题跟你聊聊一些基本知识

#### 2.1 node 如何获取命令行传来的参数

> 答案是：`process.argv`。process是一个全局变量，它提供当前 Node.js 进程的有关信息，而process.argv 属性则返回一个数组，数组中的信息包括启动Node.js进程时的命令行参数

举个场景：我们需要在script定义一个node的命令，然后执行改文件后获取不同的参数

![](https://user-gold-cdn.xitu.io/2020/6/21/172d4fd57874aaf3?w=2116&h=1248&f=png&s=298396)

> 拓展：为什么splice(2)？让我们看看其他参数的数值

- process.argv[0] : 返回启动Node.js进程的可执行文件所在的绝对路径
- process.argv[1] : 为当前执行的JavaScript文件路径
- process.argv.splice(2) : 移除前两者后，剩余的元素为其他命令行参数(也就是我们自定义部分)

![](https://user-gold-cdn.xitu.io/2020/6/21/172d5055d1b062ed?w=1092&h=162&f=png&s=27822)

> 关于获取命令行传来的参数还可以结合[commander](https://github.com/tj/commander.js)的 `commander.parse(process.argv);`

#### 2.2 node有哪些相关的文件路径？
> 答案是：Node 中的文件路径有 ```__dirname```, ```__filename```, ```process.cwd()```, ```./ 或者 ../```下面用一个例子来介绍这几种文件路径的区别

先看看我们当前运行的目录的结构
```
KSDK/
    -src/ 
        -test.js
```
在 test.js 里写下如下的code👇
![](https://user-gold-cdn.xitu.io/2020/6/21/172d53aaefb9f7b0?w=2892&h=1176&f=png&s=350946)
然后分别在src下运行和KSDK下运行对比下结果如何？

- 在src目录下运行

![](https://user-gold-cdn.xitu.io/2020/6/21/172d53c928d6f532?w=1136&h=266&f=png&s=42979)
- 在KSDK目录下运行


![](https://user-gold-cdn.xitu.io/2020/6/21/172d53d591c3bfa3?w=1386&h=272&f=png&s=46267)

对比看到，只有我们后两者产生变化，可以得出如下结论 🚀

- ```__dirname```: 总是返回被执行的 js 所在文件夹的绝对路径
- ```__filename```: 总是返回被执行的 js 的绝对路径
- ```process.cwd()```: 总是返回运行 node 命令时所在的文件夹的绝对路径

#### 2.3 node相关path API 有哪些？
> 答案：path 模块提供了一些实用工具，用于处理文件和目录的路径，常用api有：`path.dirname`、`path.join`、`path.resolve` 其他的看文档 [Path API](http://nodejs.cn/api/path.html)

- ```path.dirname()```： 返回 path 的目录名
- ```path.join()```：所有给定的 path 片段连接到一起，然后规范化生成的路径
- ```path.resolve()```：方法会将路径或路径片段的序列解析为绝对路径，解析为相对于当前目录的绝对路径，相当于cd命令

我们用个简单的demo来区分 

![](https://user-gold-cdn.xitu.io/2020/6/21/172d54d34923f276?w=3060&h=1824&f=png&s=600039)

> 我们看到```path.join(__dirname, '../lib/common.js')```和 ```path.resolve(__dirname, '../lib/common.js')```返回的结果相同，难道可以相互替换？你在看下面这个例子👇，你或许会清晰些

路径还是上面演示的路径

- join是把各个path片段连接在一起， resolve把```／```当成根目录

```
path.join('/a', '/b') // '/a/b'
path.resolve('/a', '/b') //'/b'
```

- join是直接拼接字段，resolve是解析路径并返回

```
path.join("a","b")  // "a/b"
path.resolve("a", "b") // "/Users/tree/Documents/infrastructure/KSDK/src/a/b"
```

#### 2.4 node的文件读取怎么做的？
> 答案：通过fs文件系统模块提供的API，也是node中重要的模块之一，fs模块主要用于文件的读写、移动、复制、删除、重命名等操作。

通过一个简单的重命名api```rename```使用，之前在做脚手架中使用到
![](https://user-gold-cdn.xitu.io/2020/6/21/172d573eb51e7ebc?w=4084&h=1320&f=png&s=513453)

> ⏰需要注意的是，使用require('fs')载入fs模块，fs模块中所有方法都有同步和异步两种形式,刚刚我们展示的rename是异步方法的调用，因为在繁忙的进程中，应使用异步方法， 同步的版本会阻塞整个进程（停止所有的连接），当然fs.rename对应的同步方法就是```fs.renameSync```

下面我们看一个同步方法的演示，判断文件是否存在

![](https://user-gold-cdn.xitu.io/2020/6/21/172d57f461ff8901?w=3804&h=1392&f=png&s=391249)

> 还有一点要注意的是，无论同步异步尽量对抛出的异常做相应的处理

#### 2.5 node的url模块是用来干嘛的？

> 答案是：用来对url的字符串解析、url组成等功能，主要包括以下几个API。```url.parse()```、```url.format()```

- ```url.parse```：可以将一个url的字符串解析并返回一个url的对象
- ```url.format```:将传入的url对象编程一个url字符串并返回

以```url.parse```作为例子：

![](https://user-gold-cdn.xitu.io/2020/6/21/172d5da27c46a7d3?w=2020&h=1176&f=png&s=260827)

解析出来结果

```
Url {
  protocol: 'http:',
  slashes: true,
  auth: null,
  host: 'baidu.com:8080',
  port: '8080',
  hostname: 'baidu.com',
  hash: '#node',
  search: '?query=js',
  query: 'query=js',
  pathname: '/test/h',
  path: '/test/h?query=js',
  href: 'http://baidu.com:8080/test/h?query=js#node'
}
```

#### 2.6 node的http模块创建服务与Express或Koa框架有何不同?
>  答案是：express是一个服务端框架,框架简单封装了node的http模块,express支持node原生的写法,express不仅封装好服务器，还封装了中间件、路由等特征，方便开发web服务器，换句话说express = http模块 + 中间件 + 路由

先看看```http```模块是如何实现一个简单的服务器

![](https://user-gold-cdn.xitu.io/2020/6/21/172d595f0e3123f5?w=2924&h=1392&f=png&s=396279)

```运行3000端口``` 即可访问到浏览器打印出```hello node.js```

接下来看看express如何实现


![](https://user-gold-cdn.xitu.io/2020/6/21/172d5bd5a1df942b?w=2020&h=2616&f=png&s=649251)


以上就实现一个简单的服务端逻辑，包含中间件、路由设置

#### 2.7 Express和Koa框架中间件有什么不同？

> 答案：中间件： ```app.use```方法就是往中间件队列中塞入新的中间件，express中间件处理方式是线性的，next过后继续寻找下一个中间件，当然如果没有调用next()的话，就不会调用下一个函数了，调用就会被终止

- express 中间件：是通过 next 的机制，即上一个中间件会通过 next 触发下一个中间件
- koa2 中间件：是通过 async await 实现的，中间件执行顺序是“洋葱圈”模型（推荐）


![](https://user-gold-cdn.xitu.io/2020/6/21/172d5ba955e7ae5f?w=478&h=435&f=png&s=46930)

我们看下koa2下面这个简单的例子，你可以对比下Express的实现

![](https://user-gold-cdn.xitu.io/2020/6/21/172d5cb771fd9d6f?w=2020&h=2184&f=png&s=492107)
看看输出的日志

![](https://user-gold-cdn.xitu.io/2020/6/21/172d5be181acd5d9?w=1696&h=252&f=png&s=33148)

#### 2.8 什么是模版引擎？
> 答案是：模板引擎是一个通过结合页面模板、要展示的数据生成HTML页面的工具，本质上是后端渲染（SSR）的需求，加上Node渲染页面本身是纯静态的，当我们需要页面多样化、更灵活，我们就需要使用模板引擎来强化页面，更好的凸显服务端渲染的优势

常见主流模版引擎有：

- art-template [官方文档](http://aui.github.io/art-template/) ：号称效率最高的，模版引擎
- ejs  [官方文档](http://ejs.co/)：是一个JavaScript模板库，用来从JSON数据中生成HTML字符串。
- pug [官方文档](https://pugjs.org/api/getting-started.html)：是一款健壮、灵活、功能丰富的模板引擎,专门为 Node.js 平台开发


未完待续...
