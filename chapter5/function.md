# 5.1 函数比较

> 函数对任何语言来说都是一个核心的概念

|                        | Ruby                           | Javascript                                        | Go                                                      | Lua                             |
|------------------------|--------------------------------|---------------------------------------------------|---------------------------------------------------------|---------------------------------|
|                        | def play_ball(参数)<br>end     | function playBall(参数) {<br>}                    | func (本体)playBall(入参)(出参){<br>}                   | function play_ball(参数)<br>end |
| First Class Object     | N                              | Y                                                 | Y                                                       | Y                               |
| 匿名函数与闭包         | N<br>lambda, proc提供闭包功能  | Y                                                 | Y                                                       | Y                               |
| 参数传递               | 值拷贝                         | 值拷贝                                            | 值拷贝                                                  | 值拷贝                          |
| 参数默认值             | Y                              | ES5: N<br>ES6: Y                                  | N                                                       | N                               |
| 实参和形参数量必须一致 | 必须                           | 不必须                                            | 必须                                                    | 不必须                          |
| 不定长变参             | Y<br>`def max(first, *rest)`   | ES5: arguments<br>ES6: `function(...arrayInside)` | Y<br>`func (slice_inside ...int)`                       | Y<br>`function select (n, ...)` |
| 命名参数               | Y<br>`def foo(bar: 'default')` | N<br>使用object代替                               | N<br>使用struct代替                                     | N<br>使用table代替              |
| 多返回值               | Y<br>多返回值聚合到一个数组中  | N                                                 | Y<br>不能用数组切片接收<br>可用于函数实参, 或者赋值右值 | Y                               |
| 默认返回值             | 最后一个表达式的值             | undefined<br>>ES6 箭头函数某些情况会自动return    | N                                                       | N?nil?                          |
| 函数重载               | N                              | N                                                 | N                                                       | N                               |
| 调用函数时省略括号     | 允许                           | N                                                 | N                                                       | 当唯一的参数是string或table时   |
| 尾调用优化             | 默认未开启, 需要手动开启       | ES6 + strict 才会开启                             | 不支持尾调用. 要用goto来实现其效果                      | Y                               |

---

### 1. Ruby

* 严格来说, Ruby只有method, 没有function.
* 在Ruby中一切皆为对象, 不过方法和代码块并不是可操作的对象.
* 除了常规的方法, Ruby还提供了proc, lambda以及Method对象, 他们的行为更像函数, 而非方法

#### 可调用对象

Ruby 提供了三种可调用对象: Proc对象, lambda, method对象. 可调用对象的一个使用是实现延迟执行, 这个在rails的scope中经常用到（scope中的lambda的Time.now不会在class加载的时候固化）

`Proc.new => proc`

`proc {block} => proc`

`lambda {block} => lambda`

`->(arg) {block} => lambda`

`Object#method(sym) → method`

方法中获得传入代码块转化的对象是Proc对象

proc 和 lambda 的区别:

* proc中的return会从**定义**proc的作用域返回，lambda会更合理的从lambda中返回.
* proc自适应传递的参数个数，lambda严格要求参数个数.

---

### 2. Javascript

#### 函数的属性和方法

