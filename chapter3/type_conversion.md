# 3.4 类型转换

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

### Lua

---

### Java

---

### Go

不存在隐式转换, 要求显式类型转换: `a := 10; b := byte(a)`

(除位移外)二元操作表达式必须确保类型一致: `c := a + int(b)`

具有相同声明的**未命名类型**视为同一类型, 参见(Go语言学笔记)page 35

未命名类型有赋值转换规则, 参见(Go语言学笔记)page 36

