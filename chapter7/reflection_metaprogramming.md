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
