![](https://user-gold-cdn.xitu.io/2020/7/22/17373a8ea387ca3c?w=900&h=383&f=png&s=75165)

> 前沿：前半年微前端火得一踏糊涂，刚好业务需求上有这样的应用场景，针对目前的微前端解决方案做了技术选型，qiankun作为蚂蚁金服内部孵化出来的微前端解决方案，经过线上应用充分检验及打磨最后开源，最终选择qiankun作为我们云产品架构入口。不过官方文档上关于上线部署文档较少，很多童鞋也可能只是在本地玩玩，没有到真正走通整个闭环，于是结合自身，在将qiankun落地过程中遇到的“那些坑”做个梳理。希望对你有所帮助

### 1.🍵 茶前点心

> 本文不大篇幅介绍关于qiankun的“前世今生”，更多的设计理念介绍请看[文档](https://qiankun.umijs.org/zh/guide/#%E4%BB%80%E4%B9%88%E6%98%AF%E5%BE%AE%E5%89%8D%E7%AB%AF) 
> 如果有想了解其他微前端解决方案的童鞋，也可以回顾下之前树酱分享的[微前端那些事](https://juejin.im/post/5e83f8ad6fb9a03c5e0ccccc) 

举个例子：我们有个这样的场景，我有个门户Portal的登陆界面(主应用基座)，登陆成果后可以切换不同的子应用，如下有两个子应用A和B，且都在之前是独立部署的，单独可以访问，但是我们现在想借助qiankun把他们“嵌”到基座来加载，往下看实操

你可能会问直接用iframe不香吗？真不香，主要几个局限问题

- 父子应用之间通信问题
- cookie共享问题（可做单点登陆SSO）
- 交互视图效果不佳


![](https://user-gold-cdn.xitu.io/2020/7/22/17373c50b74c48a2?w=1648&h=582&f=png&s=118966)

上面是一个通过域名访问子应用的示意图，接下来我们看看一个view视图，header头部和sideMenu左侧菜单是属于portal门户的，而右侧区域则是显示切换子应用的视图，预期效果：当我们访问```dev.portal.com/a```该域名时（即切换到子应用A），左侧菜单也会根据不同应用切换不同数据

![](https://user-gold-cdn.xitu.io/2020/7/22/17373c6434c729cb?w=1406&h=758&f=png&s=78373)


#### 1.1 注册子应用时应该注意哪些问题？

> 👦 啊明同学：qiankun是如何注册子应用的呢？

qiankun是通过```registerMicroApps(apps, lifeCycles)```API来注册子应用的，详细文档请看[点我👉](https://qiankun.umijs.org/zh/api/#%E5%9F%BA%E4%BA%8E%E8%B7%AF%E7%94%B1%E9%85%8D%E7%BD%AE)  用来实现当浏览器 url 发生变化时，自动加载相应的子应用的功能，结合上面的例子我们试着在基座main.js注册子应用

![](https://user-gold-cdn.xitu.io/2020/7/18/17360ca50154cbc6?w=2960&h=1968&f=png&s=450941)
主要包括：
- ```entry```: 子应用的 entry 地址，比如我们现在有两个子应用A和B，那么这里配置的就是他们的资源访问域名或ip
- ```render```：本质上是container的转换，container用来定义子应用的容器节点的选择器或者 Element 实例，这里使用的是实际例子
- ```activeRule```：子应用的激活规则，即什么路由访问才会去fetch entry配置的域名或ip，我们用了```getActiveRule```来完成匹配，我们看看```getActiveRule```的实现,该函数通过传入当前 location 作为参数，然后根据函数返回数值来看，若返回值为 true 时则表明当前子应用会被激活，则去调用entry入口配置

![](https://user-gold-cdn.xitu.io/2020/7/19/17364fa26188668a?w=2928&h=1032&f=png&s=267858)

匹配如下
```
✅ https://dev.portal.com/a

✅ https://dev.portal.com/a/anything/everything

🚫 https://dev.portal.com/c
```
匹配成功后，qiankun 通过 fetch 去获取所匹配子应用的静态资源

#### 1.2 资源访问跨域如何解决？
> 👦  啊呆同学：你这样不会跨域吗？基座 https://dev.portal.com/ 获子应用a的资源 https://dev.monitor.com/a的资源 ，根据浏览器同源策略(浏览器采用同源策略,禁止页面加载或执行与自身来源不同的域的任何脚本)应该获取不到吧，明显跨域

答案：是，由于 qiankun 是通过 fetch 去获取子应用注册时配置的静态资源url，所有静态资源必须是支持跨域的，那就得设置允许源了，简单的设置可以看下面

![](https://user-gold-cdn.xitu.io/2020/7/22/17373fe23865e46b?w=4096&h=1968&f=png&s=621477)

- ```Access-Control-Allow-Origin```：跨域在服务端是不允许的。只能通过给Nginx配置`Access-Control-Allow-Origin *`后，才能使服务器能接受所有的请求源（Origin）
- ```Access-Control-Allow-Headers```: 设置支持的Content-Type
- 

#### 1.3 子应用加载失败是什么问题？
> 👦  啊明同学：跨域解决了，可还是fetch不到子应用a的静态资源？是什么问题咋搞？

出现这个报错：```Application died in status LOADING_SOURCE_CODE: You need to export the functional lifecycles in xxx entry```

答案：你的打包姿势不对

> vue-cli 3x项目中需要通过在vue.config.js配置output来配置输出的方式，如下👇所示

![](https://user-gold-cdn.xitu.io/2020/7/22/17374132cbf0a234?w=4096&h=1680&f=png&s=559863)
- ```pubilcPath```: 主要解决的是子应用动态载入的 脚本、样式、图片 等地址不正确的问题
- ```output.library```：需要与主应用注册子应用时的name一致且唯一
- ```output.libraryTarget:umd ``` : 导出umd格式，可以支持inport、require和script引入

然后创建一个publichPath文件，并在main.js 引入

![](https://user-gold-cdn.xitu.io/2020/7/22/173740e3a54f2be3?w=2996&h=1104&f=png&s=335302)

#### 1.4 子应用的publichPath到底应该怎么配置？
>  👦  啊明同学：打包output配置改好了，但是为什么publichPath路径配置为/a?

拓展: 沿用上文提到的a应用的访问域名 dev.monitor.com/a

现在浏览器要正确获取a应用的静态资源中的css文件,则会去访问 dev.monitor.com/a/css/common.css


主要分两种情况：

- publichPath如果默认配置或者配置为/，则生成的index.html 访问的资源是<link href="/common.css" rel="prefetch">则不正确，因为将访问的是dev.monitor.com/css/common.css并不是a应用的资源
- 配置为/a，则生成的index.html 访问的资源是<link href="/a/common.css" rel="prefetch"> 就可以


![](https://user-gold-cdn.xitu.io/2020/7/22/173741ec2dd03d2a?w=787&h=142&f=png&s=30675)

> 👦 啊呆同学：那publichPath路径配置为```./```相对路径可以吗?

答案：也是可以的，跟配置为```/a```访问一样


#### 1.5 如何保障原来的应用运行正常，但能集成到基座portal中
> 👦 啊明同学：我之前a应用是单独运行部署的，我通过qiankun集成到基座portal中会有影响吗？

答案：使用这个全局变量来区分当前是否运行在 qiankun 的主应用中

那就是： ```window.__POWERED_BY_QIANKUN_```那可以用来干嘛？请看下面👇

![](https://user-gold-cdn.xitu.io/2020/7/22/173742be74f67822?w=3132&h=1968&f=png&s=532657)

- 独立运行：```window.__POWERED_BY_QIANKUN__```为false，执行mount创建vue对象
- 运行在qiankun: ```window.__POWERED_BY_QIANKUN__```为true，则不执行mount

#### 1.6 父应用如何共享util和data给子应用
>  👦   隔壁老王同学：如果我想把门户登陆应用登陆成功获取到的个人数据共享给子应用还有一些公用的方法，我该怎么做？

答案：可以在注册子应用的时候，把定义好要共享的msg，通过props共享出去

![](https://user-gold-cdn.xitu.io/2020/7/22/173743f430c013b4?w=2668&h=2328&f=png&s=492064)
- ```msg.data``` 把store状态管理数据共享给子应用
- ```msg.prototype``` 定义一些原型数据，比如是否为qiankun上下文中

父应用定义完，那子应用是如何获取呢？是通过在子应用挂载前，将props数据导到子应用通过遍历赋值给到子应用vue原型中
![](https://user-gold-cdn.xitu.io/2020/7/22/173743f25c5d7e30?w=2116&h=1536&f=png&s=390250)

#### 1.7 history路由模式，需要如何配置ngnix，才能正常访问？
>  👦  啊宇同学：我看你访问的路由模式不是hash，而是history模式，那你是怎么解决当页面刷新404问题？

答案：通过nginx配置加入try_files，history 模式同样会有一个问题，就是当页面刷新时，如果没有合适的配置，会出现404错误，针对这种请看，需要额外在nginx配置，对于找不到url的，将首页html返回

![](https://user-gold-cdn.xitu.io/2020/7/22/17374535daef05d3?w=3028&h=2832&f=png&s=624810)
- try_files：用来解决nginx找不到client客户端所需要的资源时访问404的问题

- proxy_pass：主要是用来配置接口网关反向代理，可以使得父子应用下访问的api是一致的，防止接口跨域问题

了解更多ngnix配置学习，请看树酱之前写的[nginx那些事](https://juejin.im/post/5e7ad2455188255e2c7256ac)