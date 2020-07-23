![](https://user-gold-cdn.xitu.io/2020/5/6/171ea44fefa5a4f1?w=1920&h=600&f=png&s=44905)

> 日常开发中，我们使用到的Js定义的每一个值都属于某一种数据类型，常见的js数据类型有String（字符串）、Number（数字）、Boolean（布尔）、Object、Undefined、Null、Symbol等等，其中Symbol是ES6引入的新的数据类型，表示独一无二的数值。因为 JS 本身是一门弱类型语言，以至于类型转换发生的频繁很高，本文旨在帮助大家梳理各种类型之间的相互转换，在每一小节讲解转换前，还会跟大家介绍这些“老朋友”

数据转换分为显示转换和隐式转换

- ☀️显示转换：常见的️显式转换方法有：Boolean()、Number()、String()等等 
- 🌛隐式转换：常见的隐式转换方法：四则运算(加减乘除) 、== 、判断语句(if)等


### 1.String 
>  String是存储字符的变量,String使用长度属性length来计算字符串的长度

#### 1.1 String转换为Number

- parseInt(string, 10)
> parseInt() 函数可解析一个字符串，从位置 0 开始查看每个字符，直到找到第一个非有效的字符为止,最后并返回一个整数。

parseInt() 方法还有基模式，可以把二进制、八进制、十六进制或其他任何进制的字符串转换成整数。基是由 parseInt() 方法的第二个参数指定的

![](https://user-gold-cdn.xitu.io/2020/5/7/171ed371e21d8805?w=1176&h=590&f=png&s=113569)

- parseFloat(string)
> 相比上一节parseInt函数是将值转换成整数，parseFloat函数则是将值转换成浮点数且该方法方法也没有基模式（转换不了），只有对 String 类型调用这些方法，它们才能正确运行


![](https://user-gold-cdn.xitu.io/2020/5/7/171ed3a479c7edf0?w=1792&h=1036&f=png&s=238425)

- Number(string)
> Number() 函数的强制类型转换与 parseInt() 和 parseFloat() 方法的处理方式相似，只是它转换的是整个值，而不是部分值

上两节提到的parseInt() 和 parseFloat() 方法只转换第一个无效字符之前的字符串，因此 "1.2.3" 将分别被转换为 "1" 和 "1.2"。
而用Number() 进行强制类型转换，"1.2.3" 将返回 NaN，因为整个字符串值不能转换成数字。如果字符串值能被完整地转换

![](https://user-gold-cdn.xitu.io/2020/5/7/171ed3ae56359766?w=1812&h=892&f=png&s=183722)

- `+ string`
> 通过在字符串前面加了个加号，这个数值就变成了number类型

![](https://user-gold-cdn.xitu.io/2020/5/7/171ed7fcdc4a1548?w=2052&h=892&f=png&s=196386)

#### 1.2  String转Object
> 通过JSON.parse来完成，该注意的是JSON.parse遇到不可解析的字符串时，会抛出SyntaxError异常。

![](https://user-gold-cdn.xitu.io/2020/5/7/171ed91df165267d?w=2264&h=892&f=png&s=206624)

#### 1.3  String转Object（Array数组类型）

![](https://user-gold-cdn.xitu.io/2020/5/7/171ed95c182d8446?w=2220&h=892&f=png&s=189898)

### 2.Number 
> Number类型是以IEEE-754标准格式来表示的，包括整数和浮点数，如果是计算会转化为2进制再计算，这也是0.1 + 0.2不等于0.3的原因

拓展：为什么在 JavaScript 中，0.1+0.2 不等于 0.3：

```
console.log( 0.1 + 0.2 == 0.3);  //false
```

> 因为在JavaScript中的二进制的浮点数0.1和0.2并不是十分精确，在他们相加的结果并非正好等于0.3，而是一个比较接近的数字 0.3000000x ，所以条件判断结果为false。

问题：有没有方法可以解决上述问题呢❓
可以使用 JavaScript 提供的最小精度值Number.EPSILON，在这个误差的范围内就可以判定0.1+0.2===0.3为true，如下👇所示

![](https://user-gold-cdn.xitu.io/2020/5/7/171ed794c0344cbf?w=2188&h=1108&f=png&s=288270)

多数情况下，Number 比 parseInt 和 parseFloat 等方法会更好

#### 2.1 Number转String
- n.toString( )
> toString() 方法把数字转换成指定进制形式的字符串。

![](https://user-gold-cdn.xitu.io/2020/5/7/171ed7a10d3b25c2?w=2004&h=964&f=png&s=227518)

- 一元运算符 +
> 通过在数字后面加了个空字符串，这个数值就变成了string类型

![](https://user-gold-cdn.xitu.io/2020/5/7/171ed83f90fc7b54?w=2116&h=892&f=png&s=202321)

#### 2.2 Number转Boolean
> number类型转Boolean，除了0数值和NaN对应的是false,其他数值都对应true

![](https://user-gold-cdn.xitu.io/2020/5/7/171ed8b7374b0e63?w=2052&h=892&f=png&s=198320)

### 3.Boolean
> Boolean 类型有且只有两种值:true和false，主要用来表示逻辑意义上的真和假
> boolean 这个类型比较简单，这里就不做复杂介绍

除了下面六个值被转为false，其他都为true

![](https://user-gold-cdn.xitu.io/2020/5/7/171eddb77c266c73?w=2140&h=1180&f=png&s=255656)

### 4.Object

> Object对象是js中比较复杂的数据类型，涉及的东西比其他类型都多，简单描述对象的话，可以说是由key-value聚合的数据集合，即属性的集合。JS对象主要可以分为两大类，分别是内置对象和宿主对象

- 内置对象: JS内置对象也被定义为内部类，换句话说就是JavaScript里面封装好了的类，内部类大致有：Array，Boolean,RegExp，Date，Math，Number，String，也就是我们平时看到的 如 ```new Date();```
- 宿主对象: JS所运行的环境提供的对象如：BOM中的Window、DOM中的document

数组(Array)、日期(Date)、null等的数据类型都是 object

这里也介绍不同类型对象toString()方法的返回值

![](https://user-gold-cdn.xitu.io/2020/5/7/171edd88d035d541?w=2588&h=1396&f=png&s=420447)

> 注意：比如 10 与 new Number(10) 是两个不同的值，前者是 Number 类型， 后者是对象类型

再举个列子比如 new Date 与 Date(),虽然得出结果一样，但内置对象 Date 作为构造器new 将产生新的对象,而作为函数时，则产生字符串，如下所示👇

![](https://user-gold-cdn.xitu.io/2020/5/7/171edea63c74b495?w=2924&h=964&f=png&s=289655)


#### 4.1 Object转为String


![](https://user-gold-cdn.xitu.io/2020/5/7/171ed992b7a0dc33?w=2220&h=1108&f=png&s=250902)

#### 4.2 Object对象转Object数组
> 对象转数组方式很多，其中包括以下几种👇
- Object.values(object)：返回一个对象所有可枚举属性对应数值组成的数组
- Object.keys(object)： 返回一个对象的自身可枚举属性组成的数组
- Object.entries(object)：返回一个给定对象自身可枚举属性的键值对数组


![](https://user-gold-cdn.xitu.io/2020/5/7/171eda98420caade?w=3412&h=1252&f=png&s=374183)

> 如果类数组对象或者可遍历的对象要转换，还可以用Array.from()方式，不过前提是object中必须有length属性，返回的数组长度取决于这个object中length长度，同时object的key值必须是数值


![](https://user-gold-cdn.xitu.io/2020/5/7/171edad588068878?w=2972&h=1252&f=png&s=306615)
小朋友👦👧你是否有很多问号❓类对象是啥？
> 类数组对象你可以看做一种“伪数组”，虽然它无法调用数组的方法，但是具备length属性，可以索引获取内部项的数据结构

#### 4.3 日期Object转Number
> 将日期对象转换为数字（时间戳的形式）,可以通过Number() 或者日期方法 getTime() 

![](https://user-gold-cdn.xitu.io/2020/5/7/171edc047a1ff43e?w=2624&h=1036&f=png&s=291550)


#### 4.4 数组Object转String
> 通过join或toString()的方法，join()可以指定分隔符,如果不加参数，则默认使用逗号作为分隔符，与 toString() 方法转换操作效果相同

![](https://user-gold-cdn.xitu.io/2020/5/7/171eddfb963d0cd2?w=2452&h=964&f=png&s=209202)

> 通过[1，2，3，4]初始化与new Array()作用一样，也是创建了一个对象，新建的对象a.__proto__ == Array.prototype

### 5 Undefind和Null
> 两者都是JavaScript语言用来表示"无"的值，且两者有共同点也有不同点，共同点在于都只有一个值，Null只有一个值 null,Undefined也只有一个值undefined。不同点在于Null 表示为‘定义了但是值为空’,而Undefind 表示为'这里应该有一个值，但是还没有定义'

要注意的是，如果我们用typeof来判断null的类型，会判定为 Object 类型，而不是Null类型只是为什么呢？

> 是因为JavaScript 数据类型在底层都是以二进制的形式表示的，二进制的前三位为 0 会被 typeof 判断为对象类型，而 null 的二进制位恰好都是 0 ，因此，null 被误判断为 Object 类型。此时我们可以用上一节谈到的通过Object上的 原型上的toString()方法，如下👇 `Object.prototype.toString.call(null)  //[object Null] `来区分

#### 5.1 Undefind和Null转Number
> undefined无法转为数字、而Null被转换为0

![](https://user-gold-cdn.xitu.io/2020/5/7/171ee09838bb7b10?w=2116&h=964&f=png&s=218844)

再看一个例子👇


![](https://user-gold-cdn.xitu.io/2020/5/7/171ee0ed1d24cbb8?w=2828&h=1252&f=png&s=327302)

> undefined无法转为数字,第一个调用返回NaN.第二个是null转为隐式转换为0所以是2 ，第三个是如果传入的参数是undefined会以默认值为准，所以是3


#### 5.2 总结
- 不要对一个显式变量的赋值 undefined，当需要释放一个对象时，直接赋值为 null 即可
- `==` 双等号中如果两个值类型不同，也有可能相等，`undefind == null`就是其中一个，包括 `1 == '1'`，但是如果null与undefined与其他数相等运算时就不行，因为它们不进行类型转换（隐式转换）

### 6.Symbol
> Symbol是ES6新引入的数据类型，表示独一无二的值，类似于一种标识唯一性的ID，Symbol 函数不同的是，直接用new 调用它会抛出错误，因为生成的是原始类型值，不是对象，是 Symbol 对象的构造器。下面简单用一个例子就能告诉你如何独一无二👇


![](https://user-gold-cdn.xitu.io/2020/5/7/171ee189402d5cd3?w=2492&h=1036&f=png&s=243178)

symbol不能与其他类型的值进行运算，会报错（即不能隐式转换），但是部分可以显示转换为字符串或者布尔值

![](https://user-gold-cdn.xitu.io/2020/5/7/171ee2bc8bc615bd?w=3396&h=1180&f=png&s=460771)

### 7.拓展

#### 7.1 类型判断方法

因为类型识别中，如果使用 typeof 来判断数据类型，只能区分基本类型，即 number、string、undefined、boolean、object、function、symbol这几种，但是对于数组、null、对象这些来说，使用 typeof 都会统一返回 “object” 。这时候就需要用到其它方式👇
> 通过``` Object.protptype.toString.call()```截取字符串[object...]中间字符串来区分类型，去掉前后符号，比如 "[object Array]"就提取了array来判断,之前写的工具库有定义[点我👉](https://github.com/littleTreeme/kdutil/blob/master/src/modules/tools/index.js)

![](https://user-gold-cdn.xitu.io/2020/5/7/171ee2f64add2494?w=2556&h=1036&f=png&s=247623)

那直接用 ```Object.prototype.toString(1)``` 可以吗？答案是不行的，因为考虑到为了每个对象都能通过，所以才需要以 Function.prototype.call()的形式来调用，传递要检查的对象作为第一个参数

![](https://user-gold-cdn.xitu.io/2020/5/14/17211b90ff22c1eb?w=2792&h=1036&f=png&s=311065)

在举个例子，看如下👇

![](https://user-gold-cdn.xitu.io/2020/5/14/17211c0c2940cf6b?w=2524&h=1036&f=png&s=281150)

为什么Object.prototype和Array.protoType是两个结果？ 这里涉及到一些原型链的问题，这里也大概讲一下

首先js中对象大多继承自Object，当在某个对象上调用方法时，会先优先在该对象上进行查找，如果没找到则会进入对象的原型（也就是.prototype）进行探索，如果还是没找到这个方法，同样的也会进入对象原型的原型进行查找，直到找到或者进入原型链的顶端Object.prototype为止。

所以，比如它调用的是Array.prototype.toString，虽然Array也继承自Object，但js在Array.prototype上重写了toString，所以导致结果不同，而第三个例子toString(),因为本身调用的是Array.prototype.toString，所以结果是跟第二种方式一致。

> 总结：只有Object.prototype上的toString才能用来进行复杂数据类型的判断

