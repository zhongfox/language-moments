# 3.2 数据类型

|               | Ruby                   | Javascript                | Go     | Lua                    |
|---------------|------------------------|---------------------------|--------|------------------------|
| Value Content | 立即值<br>引用类型对象 | primitive<br>引用类型对象 | 值类型 | 值类型<br>引用类型对象 |

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

Ruby 是一门非常纯粹的面向对象的语言, 所有值都是对象, 没有基本类型(primitive)和对象类型的区别, 而且所有对象都是通过引用操作.

不过在Ruby中, **Fixnum**和**Symbol**对象实际上是立即值(immediate values), 而不是引用, 它们没有可变方法, 所有它们是**不可变**的, 同时立即值也不能有单键方法.

#### 类型判断:

* 获取类型: `Object#class`
* 直接类型判断:
  * `Kernel#instance_of?`
  * `Module#===` 可以用于任一class上: `SomeClass === obj`
* 间接类型判断:
  * `Kernel#kind_of?`
  * `Kernel#is_a?`

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

javascript 的primitive类型是不可变的值, Object 是可变的. 有很多办法可以吧primitive转换为Object, 其中最通用的就是`Object`方法:

```javascript
if (Object(result) === result) {
  // result是一个对象......
}
```

#### primitive包装类型: String, Number, Boolean

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

##### typeof

`typeof`操作符, 而不是函数, 有以下7种返回值, 要注意和以上类型并非一一对应:

* 「undefined」: 如果值是undefined, 或者是根本没有声明的变量
* 「boolean」: 如果值是true或者false(Boolean类型)
* 「string」: 如果值是字符串(String类型)
* 「number」: 如果值是数字(Number类型)
* 「object」: 如果值是Object, 但不是Function, 或者值是null
* 「function」: 如果值是函数(Function类型)
* 「symbol」: 如果值是符号类型(ES6 Symbol类型)

`typeof` 对primitive类型数据很有用, 但是对于Object想要进一步检测类型, 需要用到`instanceof`

##### instanceof

`instanceof` 同样是操作符, `obj instanceof SomeClass` 是用于判断obj是否是SomeClass的实例, 如果obj是SomeClass的直接实例或者间接实例, 都返回true

* `引用值 instanceof Object` 始终是true
* `primitive值 instanceof AnyClass` 始终是false

但是要注意primitive值和包装类型对象是不一样的:

```javascript
> 'hi' instanceof String
false
> (new String('hi')) instanceof String
true
```

---

### 3. Go

* 原始类型: (不可变)
  * 整数: int, unit, int16, uint16, int32, uint32, int64, uint64
  * 浮点数: float32, float64
  * 复数: complex64, complex128
  * 字符: byte(uint8的别名), rune(int32的别名)
  * bool
  * string
  * uintptr
  * pointer
* 引用类型: (包指向底层数据结构的指针)
  * map
  * slice
  * channel
  * function
  * interface
* 复合类型:
  * array: 数组长度是类型的一部分, `[3]int` 和 `[4]int` 不算同一个类型
  * struct

#### 底层类型

每一个类型都有一个底层类型(underlying type)

#### 未命名类型

与有明确标识符的bool, int, string等相比, array, slice, map, channel等类型与具体的元素类型或长度相关, 叫做**未命名类型**(unnamed type), 使用`type`可以使其新增为**命名类型**(named type)

如 int 是命名类型, `*int` `[]int` 是未命名类型

未命名类型的一个重要属性就是用同样类型的未命名类型声明的变量拥有相同的类型，而两个不同的命名类型，即使底层的类型相同，它们的类型也是不同的。

可以这样理解: 未命名类型其实不是完整的类型, 或者不是类型, 只有存储的元素长度或者类型确定, 才叫做真正的类型

具有相同声明的未命名类型, 被认为是同一类型

命名类型可以定义自己的函数， 而未命名类型确不行

#### 空struct

空struct不占空间, 参考: [The empty struct](https://dave.cheney.net/2014/03/25/the-empty-struct)

---

### 4. Lua

* 值类型:
  * nil 只有一个值 nil
  * boolean: true, false
  * number: 用于表示双精度浮点数, Lua中没有整数
  * string: 字符序列, 使用8位字节, 字符串可以包括任何数字字符

* 引用类型:
  * function: 第一类值
  * userdata: 用于存储C 数据, 预定于操作只有赋值和相等比较
  * thread: 表示执行的独立线路，用于执行协同程序
  * table: Lua 中的表(table)其实是一个关联数组(associative arrays), 数组的索引可以是数字或者是字符串

> Tables, functions, threads, and (full) userdata values are objects: variables do not actually contain these values, only references to them. Assignment, parameter passing, and function returns always manipulate references to such values; these operations do not imply any kind of copy.

Tables, functions, threads, userdata 是引用类型, 存储、传递均是对象引用.

#### 类型判断

`type (v)`

返回值是一个字符串, 有8个可能值:  "nil", "number", "string", "boolean", "table", "function", "thread", 和 "userdata".

---

参考资料:

* <http://www.lua.org/manual/5.3/manual.html>

<!--
Lua userdata

### Java

* primitive(变量存的是变量实际的值): 4整2浮1 char 1 boolean
* 引用类型

类似javascript 也有基本包装类型

-->
