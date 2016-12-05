# 3.1 类型

### Ruby

* Object (一切皆为对象)
  * Numeric
  * String
  * Array
  * Hash
  * Range
  * Symbol
  * TrueClass
  * FalseClass
  * NilClass
  * ...

#### 类型判断:

`Object#class`

---

### Javascript

* primitive:

  * Undefined: 唯一的值是`undefined`
  * Null
  * Boolean
  * Number
  * String

  * ES6增加一个: Symbol

* 引用类型: Object(Array, Function, ...)

#### 类型判断

* `typeof` 操作符, 而不是函数, 有以下7种返回值, 要注意和以上类型并非一一对应:

  * 「undefined」: 如果值是undefined, 或者是根本没有声明的变量
  * 「boolean」: 如果值是true或者false(Boolean类型)
  * 「string」: 如果值是字符串(String类型)
  * 「number」: 如果值是数字(Number类型)
  * 「object」: 如果值是Object, 但不是Function, 或者值是null
  * 「function」: 如果值是函数(Function类型)
  * 「symbol」: 如果值是符号类型(ES6 Symbol类型)

#### undefined 和 null

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

* 不严格比较`==`, 会报告两者相等:

  `undefined == null` true

* typeof

  * `typeof undefined => 'undefined'`
  * `typeof null => 'object'`, null 在语义上表示不是一个对象, null表示一个空对象指针, 这也是为什么null的typeof返回「object」.

    如果需要判断一个值是不是null, 可以直接用`=== null` 进行判断

---

### Lua

nil, boolean, number, string, userdata, function, thread, table

---

### Java

* primitive(变量存的是变量实际的值): 4整2浮1 char 1 boolean
* 引用类型

---

### Go

* 整数: int, unit, int16, uint16, int32, uint32, int64, uint64
* 浮点数: float32, float64
* 复数: complex64, complex128
* 字符: byte(uint8的别名), rune(int32的别名)
* bool
* string
* error
* function
* uintptr
* 复合类型:
  * pointer, array, struct, interface
  * 引用类型:
    * map
    * slice
    * channel


