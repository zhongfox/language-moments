# 3.12 空值

> 空值既简单又复杂

|                 | Ruby   | Javascript | Javascript | Go       | Lua    |
|-----------------|--------|------------|------------|----------|--------|
| 空值            | nil    | undefined  | null       | nil      | nil    |
| 预定义值/关键字 | 关键字 | 预定义值   | 关键字     | 预定义值 | 关键字 |

 关键字不允许作为赋值的左值, 而预定义值是可以被重新赋值的.

---

### 1. Ruby 的 nil

Ruby 中一切皆为对象, nil也不例外, nil是NilClass唯一的实例:

> nil.class # NilClass

nil 和布尔值false不等:

> nil == false #false

不过当Ruby需要一个布尔值是, nil表现和false一致, 都是为假

对象判断是否是真正的nil有2种方式:

> o == nil

或者

> o.nil?

---

### 2. Javascript 的 undefined 和 null

* null 和 undefined 是语言设计上的历史包袱, 时至今日, null和undefined基本是同义的，使用上有一些细微的差别:

  null:

  >（1） 作为函数的参数，表示该函数的参数不是对象。  
  >（2） 作为对象原型链的终点。

  undefined:

  >（1）变量被声明了，但没有赋值时，就等于undefined。  
  >（2) 调用函数时，应该提供的参数没有提供，该参数等于undefined。  
  >（3）对象没有赋值的属性，该属性的值为undefined。  
  >（4）函数没有返回值时，默认返回undefined


* undefined 并不是一个字面量, 不是保留字, 只是一个值, 因此undefined其实可以作为变量进行赋值, 这是引起某些bug的潜在原因

  在某些jQuery代码中有这种写法:

  ```javascript
  ;(function(window,undefined){

  })(window)
  ```
  其中一个原因就是防止全局的undefined变量被修改成其他值

* null 是一个字面量, 是个保留字,你不能把null作为变量名, 不能被赋值

* undefined和null在逻辑运算语句中，都会被自动转为false

* 不严格比较`==`, 会报告两者相等: `undefined == null #true`

* typeof

  * `typeof undefined => 'undefined'`
  * `typeof null => 'object'`, null 在语义上表示不是一个对象, null表示一个空对象指针, 这也是为什么null的typeof返回「object」.

    如果需要判断一个值是不是null, 可以直接用`=== null` 进行判断

---

### 3. Go 中的nil

> nil is a predeclared identifier representing the zero value for a pointer, channel, func, interface, map, or slice type

* 无类型(untyped)的预定义值
* 用于代表pointer, channel, func, interface, map, slice的零值, 因此也只能用于这6种类型

nil 是无类型的预定义值, 而不是关键字, 预定义值在语法上可以被重新赋值:

> nil := 123 //ok

在golang中，nil只能赋值给pointer, channel, func, interface, map, slice类型的变量. 如果未遵循这个规则，则会引发panic

以上六种类型nil值的含义如下:

##### pointer

空指针, 指向nil, 亦可以说是什么都没有

nil指针的变量, 仍然可以调用其类型方法集中的方法, 以下代码可以执行成功.

```go
package main

import (
  "fmt"
)

type littleGirl struct {
  Name string
  Age  int
}

func (this *littleGirl) changeName(name string) {
  // this.Name = name // nil 指针不能进行字段操作
  fmt.Println(name)
}
func main() {
  little := &littleGirl{Name: "Rose", Age: 1}
  little = nil
  little.changeName("fox")
  fmt.Println(little)
}
```

##### slice

一个为nil的slice，除了不能索引外，其他的操作都是可以的.

形如`s := make([]byte,5)` 的切片内部构造如下:

>[]byte  
>ptr *elem //ptr指向一个底层数组它有五个元素[0,0,0,0,0]  
>len 0  
>cap 0

`var s []byte`的切片将初始化为零值nil, 内部结构如下:

>[]byte  
>ptr nil //空指针  
>len 0  
>cap 0

##### map, channel, func

非零值的map, channel, func, 内部有个ptr 指向具体内部实现, 零值的map, channel, func内部指针是空指针:

>ptr nil

#### map

对于nil的map，可以简单把它看成是一个只读的map，不能进行写操作，否则就会panic

#### channel

关闭一个nil的channel会导致程序panic

nil的channel是永远阻塞的

#### interface

interface并不是一个指针，它的底层实现由两部分组成，一个是类型，一个值，也就是类似于：(Type, Value)

只有当类型和值都是nil的时候，才等于nil

`(type, value)` 输出结果是 `<nil> <invalid reflect.Value>`, 可以看到是无类型(untyped)

---

### 4. Lua 的nil

给变量赋值nil将回收此变量内存, 删除变量, 以及删除map的键值对, 都可以通过赋值nil实现.

nil只等于自己本身

---

参考资料: [Go: Understanding Nil](https://speakerdeck.com/campoy/understanding-nil)
