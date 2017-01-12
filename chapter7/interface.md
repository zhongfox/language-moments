# 7.3 接口

> 针对接口编程，不要针对实现编程

> 要面向抽象编程，延迟面向细节编程

接口是对继承思想中相同或者不同继承链中相似行为的再一次抽象. 针对接口编程，真正的意思是针对超类（抽象类或者接口类型）编程，延迟绑定

---

### 1. Ruby

Ruby 提供了mixin Module的功能

---

### 2. Go

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
// TODO: dove 和 animal 指向的值是相同的????
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

### 3. Java

TODO: Java 说: 前排占坑, 等下班了我来回答

---

### 4. Lua

Lua 表示不想和你们说话

---

### 5. Javascript

Javascript 不想和你们说话, 并扔来一个undefined