* 属性 length: 形参个数
* 属性 prototype: 见[面向对象](/chapter7/#prototype)
* 方法apply
* 方法call

#### arguments/callee/caller

* arguments

  和大多数语言不同, Javascript函数不介意传递进来多少参数, 没有传递的参数自动赋予undefined

  原因在于Javascript中参数在内部是用一个数组来表示, 函数接收到的始终都是这个数组, 而不关心数组里有哪些参数.

  这个数组就是arguments, 它是一个伪数组, 不是Array的实例, 但是可以使用`[]`进行索引, 也有length属性

* arguments.callee

  返回正被执行的 Function 对象, callee属性是arguments 对象的一个成员, 它表示对函数对象本身的引用, 这有利于匿名函数的递归或者保证函数的封装性

* 补充caller: `someFunction.caller`

  caller属性只有在函数执行时才有定义

  返回一个对函数的引用，该函数调用了当前函数, 如果函数是由顶层调用的，那么 caller包含的就是 null

  不过在node环境中, 顶层函数是由包装函数调用的:

  ```javascript
  //test.js
  function a() {
    console.log(arguments.callee.caller.toString())
  }
  a()

  // node test.js 输出:
  function (exports, require, module, __filename, __dirname) { function a() {
    console.log(arguments.callee.caller.toString())
  }
  a()

  }
  ```

* 在函数中, arguments.callee 就是该函数本身, 因此在函数someFunction执行时: `arguments.callee.caller === someFunction.caller`

#### 默认参数

* ES5:

  ```javascript
  function log(x, y) {
    y = y || 'World';
    console.log(x, y);
  }
  ```

  因为js中有6个值都可以转换为布尔false, 因此更严格的实现应该是:

  ```javascript
  function log(x, y) {
  if (typeof y === 'undefined') {
    y = 'World';
  }
  ```

* ES6:

  ```javascript
  function log(x, y = 'World') {
    console.log(x, y);
  }
  ```
  ES6实现了通用的默认参数, 阅读代码的人，可以立刻意识到哪些参数是可以省略的，不用查看函数体或文档. 函数体也更加简洁.

#### 返回值

* 没有执行return的函数返回undefined
* return 语句可以不带任何值, 这样将返回undefined
* 最佳实践: 一个函数要么始终有返回值, 要么始终没有返回值, 如果函数有时有返回值有时没有, 会给调试带来不便

#### 没有重载

用相同的函数名重复定义的函数, 后面将覆盖前面的, 不论其参数列表是否相同

#### 函数声明与函数表达式

解析器在向执行环境中加载数据时, 对函数声明和函数表达式并非一视同仁, 解析器会优先读取函数声明, 并使其在执行任何代码之前可用; 至于函数表达式, 则必须等到解析器执行到它所在的代码行才会真正被解释执行:

```javascript
//函数声明, 执行正常
console.log(sum(1, 2));
function sum(x, y) {
  return x + y;
}

//函数表达式, 执行错误
console.log(sum(1, 2));
var sum = function (x, y) {
  return x + y;
}
```

#### ES6 箭头函数

印象流, 化简回调非常简洁: > [1,2,3].map(x => x * x);

* 箭头函数没有this, 在其中this始终是箭头函数的定义处的this, 而和使用时调用者没有关系
* 箭头函数也没有arguments, arguments同样来自上级作用域
* 不能当成构造函数使用, 也就是不能使用`new`命令

箭头函数除了传入的参数之外，真的是什么都没有！如果你在箭头函数引用了this、arguments或者参数之外的变量，那它们一定不是箭头函数本身包含的，而是从父级作用域继承的

最佳实践: 什么时候使用箭头函数?

1. 箭头函数适合于无复杂逻辑或者无副作用的纯函数场景下，例如用在map、reduce、filter的回调函数定义中；
2. 不要在最外层定义箭头函数，因为在函数内部操作this会很容易污染全局作用域。最起码在箭头函数外部包一层普通函数，将this控制在可见的范围内；
3. 箭头函数最吸引人的地方是简洁。在有多层函数嵌套的情况下，箭头函数的简洁性并没有很大的提升，反而影响了函数的作用范围的识别度，这种情况不建议使用箭头函数

---

### 3. Go

在Go语言中，函数被看做是第一类值：(first-class values)：函数和其他值一样，可以被赋值，可以传递给函数，可以从函数返回。

Go语言函数有两点很特别：

* 函数值类型不能作为map的key
* 函数值之间不可以比较，函数值只可以和nil作比较，函数类型的零值是nil

以下代码不能通过编译:

```go
func main(){
	array := make(map[func ()int]int)
	array[func()int{return 12}] = 10
	fmt.Println(array)
}
```

#### 内建函数

并非所有的GO函数都是一等公民, 内建函数(builtin/builtin.go)不能作为函数值进行参数传递, 事实上这些内建类型并不真的在builtin包中，只是为了生成文档的需要.

可以在任意的包中调用这些内建函数，不必引入特定的包如"builtin"

```
1、close: 关闭channel
2、len(s):得到字符串、数组、数值指针、slice、map、chan的长度
3、cap(s):得到数组、数组指针的长度，得到slice、channel的容量
4、new(T): 生成类型T的零值指针,注意它返回的是指针 *T
5、make: 生成slice、map、channel对象
6、append(s S, x ...T) S: 增加0到n个元素到slice中，返回新的slice
7、copy(dst, src []T) int: 复制源src slice的元素到目标dst slice中,返回复制的元素的数量n, n是src和dst长度的最小值。字符串也可以作为src,但是T的类型必须是byte
8、delete(m,k): 删除map中的一个映射， m为nil或者m[k]不存在也不会panic,而是一个空操作
9、complex、real、imag: 复数操作
10、panic、recover: 报告panic和处理panic,后面讲
11、print、println: 尽量不用这两个函数，因为保证将来它们还会留在Go语言中，使用fmt.Print、fmt.Println
```

* 支持命名返回值
* defer 延迟调用

TODO [Golang中defer、return、返回值之间执行顺序的坑](https://my.oschina.net/henrylee2cn/blog/505535)

---

### 4. Lua

#### 避免代码死区

Lua要求break和return只能出现在block的结尾一句, 也就是chunk的最后一句, 具体讲, 就是end/else/until之前, 处于调试等目的想打破此规定, 可以将break/return包裹在do end块中.

----

参考资料

* [尾调用优化](http://www.ruanyifeng.com/blog/2015/04/tail-call.html)
* [Tail Call Optimization in Ruby](http://nithinbekal.com/posts/ruby-tco/)
* [Tail call optimization](https://groups.google.com/forum/#!msg/golang-nuts/0oIZPHhrDzY/2nCpUZDKZAAJ)

[comment]: <> (尾递归单独一个)
[comment]: <> (单独写一个匿名函数)
[comment]: <> (ruby 方法中再定义方法)
[comment]: <> (lua 中的函数可以使lua或者其他语言编写, 使用起来都一样)
[comment]: <> (https://i6448038.github.io/2017/10/06/GolangDetailsTwo/)

