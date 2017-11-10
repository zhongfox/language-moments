# 3.7 比较

|          | Ruby                       | Javascript | Go   | Lua  |
|----------|----------------------------|------------|------|------|
| 相等比较 | `equal?` `==` `eql?` `===` | `==` `===` | `==` | `==` |
| 不等比较 |  使用unless                | `!=` `!==` | `!=` | `~=` |

---

### 1. Ruby

Ruby 提供了多的出奇的对象相等性比较选择, 了解它们的机制非常必要, 这样才能在适当的时候选择正确的用法. 主要约定如下;

* `equal?` 同一对象
* `==` 合理相等性, 允许类型转换
* `eql?` 不允许类型转换的相等
* `===` 条件性相等

需要注意的是, 以上都是方法, 因此有的场景颠倒左右值, 可能得到不同的结果.

#### equal?

`BasicObject#equal?`比较两个值是否引用了同一对象, 按照惯例, 永远不要改写此方法:

```ruby
a = 'zhong'
b = 'zhong'
a.equal? b #false
```

也可以直接比较对象的`object_id` 实现`equal?`:

```ruby
a.object_id == b.object_id
```

#### ==

`BasicObject#==`在BasicObject中是`equal?`的同义词, 大多数类重新定义了此方法, 目的在于实现一个合理的相等性比较, 举例:

* String: 比较长度和内容. 如果右值不是字符串, 在比较前, 还会尝试调用`to_str`进行转换.
* Array: 两个比较数组如果有相同数量的元素, 且对应位置上的元素通过`==`也为true, 那么整体返回true.
* Hash: 两个比较的Hash如果有相同数量的键值对, 对应键通过`eql?`比较, 对应值通过`==`, 如果都返回true, 那么整体返回true.
* Numeric: 会尝试简单的类型转换, `1 == 1.0` 返回true.

### eql?

`Object#eql?` 是`equal?`的同义词, 部分类重新定义此方法, 实现严格版的`==`, 即不允许类型转换的相等比较.

`Hash#eql?`用于检测两个hash键是否相等

### ===

`Object#===`在Object中默认调用了`==`, 部分类重新定义此方法, 用于实现条件性相等, 举例:

* `Range#===` 测试右值是否在该range范围内
* `Regexp#===` 测试右值是否可以匹配该正则表达式
* `Module#===` 测试右值是否是该Class的直接或间接实例
* `Symbol#===` 测试右值是否是该Symbol的字符串形式

另外when case中的相等比较用的是`===`

---

### 2. Javascript

要点:

* `==` `!=` 允许类型隐式转换, 规则比较难记, 不推荐使用, 干脆不要记
* NaN 和任何值都不相等, 包括它自己, 判断是否是NaN需要用函数`isNaN(testValue)`
* 两个对象的比较是比较是否是同一对象
* `===` `!==` 不会进行类型转换

比较详解: [JavaScript中比较运算的详细总结](http://gcidea.info/2017/01/12/js-compare/)

---

### 3. Go

Go是强类型语言, 只有同一类型下的值才能比较.

在 Go 中, 两个不同可比较类型的两个值只能在一个值可以隐式转换为另一种类型的情况下进行比较。具体来说, 有两个案例两个不同的值可以比较:

* 两个值之一的类型是另一个的基础类型。
* 两个值之一的类型实现了另一个值的类型 (必须是接口类型)。

nil值比较并没有脱离上述规则。

```go
type IntPtr *int
fmt.Println(IntPtr(nil) == (*int)(nil))         //true
fmt.Println((interface{})(nil) == (*int)(nil))  //false
```

#### 不可比类型

map、slice和函数类型是不可比较类型，它们有一个别称为不可比拟的类型，所以比较它们的nil亦是非法的

```go
var v1 []int = nil
var v2 []int = nil
fmt.Println(v1 == v2) // slice can only be compared to nil
fmt.Println((map[string]int)(nil) == (map[string]int)(nil)) //map can only be compared to nil
fmt.Println((func())(nil) == (func())(nil)) //func can only be compared to nil
```

不可比的类型的值缺是可以与「纯nil」进行比较

#### 不可比类型 Function/Slice/Map

函数, Slice, Map 只能判断是否为nil, 不支持其他比较操作

#### Array

另外, 对于数组而言,  如果元素支持`==` `!=` 数组也支持, 如果两个数组的对应元素比较都相等, 则数组比较为相等, 否则为不等. 如果元素不支持比较, 则数组比较会报错.

#### Struct

只有在所有字段类型全部支持时, 才可以做相等操作

#### nil 的比较

nil 是pointer, channel, func, interface, map, slice 六种类型的零值, 因此nil只能和以上6种类型进行比较:

```go
type T struct{ }
var t = T{}

//不能和Struct比较
if t == nil { //Cannot convert nil to type T
}

//但是可以和指针比较
if &t == nil {
}
```

nil 和以上6种类型的零值相等:

```go
var stringer fmt.Stringer //interface
fmt.Println(stringer == nil) //true

var byteSlice []byte
fmt.Println(byteSlice == nil) //true

var bird Bird  //子接口
var animal Animal = b //父接口
fmt.Println(animal == nil) //true
```

除此之外的值和nil不等.

```go
var person *Person     // struct
var personStringer fmt.Stringer = person // Stringer(*Person, nil)
fmt.Println( personStringer == nil)  // false
```

不过注意等于nil的两个值, 不一定能相互比较, 原因是「同一类型下的值才能比较」

```go
var m map[int]int
fmt.Println(m == nil) //true
var i interface{}
fmt.Println(i == nil) //true
fmt.Println(m == i)  //异常: operator == not defined on map
```

对于interface和nil比较, 还可以如下理解:

interface 存储的是(type, value)对, nil之和(nil, nil)相等, 和任何(类型, nil)不等, 以上示例中stringer和animal都是(nil, nil), 但是personStringer是(*Person, nil)

---

### 4. Lua

类型不同的数据可以比较, 不过比较结果始终是false

> 10 == '10' -- false

---
