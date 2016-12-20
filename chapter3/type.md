# 3.1 类型

|          | Ruby     | Javascript | Go       | Lua |
|----------|----------|------------|----------|-----|
| 类型系统 | 强类型   | 弱类型     | 强类型   |     |
|          | 动态类型 | 动态类型   | 静态类型 |     |
|          |          |            |          |     |

---

### 1. Ruby

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

#### 立即值

Ruby 是一门非常纯粹的面向对象的语言, 所有值都是对象, 没有基本类型(primitive)和对象类型的区别, 而且所有对象都是通过引用操作

不过在Ruby中, **Fixnum**和**Symbol**对象实际上是立即值(immediate values), 而不是引用, 它们没有可变方法, 所有它们是**不可变**的, 同时立即值也不能有单键方法

#### 类型判断:

* 获取类型: `Object#class`
* 直接类型判断: `Kernel#instance_of?`
* 间接类型判断:`Kernel#kind_of?` `Kernel#is_a?`

* `Class ===` TODO

---

### 2. Javascript

* primitive:

  * undefined: 唯一的值是`undefined`
  * null
  * boolean
  * number
  * string

  * ES6增加一个: Symbol

* 引用类型: Object(Array, Function, Date, RegExp, ...)

* 基本包装类型: String, Number, Boolean

  基本包装类型属于引用类型

  设计原本: primitive值不是对象, 应该是没有方法调用的, 不过为了使用方便, 在访问的**读取**模式下, 后台自动创建了primitive值对应的基本包装引用类型, 使其可以使用方法, 在使用后立即销毁:

  ```javascript
  var s1 = "hello javascript";
  var s2 = s1.substring(2);

  //后台完成类似这样的工作:
  var s1 = new String("hello javascript");//这样s1就有方法可用
  var s2 = s1.substring(2);
  s1 = null;
  ```

  因为自动销毁, 对自动基本包装类型设置属性是无效的:

  ```javascript
  var s1 = "hello javascript";
  s1.color = "red"; //随之销毁
  console.log(s1.color); //undefined
  ```

#### 类型判断

* `typeof` 操作符, 而不是函数, 有以下7种返回值, 要注意和以上类型并非一一对应:

  * 「undefined」: 如果值是undefined, 或者是根本没有声明的变量
  * 「boolean」: 如果值是true或者false(Boolean类型)
  * 「string」: 如果值是字符串(String类型)
  * 「number」: 如果值是数字(Number类型)
  * 「object」: 如果值是Object, 但不是Function, 或者值是null
  * 「function」: 如果值是函数(Function类型)
  * 「symbol」: 如果值是符号类型(ES6 Symbol类型)

  `typeof` 对primitive类型数据很有用, 但是对于Object想要进一步检测类型, 需要用到`instanceof`

* `instanceof`操作符

  `obj instanceof SomeClass` 是用于判断obj是否是SomeClass的实例, 如果obj是SomeClass的直接实例或者间接实例, 都返回true

  * `引用值 instanceof Object` 始终是true
  * `primitive值 instanceof AnyClass` 始终是false

  但是要注意primitive值和包装类型对象是不一样的:

  ```javascript
  > 'hi' instanceof String
  false
  > (new String('hi')) instanceof String
  true
  ```

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

* 不严格比较`==`, 会报告两者相等: `undefined == null #true`

* typeof

  * `typeof undefined => 'undefined'`
  * `typeof null => 'object'`, null 在语义上表示不是一个对象, null表示一个空对象指针, 这也是为什么null的typeof返回「object」.

    如果需要判断一个值是不是null, 可以直接用`=== null` 进行判断

---

### 3. Go

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

#### 未命名类型

与有明确标识符的bool, int, string等相比, array, slice, map, channel等类型与具体的元素类型或长度相关, 叫做**未命名类型**(unnamed type), 使用`type`可以使其新增为**命名类型**(named type)

可以这样理解: 未命名类型其实不是完整的类型, 或者不是类型, 只有存储的元素长度或者类型确定, 才叫做真正的类型

具有相同声明的未命名类型, 被认为是同一类型




---

### 4. Lua

nil, boolean, number, string, userdata, function, thread, table


---

<!--
TODO

### Java

* primitive(变量存的是变量实际的值): 4整2浮1 char 1 boolean
* 引用类型

-->
