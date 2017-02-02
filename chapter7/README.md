# 7. 面向对象

|              | Ruby                                 | Javascript                             | Go                | Lua                                 |
|--------------|--------------------------------------|----------------------------------------|-------------------|-------------------------------------|
| 实现         | Class                                | Prototype                              | Interface         | metatable                           |
| duck typing  | Y                                    | N                                      | Y                 | N                                   |
| 类即对象     | Y                                    | Y<br>函数是对象                        | N                 | Y                                   |
| 对象标识     | `Kernel#object_id`                   | 无                                     | 取地址`&var_name` | N                                   |
| 对象自举     | self                                 | this                                   |                   | self                                |
| 类的超类     | Object                               | Object                                 |                   | nil                                 |
| 自省类名     | `Kernel#class`                       | `Object.prototype.constructor`         |                   | type()                              |
| 直接类型判断 | `Kernel#instance_of?`                | `someObject.constructor === SomeClass` |                   | N                                   |
| 间接类型判断 | `Kernel#kind_of?`<br> `Kernel#is_a?` | `someObject instanceof SomeClass`      |                   | N                                   |
| 实例化       | `Person.new(args)`                   | `new Person(args)`                     |                   | Person.new(args)<br>new是自定义方法 |

<!--
| 实例方法     |                                      |                                        |                   |                                     |
| 实例变量     | `@var_name`                          |                                        |                   |                                     |
| 类方法       | 类的单键方法                         |                                        |                   |                                     |
| 类变量       | `@@var_name`                         |                                        |                   | TODO                                |
-->

Javascript, Go, Lua等语言都没有标准的Class的概念, 不过它们通过其他方式都可以实现OOP, 文中提及的「类」等概念是类比的术语, 比如在javascript中, 「类」其实是构造器函数, 而在Lua中, 「类」是利用table实现的.

---

### 1. Ruby

Ruby 是一门非常纯粹的面向对象的语言, 所有值都是对象, 没有基本类型(primitive)和对象类型的区别


* 实例变量以`@`开头

  引用未赋值的实例变量不会报错, 返回nil, 不过最佳实践是不要依赖这种行为

* 类变量以`@@`开头

  使用前必须先赋值, 否则会报错

  类变量是可以继承的(共享), 在一个继承链上, 只有一个同名的类变量, 看看下面诡异的结果

  ```ruby
  @@v = 1
  class MyClass
    @@v = 2
  end

  @@v #=> 2
  ```

  原因在于顶层环境的当前类是Object, 因此全局定义@@v是属于Object的，MyClass继承了Object，这个继承链上类变量是共享的, 所以上面的代码出现了子类复写类变量.

  最佳实践: 避免使用类变量，尽量使用类的实例变量.

---

### 2. Javascript

#### 构造函数

* 大写字母开头
* 会自动创建对象, 函数中的this指向这个对象
* 自动返回创建的this

构造函数与原型链:

构造函数中对this增加的属性和方法, 是该新对象独占的, 而在构造函数原型链上的属性和方法是所有实例共享的(共享读)

前者有点类似ruby中的单键方法, 后者是实现javascript面向对象的主要技术.

#### prototype

* 每个函数都有一个prototype属性, 对构造函数而言, 用于存储该构造函数所有实例共享的属性和方法

* prototype 有一个自带属性constructor, 该属性指向prototype所属的构造函数

* 每个对象内部有一个指针指向它的构造函数的prototype, 在很多实现中这个指针叫做`__proto__`, 这个指针实现了对象在原型链上的属性查找

* 原型链上的属性会出现在`for in`循环中, 可以使用对象的方法`hasOwnProperty` 过滤掉

* 原型链上的属性是共享给实例的可读属性, 但是实例却不能覆盖原型链上的属性. 如果在实例对原型链同名的属性进行赋值, 将只在该对象本身上起作用.


  ```javascript
  function Person() { }
  Person.prototype.name = "fox";

  let person1 = new Person();
  let person2 = new Person();

  person1.name = "zhong"; //直接赋值, 不会影响原型链
  console.log(person2.name); //fox
  ```

  然后对原型链的可变属性的修改(不是直接赋值), 会影响到原型链, 这也是原型链共享带来的潜在问题
  ```javascript
  Person.prototype.friends = ["Kevin"];
  person1.friends.push("Nash"); //可变对象的修改, 会影响原型链
  console.log(person2.friends); //[ 'Kevin', 'Nash' ]
  ```
#### __proto__

* `__proto__` 是对象原型链上方法查找的直接导向

* `instanceof` 的判断依据也依赖`__proto__`

* 有的实现中, 内部指针`__proto__`还可以直接赋值, 这样可以很直观的看出方法和属性查找的实现:

  ```javascript
  const basicConfig = {
    level: 5
  }

  const config = {
    // 直接指定原型对象
    __proto__: basicConfig
  }

  config.level //5
  ```

  这不就是lua面向对象中元表的实现吗?

