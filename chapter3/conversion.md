# 3.4 类型转换

> 隐式类型转换造成的问题远大于它带来的好处

|                       | Ruby     | Javascript | Go     | Lua |
|-----------------------|----------|------------|--------|-----|
| 隐式类型转换          |          |            | 不存在 |     |
| 条件/逻辑表达式值类型 | 任意类型 | 任意类型   | bool   |     |




---

### Ruby

---

### Javascript

#### 转换布尔值

* 逻辑判断中会自动进行Boolean转换, 以下6个值会转换为false, 其他值是true

  > false, "", 0, NaN, null, undefined

* 显示布尔转换的函数是`Boolean()`, 实际编码中也经常出现使用双重否定求布尔: `!!someValue`

#### 转换数字

* `Number()`
* `parseInt(string, radix)`
* `parseFloat(string)`

#### 转换为字符串

* `Object.prototype.toString()` 从继承链[TODO] 关系看, 除了null, undefined以外的所有值, 都有`toString`方法

* `String()` 在不确定需要转换的值是否是null或者undefined时, 可以使用String方法, String的转换规则是:

  * 如果有`toString`则调用`toString`
  * 如果是null, 则返回`null`
  * 如果是undefined, 则返回`undefined`

* 实际编码中常常出现使用`+`空字符串进行字符串转换: `0 + ''`

---

### Go

* 不存在隐式转换

  要求显式类型转换: `a := 10; b := byte(a)`

  (除位移外)二元操作表达式必须确保类型一致: `c := a + int(b)`

  加上不支持操作符重载, 所以语句总能表达明确的含义

* 「类似」隐式的类型转换场景, 但其实不是:
  * 常量
  * 别名类型(byte和uint8, rune和int32)
  * 未命名的类型: 具有相同声明的**未命名类型**视为同一类型, 参见(Go语言学笔记)page 35

* 甚至对于`type`关键字创建的底层相同的新类型, 也无法通用


* 对象实现了目标接口的所有方法, 那么该对象可以赋值给此接口类型

  这是面向接口编程的基础

#### 未命名类型有赋值转换规则

* 具有相同声明的2种未命名类型
* 一个是未命名类型, 另一个是和前者基础类型相同的命名类型
* 数据类型相同, 将双向通道赋给单向通道, 且其中一个是未命名类型
* 把nil赋值给slice, map, channel, pointer, function, interface



strconv 不同进制间转换

---

### Lua

