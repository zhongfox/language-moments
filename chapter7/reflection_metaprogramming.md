# 7.2 反射与元编程

> 反射(reflection)也被称作内省(introspection), 通常表示一个程序可以审视自身的状态和结构, 尤其是类型

> 反射是元编程的一种形式

---

### 1. Ruby

元编程是Ruby的重要特点之一, 经典学习材料是[Ruby元编程](https://book.douban.com/subject/26575429/)

我的学习笔记: [Ruby 元编程学习笔记](https://zhongfox.github.io/2013/03/17/ruby-metaprograming/)

---

### 2. Javascript

ES6 提供了Proxy, Reflect.

Proxy 用于修改某些操作的默认行为, 等同于在语言层面做出修改, 所以属于一种「元编程」（meta programming）, 即对编程语言进行编程.

Reflect对象的方法与Proxy对象的方法一一对应，只要是Proxy对象的方法，就能在Reflect对象上找到对应的方法。这就让Proxy对象可以方便地调用对应的Reflect方法，完成默认行为，作为修改行为的基础。也就是说，不管Proxy怎么修改默认行为，你总可以在Reflect上获取默认行为.

#### Proxy

Proxy 实际上重载「overload」了运算符，即用自己的定义覆盖了语言的原始定义.

Proxy 的应用场景举例:

* 只读视图: 覆写所有五种可变方法, set, defineProperty, deleteProperty, preventExtensions, setPrototypeOf
* Web 服务的客户端: 一个接口拦截所有请求path
* 数据库ORM
* methode missing (类似ruby)
* 测试框架也可以用代理来创建模拟对象（mock object）

关于Proxy的好文章可以参考:

* [代理 Proxies](http://www.infoq.com/cn/articles/es6-in-depth-proxies-and-reflect/)
* [ECMAScript 6简介: Proxy](http://es6.ruanyifeng.com/#docs/proxy)

#### Reflect

参考:

[ECMAScript 6简介: Reflect](http://es6.ruanyifeng.com/#docs/reflect)

#### ES5 Object.defineProperty

> Object.defineProperty(obj, prop, descriptor)

* 作用: 直接在一个对象上定义一个新的属性，或者是修改已存在的属性
* 参数:
  * obj：必需, 目标对象
  * prop：必需, 需定义或修改的属性的名字
  * descriptor：必需, 描述符, 包括属性描述符(value, writable, configurable, enumerable)和存取描述符(get, set)
  * set: 可选, 该属性的setter
  * get: 可选, 该属性的getter
* 返回值: 目标对象, 即第一个参数

**属性描述符**:

|              | 含义           | 默认值    | 作用                                                                                                           |
|--------------|----------------|-----------|----------------------------------------------------------------------------------------------------------------|
| value        | 属性的值       | undefined |                                                                                                                |
| writable     | 属性是否可写   | true      | 如果设置成 false，则任何对该属性改写的操作都无效（但不会报错）                                                 |
| configurable | 属性是否可配置 | true      | 如果为false，则任何尝试删除/修改目标属性, 或修改属性特性（writable, configurable, enumerable）的行为将被无效化 |
| enumerable   | 属性是否可枚举 | true      | 如果设为false, 则在for-in循环中, 在Object.keys中, 都不能列举出来                                               |

注意, 以上默认值是指的普通属性的配置默认值, 如果是用`Object.defineProperty` 设置属性, 那么以上配置是从第三个参数`descriptor`中获取, 如果不传就是undefined/false

**存取器描述**

* 当使用了getter或setter方法，不允许使用writable和value这两个属性
* get或set不是必须成对出现，任写其一就可以
* getter, setter 中的this指向目标对象

getter/setter常见使用场景:

* 读取属性时改变对象状态, 如[chai](http://chaijs.com/)的链式调用: `expect(foo).to.not.equal('bar')`中, to, not都是在对应的getter中进行对象属性操作
* 增加属性获取和修改时的信息

#### 获取描述符:

> Object.getOwnPropertyDescriptor(obj, prop)

---

### 3. Go

![反射三定律](/images/reflection.png)

#### 反射第一定律(红色): 反射可以将「接口类型」转换为「反射类型对象」

反射类型:
* reflect.Type 通过`reflect.TypeOf(interface{})`获取
* reflect.Value 通过`reflect.ValueOf(interface{})`获取

#### 反射第二定律(黄色): 反射可以将「反射类型对象」转换为「接口类型变量」

`func (v Value) Interface() interface{}`

然后可以通过断言, 恢复底层的具体值:

```go
f := 8.8
reflectValue := reflect.ValueOf(f)       //reflect.Value
ff := reflectValue.Interface().(float64) //float64
```

#### 反射第三定律: 如果要修改「反射类型对象」, 其值必须是「可写的」（settable）

---

### 4. Lua

Lua 中的 metatable 和 week table 可以认为是一种元编程. <!--todo-->

---