#### 避免在原型链上直接共享对象

```javascript
function Person() { }

Person.prototype.info = {
  age: 0
}

var p1 = new Person()
var p2 = new Person()

console.log(p1.info.age) // 0
console.log(p2.info.age) // 0

p1.info.age = 1
console.log(p2.info.age) // 1
```

上例中, 修改p1的age, 导致了p2的age变化.

对象的属性, 最好放到构造函数中作为私有属性, 如需实现读取共享, 可以利用prototype的方法进行封装, 而不是直接暴露共享对象.

通常在prototype上的属性, 都应该是方法, 或者primitive值, 因为primitive值不可变.

---

### 3. Go

Go不是一种典型的OO语言，它在语法上不支持类和继承的概念。
没有继承的Go仍然可以拥有多态行为, Go语言引入了一种新类型—Interface，它在效果上实现了类似于C++的「多态」概念, 虽然与C++的多态在语法上并非完全对等, 但至少在最终实现的效果上, 它有多态的影子.

谈及一个对象的类型(type)时, 我们指的是一个刻画了该对象的行为集合. 换言之, 对象的类型就是它能响应的方法集合. 一个对象的类型与其所属的类有关系, 但是类只是对象类型的一部分.

---

### 4. Lua

Lua 的table很适合实现基于原型的面向对象编程, 在这种编程风格中, 类和实例不是割裂的概念. 在基于原型的面向对象系统中, 你先创建一个实例, 然后再把这个实例复制很多份, 每一份实例可以有个性化的定制. 这样的面向对象系统和传统的基于类的面向对象体系一样强大, 但是更加简单.

#### metatable

设置元表:

 > setmetatable (table, metatable)

获取元表:

> setmetatable (table, metatable)

* table 和userdata 的实例可以有各自不同的metatable, 其他类型的值共享其类型所属的单一metatable

* `{}`创建的table默认没有metatable

* lua 代码中只能设置table的metatable

* 字符串默认设置了metatable, 其他类型默认没有


#### metamethod

* 对于二元操作, Lua 选择 metamethod 的原则:

  * 算术运算: 不关心混合类型的, 如果第一个参数存在带有`__add` 域的 metatable,Lua使用它作为 metamethod, 和第二个参数无关; 否则第二个参数存在带有`__add` 域的 metatable, Lua 使用它作为 metamethod 否则报错

  * 关系元算: 不支持混合类型运算, 如果你试图比较一个字符串和一个数字, Lua 将抛出错误.相似的,如果你试图比较两个带有不同 metamethods 的对象, Lua 也将抛出错误

常见的metamethod:

* 算术元方法: `__add __mul __sub __div __unm __mod __pow`

* 连接操作: `__concat`

* 关系元方法: `__eq __lt __le`

* tostring: `__tostring(this)` 对print有效

* `__metatable` 设置此值, 用于禁止元表的读写, 读取将获得该设置值, 写入将抛出异常`cannot change protected metatable`

* `__index(table, key)` 可以是一个函数或者table, 当读取table的key不存在时, 调用此方法/table, 此metamethod提供了一种廉价而简单的实现单继承的方式.

* `__newindex(table, key, value)` 可以是一个函数或者table, 当设置table的key不存在时, 调用此方法/table


可以跳过metamethod:

* `rawset(table, key, value)`
*  `rawget(table, key)` 可以跳过metamethod

#### 利用`__index`实现继承

```lua
-- create a namespace
Window = {}

-- create the prototype with default values
Window.prototype = { x=0, y=0, width=100, height=100 }

-- create a metatable
Window.mt = {}

-- declare the constructor function
function Window.new (o)
  setmetatable(o, Window.mt)
  return o
end

Window.mt.__index = function (table, key)
  return Window.prototype[key]
end

w = Window.new{x=10, y=20}
print(w.width) --> 100
```

`__index`还可以直接设置table: `Window.mt.__index = { x=0, y=0, width=100, height=100 }`

以上lua代码几乎就是实现了javascript原型链集成, 一个明显的区别是`setmetatable(o, Window.mt)` 在javascript中是语言实现的, lua 需要编码者实现的

`__newindex` metamethod 用来对表更新, 当你给表的一个缺少的域赋值,解释器就会查找__newindex metamethod, 如果存在则调用这个函数而不进行赋值操作.

像`__index` 一样,如果 metamethod 是一个表, 解释器对指定的那个表, 而不是原始的表进行赋值操作.

对比javascript ES6的Proxy, `__index` 类似 Proxy 中定义的set, 而`__newindex`对应 Proxy get.

---

[comment]: <> (面向对象, 主要是找实例方法/属性)
