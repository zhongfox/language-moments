# 7.3 接口

不同编程语言中的接口, 可能实现以下某些目的:

1. 限定规格
2. 规格或实现的抽象, 共享具体实现
3. 延迟绑定, 实现多态
4. 某种程度上实现多重继承

#### 接口与多态

> 针对接口编程，不要针对实现编程

> 要面向抽象编程，延迟面向细节编程

接口是对继承思想中相同或者不同继承链中相似行为的再一次抽象. 针对接口编程, 真正的意思是针对超类（抽象类或者接口类型）编程, 延迟绑定.

#### 单一继承和多重继承的矛盾

多重继承是真实世界的实际反映, 不过多重继承实现比较复杂, 实际使用中有以下缺点:

* 结构复杂化
* 优先顺序模糊
* 功能冲突

单一继承实现简单, 不过这限制了对象的真实扩展需求.

不同的语言改善多重继承的实现有不同的实现, 它在Java编程语言中被称为接口(interface), 在Ruby中是Mix-in.

---

### 继承的两层含义

* 规格的继承: 类都有哪些方法
* 实现的继承: 类都有什么数据结构和算法

静态语言中, 这两者的区别很重要. Java就对两者有很明确的区分, 实现的继承用extends来继承父类, 规格的继承用implements来指定接口.

动态语言本来就没有规格继承的概念. 动态语言需要解决的就是实现的多重继承.

---

### 如何解决「实现的多重继承」

静态语言(如Java)的接口只实现了「规格的多重继承」(一个类可以implements多个接口), 还是不允许「实现的多重继承」(一个类只能extends一个类), 因为实现是在类中, 而不是在接口中.

Java 推荐解决「实现的共享」办法是: 在单一继承的前提下, 使用组合模式(Composite)来调用别的类实现的共通功能.

Lisp、Perl和Python都提供了多重继承功能, 这样就不存在单一继承的问题了, 不过要面对上面所说的多继承问题.

---

### Ruby Mixin

Mixin技术按照以下规则来限制多重继承:

* 单一继承
* 第二个以及其他的父类必须是mixin的抽象类

Mixin类有以下特征:

* 抽象类, 不能单独生成实例
* 不能继承普通类

在ruby中, Mixin的单位是module, module有以下特点:

* 包含具体实现
* 不能生成实例
* 不能从普通类继承
* 一个类可以mixin多个module

这样就实现了「实现的多重继承」

总结:[^注1]

| 术语         | 内容                                                                                   |
|--------------|----------------------------------------------------------------------------------------|
| 单一继承     | 只能有一个父类, 单纯但是存在限制                                                       |
| 多重继承     | 多个父类, 解决了单一继承的问题<br>(面向对象需要某种形式的多重继承)<br>但引入了新的问题 |
| 静态语言     | 区分规格继承和实现继承                                                                 |
| 动态语言     | 只有实现继承                                                                           |
| 规格多重继承 | Java 接口可以实现                                                                      |
| 实现多重继承 | Mix-in可以实现                                                                         |
| Ruby Mixin   | module 实现                                                                            |

---

### Go interface

Go是静态类型语言. 每个变量都有且只有一个静态类型, 在编译时就已经确定, 关于类型, 一个重要的分类是接口类型（interface）, 每个接口类型都代表固定的方法集合. 一个接口变量就可以指向任何类型的具体值, 只要这个值实现了该接口类型的所有方法。

在继续接口之前, 我觉得有必要区分一下以下三种场景的类型:

* 1.变量的静态类型:

  每个变量都有且只有一个静态类型, 在编译时就已经确定, 可能是接口类型, type struct, 或基本类型

  * 1.1 不同类型的非接口值无法相互赋值
  * 1.2 接口变量也是静态类型的，它永远只有一个相同的静态类型
  * 1.3 由于接口的静态类型限制，接口变量只能调用接口支持的方法, 指向的数据的额外方法无法调用.

* 2.数据的静态类型:

  存储值的初始化时的静态类型, 通过`reflect.TypeOf(interface{})`获取, 可能是type struct或者基本类型, 不会是接口类型

  一个值在以下情况下可以被变量引用(指向):

  * 2.1 赋值给静态类型相同的变量
  * 2.2 赋给值自身的方法集满足的接口类型变量
  * 2.3 通过类型断言, 转换为其他的类型(普通类型或接口)

* 3.数据的底层类型:

  存储值的底层类型, 通过`reflect.ValueOf(interface{}).Kind()`获取


看看这个例子:

```go
type Animal interface {
  eat()
}

type Bird interface {
  eat()
  fly()
}

type Pig struct{}
type Dove struct{}

func (Pig) eat() {
  fmt.Println("pig eat")
}

func (Dove) eat() {
  fmt.Println("dove eat")
}

func (Dove) fly() {
  fmt.Println("dove fly")
}

var pig = Pig{} // 规则2.1
fmt.Println(reflect.TypeOf(pig), reflect.ValueOf(pig).Kind()) //main.Pig struct
//变量pig的静态类型是Pig, 它指向的值的静态类型也是Pig, 值的底层类型是struct


var dove Bird = Dove{} // 规则2.2
var animal Animal = dove //规则2.2
fmt.Println(reflect.TypeOf(dove), reflect.ValueOf(dove).Kind()) //main.Dove struct
fmt.Println(reflect.TypeOf(animal), reflect.ValueOf(animal).Kind())  //main.Dove struct
// 接口变量dove的静态类型是接口类型Bird, 它指向的值的静态类型是Dove, 值的底层类型是struct
// 接口变量animal的静态类型是接口类型Animal, 它指向的值的静态类型是Dove不变, 值的底层类型是struct
// 简单的animal内心是复杂的Dove

// 规则1.3
animal.fly() //异常: type Animal has no field or method fly
// 虽然animal指向的值实际是Dove, 但是方法调用受限于变量的类型


var bird = animal.(Bird) //规则2.3 断言为接口类型
fmt.Println(reflect.TypeOf(bird), reflect.ValueOf(bird).Kind()) //main.Dove struct
// 接口变量bird的静态类型是Bird, 它指向的值的静态类型是Dove不变, 值的底层类型是struct

var dove2 = animal.(Dove) //规则2.4 断言为普通类型
fmt.Println(reflect.TypeOf(dove2), reflect.ValueOf(dove2).Kind())
// 接口变量bird的静态类型是Bird, 它指向的值的静态类型是Dove不变, 值的底层类型是struct
```

**空接口**

`interface{}`代表一个空集，没有任何方法。由于任何具体的值都有 零或更多个方法，因此类型为interface{} 的变量能够存储任何值

---

### Javascript

Javascript 设计模式中可以实现接口, 但是并不常用.

---

### Lua

Lua 表示不想和你们说话

---

[^注1]: 参考 [松本行弘的程序世界](https://book.douban.com/subject/6756090/) 第二章 「面向对象」


<!--
### 4. Java

todo: Java 说: 前排占坑, 等下班了我来回答

-->
