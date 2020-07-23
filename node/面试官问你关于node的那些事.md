![](https://user-gold-cdn.xitu.io/2020/6/21/172d4ca6487bfe5a?w=900&h=383&f=png&s=124039)
> 前沿：续上次面试官问你关于node的那些事普通篇发出，童鞋反馈说“怎么那么基础啊，这也太水了吧” 这里统一做回复，不基础咋叫“基础篇”呢，只是通过自己的角度，希望能帮助大家更好地去学习，于是就有了进阶篇的梳理计划，今天树酱继续跟你聊聊关于node后续的那些事，附上 [面试官问你关于node的那些事（普通篇）](https://juejin.im/post/5eeec838e51d4574134ac467)


#### 1. 今日主食 🍞
#### 1.1 注册路由时 app.get、app.use、app.all 的区别是什么？

> 上一章基础篇提及到如何使用express搭建一个简单的服务端，基础架子完成搭建好，就需要定义接口路由和中间件，这时候我们就需要在入口文件app.js中定义app.get、app.use及app.all等方法，而这三者之前又有什么区别？我们用例子来说明

![](https://user-gold-cdn.xitu.io/2020/6/30/17302c8a3dfd2861?w=2388&h=2040&f=png&s=488821)

当我们请求```/user```路由时，会依次输出```树酱🌲来了```和```Hello World ```，接着浏览器端显示执行完毕，同理访问```/user/tree```则只会输出 ```树酱🌲来了```，为啥呢？

![](https://user-gold-cdn.xitu.io/2020/6/30/17302c2a0cdab0f2?w=1134&h=618&f=png&s=83363)

- app.use(path,callback)

> ```app.use```是express用来调用中间件的方法。中间件通常不处理请求和响应，一般只处理输入数据，并将其交给队列中的下一个处理程序，比如下面这个例子```app.use('/user')```，那么只要路径以 ```/user``` 开始即可匹配，如 ```/user/tree``` 就可以匹配

- app.all()

> app.all 是路由中指代所有的请求方式，用作路由处理，匹配完整路径，在app.use之后
> 可以理解为包含了app.get、app.post等的定义，比如```app.all('/user/tree')```,能同时覆盖：```get('/user/tree') 、 post('/user/tree')、 put('/user/tree')``` ,不过相对于app.use()的前缀匹配，它则是匹配具体的路由


> 总结：一句话概括：all完整匹配，use只匹配前缀

#### 1.2 express response有哪些常用方法?
> express response对象是对Node.js原生对象ServerResponse的扩展，express response常见的有：```res.end()```、```res.send() ```、```res.render() ```、```res.redirect() ```，而这几个有什么不同呢？更多请看文档 [express Response](http://www.expressjs.com.cn/4x/api.html#res)

- res.end()

> 结束response - 如果服务端没有数据回传给客户端则可以直接用res.end返回，以此来结束响应过程

- res.send(body)
> 如果服务端有数据可以使用res.send,可以忽略res.end，body参数可以是一个Buffer对象，一个String对象或一个Array

![](https://user-gold-cdn.xitu.io/2020/6/30/17302e661d23aa61?w=1710&h=288&f=png&s=143037)

- res.render

> res.render用来渲染模板文件，也可以结合模版引擎来使用，下面看个简单的demo (express+ejs模版引擎)

![](https://user-gold-cdn.xitu.io/2020/6/30/17302f3eec017e54?w=2792&h=1824&f=png&s=500638)

首先是配置说明

```
app.set('views', path.join(__dirname, 'views')); // views：模版文件存放的位置，默认是在项目根目录下
app.set('view engine', 'ejs'); // view engine：使用什么模版引擎
```
其次是根据使用的模版引擎语法编写模版，最后通过```res.render(view,locals, callback)```导出，具体使用参数

```
view：模板的路径
locals：渲染模板时传进去的本地变量
callback：如果定义了回调函数，则当渲染工作完成时才被调用，返回渲染好的字符串（正确）或者错误信息 ❌
```

关于 res.render结合模版引擎更多用法，推荐阅读： [Express：模板引擎深入研究](https://zhuanlan.zhihu.com/p/67695057)


- res.redirect

> 重定义到path所指定的URL，同时也可以重定向时定义好HTTP状态码（默认为302）

```
res.redirect('http://baidu.com');
res.redirect(301, 'http://baidu.com');
```

#### 1.3 node如何利用多核CPU以及创建集群?

> 众所周知，nodejs是基于chrome浏览器的V8引擎构建的，一个nodejs进程只能使用一个CPU(一个CPU运行一个node实例)，举个例子：我们现在有一台8核的服务器，那么如果不利用多核CPU，是很一种浪费资源的行为，这个时候可以通过启动多个进程来利用多核CPU

Node.js给我们提供了```cluster```模块，用于nodejs多核处理，同时可以通过它来搭建一个用于负载均衡的node服务集群。

![](https://user-gold-cdn.xitu.io/2020/6/30/173031858f1bee58?w=3060&h=2904&f=png&s=804833)
![](https://user-gold-cdn.xitu.io/2020/6/30/1730337f2290f421?w=1162&h=586&f=png&s=68286)

通过上述代码我们就创建了一个支持多进程和负载均衡的服务，运行结果如下👇
![](https://user-gold-cdn.xitu.io/2020/6/30/173031912122225f?w=1720&h=392&f=png&s=55217)

> 啊呆👦同学：那为什么多个进程可以监听同一个端口呢？

上面运行的Demo中，成功的开启了 1 个 Master 进程及8个 Worker 进程，因为监听的只有3000一个端口，按道理的话，一个端口被多个进程监听是会报端口冲突的，但是这时候却没有报错，奇了怪了？，让我们看下一下端口查看详情👇

![](https://user-gold-cdn.xitu.io/2020/6/30/173031f115a8b0e1?w=1594&h=142&f=png&s=28895)

我去～原来3000端口并不是被所有进程监听，而是仅仅监听 Master 进程（pid为'32101'）, 我们再来看看Master 进程和Worker的关系

![](https://user-gold-cdn.xitu.io/2020/6/30/1730323d4e252301?w=1746&h=420&f=png&s=122776)

Master 通过 cluster.fork() 这个方法创建的，本质上还是使用的 child_process.fork() 这个方法，关于 cluster、child_process、Process等的关系可以看这篇 [Node.js cluster 踩坑小结](https://zhuanlan.zhihu.com/p/27069865)

> 啊宽👦同学：除了上面的方式实现多进程及负载均衡还有其他方式吗？

可以使用PM2工具来实现, pm2内部包含了所有上述的处理逻辑，我们可以不用对原来的代码进行修改，只要再启动的时候使用pm2管理即可，运行```pm2 start test.js -i 2```


![](https://user-gold-cdn.xitu.io/2020/6/30/173032d836366082?w=2006&h=360&f=png&s=71191)

- ```pm2 start test.js -i 2``` 

 意思是cluster mode 模式启动2个app.js的应用实例，这2个应用程序会自动进行负载均衡，```- i```后面的数字表示要启动的工作线程的数量。如果给定的数字为0，PM2则会根据你CPU核心的数量来生成对应的工作线程

> 拓展：我们可以通过借助cluster模块来实现多进程分页爬虫，Node多进程架构可以充分利用 cpu 资源，我们在一些耗时的操作上，可以尝试这种方式来解决。

更多关于pm2 的使用可以看之前树酱写的：[前端运维部署那些事](https://juejin.im/post/5e88904bf265da47f517837c#heading-12)

#### 1.4 node是怎样支持https?
> https实现，离不开证书，通过openssl生成公钥私钥（不做详细介绍），然后基于 express的 https模块 实现，设置options配置, options有两个选项，一个是证书本体，一个是密码


![](https://user-gold-cdn.xitu.io/2020/6/30/1730348b42decca8?w=1894&h=322&f=png&s=63186)

![](https://user-gold-cdn.xitu.io/2020/6/30/173034668387178e?w=2452&h=2112&f=png&s=507684)

#### 1.5 node和客户端怎么解决跨域的问题？
> 答案：可以通过在路由设置里面加了header的设置即可

![](https://user-gold-cdn.xitu.io/2020/6/30/1730393023ba8fc4?w=3132&h=1464&f=png&s=484300)

> 啊乐👧同学：这里使用到app.use('*')是什么意思呀？

后面添加* 可以实现全匹配, ```app.all('*',(req,res,next)=>{})``` 效果相当于```app.use((req,res,next)=>{})```, 这也是app.all的一个比较常见的应用，就是用来处理跨域请求

#### 1.6  node应用内存泄漏咋搞？
> 内存泄漏（Memory Leak）指由于错误造成程序未能释放已经不再使用的内存的情况。如果内存持续占用过高，会影响服务器相应，情况严重直接能让程序奔溃，那么怎么尽量避免这种情况出现，或者出现了怎么排查呢？

导致内存泄漏有主要以下几点：
- 全局变量没有手动销毁，因为全局变量不会被回收
- 闭包：闭包中的变量被全局对象引用，则闭包中的局部变量不能释放
- 监听事件添加后，没有移除，会导致内存泄漏

这也同时涉及到垃圾回收(GC),nodejs是执行javascript的V8引擎，也就是说nodejs的GC就是说V8引擎的GC，而基于GC的原理，内存泄漏就是应该被回收的内存，换句话说就是本应该被标记为可达到对象却没有被正常回收

> 啊开👦同学：那么如果一旦出现内存泄漏怎么检测？

- 通过内存快照，可以使用node-heapdump [官方文档](https://github.com/bnoordhuis/node-heapdump)获得内存快照进行对比，查找内存溢出
- 可视化内存泄漏检查工具 Easy-Monitor [官方文档](https://github.com/hyj1991/easy-monitor#readme)


![](https://user-gold-cdn.xitu.io/2020/6/30/17303b3abe9bbeae?w=1342&h=1016&f=png&s=335408)

#### 1.7 两个node程序之间怎样交互?

> 答案是：通过fork，上面讲过了．原理是子程序用process.on来监听父程序的消息，用 process.send给子程序发消息，父程序里用child.on,child.send进行交互，来实现父进程和子进程互相发送消息

![](https://user-gold-cdn.xitu.io/2020/6/30/17303cba93ce609d?w=2940&h=1824&f=png&s=525474)


![](https://user-gold-cdn.xitu.io/2020/6/30/17303d90c2d342ca?w=1532&h=166&f=png&s=29244)

child_process模块

> 提供了衍生子进程的功能，包括前几节提到的cluster底层实现还是child_process

该模块主要包括以下几个异步进程函数
- fork：就是上面代码中实现父进程和子进程互相发送消息的方法，通过fork可以在父进程和子进程之间开放一个IPC通道，使得不同的node进程间可以进行消息通信。
- exec: 衍生一个 shell 并在该 shell 中运行命令，当完成时则将stdout 和 stderr 传给回调函数，exec的第一个参数，跟shell命令完全相似，场景用来执行命令较多，
- spawn

未完待续...
