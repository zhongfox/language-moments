# 3.5 类型转换

> 隐式类型转换造成的问题远大于它带来的好处

|                       | Ruby                         | Javascript                | Go                | Lua  |
|-----------------------|------------------------------|---------------------------|-------------------|------|
| 隐式类型转换          | 存在                         | 存在                      | 不存在            |      |
| 条件/逻辑表达式值类型 | 任意类型                     | 任意类型                  | bool              | TODO |
| 如何转换为bool        | `!!var`<br>自行判断 `x == 0` | `!!x`<br>自行判断`x == 0` | 自行判断 `x != 0` | TODO |

需要注意, Ruby中`!!x` 结果为false的情况只有x等于nil或者false, 而javascript中有[6种情况](TODO), 而Go语言中只有准确的bool类型才能使用`!`操作, 其他类型数据无法通过`!`进行bool转换.

---

### 1. Ruby

#### 显式的类型转换

Ruby 2.4 中以`to_` 开头的方法名有30多个, 大都在多个类中有不同的实现.

* 转换为字符串 `#to_s`: 任何ruby对象都有`to_s`方法, 因为该方法定义在`Kernel#to_s`, 很多类中都复写了该方法, 在Ruby 2.4中有98个实现, 包括 Integer, Array, Float, Hash等等
* 转换为整数 `#to_i`: 在Ruby 2.4中有18个类实现了此方法, 包括 String, Time, Float, ARGFF等
* 转换为浮点数 `#to_f`
* 转换为浮点数 `#to_a`
* 转为为Hash `#to_h`
* 转换为Symbol: `String#to_sym`

Kernel还定义了四个和类名相同的转换函数: `Kernel#Array` `Kernel#Float` `Kernel#Integer` `Kernel#String`, 不过我觉得这样做(和类名相同)会让人感到迷惑, 至少我曾经遇到过.

#### 隐式类型转换

Ruby的理念是让使用者happy, 在很多地方使用了隐式类型转换或者自动调用了转换方法, 使得使用者不必考虑这些繁琐细节, 通常来说这些隐式或者自动转换都是安全的, 不过知悉这些转换的存在, 将有助于我们写出更健壮的代码.

* 字符串内插会自动调用`to_s`方法: `"now is #{Time.now}"`
* `String#+`会自动调用右值的`to_str`

  ```ruby
  class A
    def to_str
      self.object_id.to_s
    end
  end

  s = 'test'
  a = A.new
  puts s + a #test2156356600
  ```

  类似的隐式转换方法还有`to_int` `to_ary` `to_hash` 不过在内建类中, 实现并不广泛, 使用也不多

*  String, Array, Hash 的实例方法相等运算`==`也类似一种week类型转换, 如果左右值是同一类型则进行常规比较, 否则, 会检测右值是否定义了`to_str` `to_ary` `to_hash`, 如果存在, 则使用方法调用后的结果再进行比较.

---

### 2. Javascript

#### 显式类型转换

##### 转换布尔值

* 显示布尔转换的函数是`Boolean()`, 实际编码中也经常出现使用双重否定求布尔: `!!someValue`

* 逻辑判断中会自动进行Boolean转换, 以下6个值会转换为false, 其他值是true

  > false, "", 0, NaN, null, undefined

##### 转换数字

* `Number(anyValue)`
* `parseInt(string, radix)`
* `parseFloat(string)`

##### 转换为字符串

* `Object.prototype.toString()` 从[继承链](TODO) 关系看, 除了null, undefined以外的所有值, 都有`toString`方法

* `String()` 在不确定需要转换的值是否是null或者undefined时, 可以使用String方法, String的转换规则是:

  * 如果有`toString`则调用`toString`
  * 如果是null, 则返回`null`
  * 如果是undefined, 则返回`undefined`

* 实际编码中常常出现使用`+`空字符串进行字符串转换: `0 + ''`

#### 隐式类型转换

`+` 操作, 如果其中有一个操作数是string, 那么另一个将自动被调用`toString()`方法转换为字符串, 然后进行字符串相加

> 6 + '6' //'66'

`+` 操作, 如果没有string操作数, 但其中有Object, number, undefined或者null, 将对其调用`String()`方法转换为字符串, 然后按照上面的流程进行字符串相加

> null + '6' //'null6'

相等比较`==`的操作数如果不是相同类型, 将会被自动转换为相同类型再进行比较, 转换规则比较复杂, 建议尽量避免使用

> 4 == '4' //true  
> null == undefined //true

**最佳实践**:

* 不要依赖隐式类型转换
* 不要使用`==`, `!=`, 一直使用 `===` `!==`

---

### 3. Go

#### 不存在隐式转换

要求显式类型转换: `a := 10; b := byte(a)`

(除位移外)二元操作表达式必须确保类型一致: `c := a + int(b)`

加上不支持操作符重载, 所以语句总能表达明确的含义

#### 转换为布尔值

在动态类型(TODO)语言, 经常会使用数字, 字符串等去逻辑判断, 因为在这些语言中通常会支持任一值自动转换布尔值, 不过在Go中没有自动转换, 甚至也不支持显示布尔转换:

```go
i1 := 1
i2 := 0
fmt.Printf("%t %t\n", bool(i1), bool(i2)) //报错
```

只能自行使用判断语句如`n > 0`

#### type 定义的类型是全新类型

对于`type`关键字创建的底层相同的新类型, TODO 补充例子

#### 「类似」隐式的类型转换场景

以下场景看起来像隐式的类型转景, 但其实不是:

* 常量: 无显示声明的常量, 作为操作数时, 可以自动转换
* 别名类型(byte和uint8, rune和int32)
* 未命名的类型: 具有相同声明的**未命名类型**视为同一类型, 参见(Go语言学笔记)page 35
* 对象实现了目标接口的所有方法, 那么该对象可以赋值给此接口类型, 这是面向接口编程的基础

#### 未命名类型有赋值转换规则

* 具有相同声明的2种未命名类型
* 一个是未命名类型, 另一个是和前者基础类型相同的命名类型
* 数据类型相同, 将双向通道赋给单向通道, 且其中一个是未命名类型
* 把nil赋值给slice, map, channel, pointer, function, interface

TODO strconv 不同进制间转换

---

### 4. Lua

