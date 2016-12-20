# 5.1 函数比较

> 函数对任何语言来说都是一个核心的概念

|                        | Ruby                  | Javascript                                        | Lua | Java | Go                                                      |
|------------------------|-----------------------|---------------------------------------------------|-----|------|---------------------------------------------------------|
|                        | def                   | function                                          |     |      | func                                                    |
| First Class Object     | N                     | Y                                                 |     |      | Y                                                       |
| 匿名函数与闭包         | N<br>lambda有类似效果 | Y                                                 |     |      | Y                                                       |
| 参数传递               |                       | 值拷贝                                            |     |      | 值拷贝                                                  |
| 参数默认值             |                       | ES5: N<br>ES6: Y                                  |     |      | N                                                       |
| 实参和形参数量必须一致 | 必须                  | 不必须                                            |     |      |                                                         |
| 不定长变参             |                       | ES5: arguments<br>ES6: `function(...arrayInside)` |     |      | Y<br>`func (slice_inside ...int)`                       |
| 命名参数               |                       | N<br>使用object代替                               |     |      | N<br>使用struct代替                                     |
| 多返回值               |                       | N                                                 |     |      | Y<br>不能用数组切片接收<br>可用于函数实参, 或者赋值右值 |
| 默认返回值             | 最后一个表达式的值    | undefined<br>>ES6 箭头函数某些情况会自动return    |     |      | N                                                       |
| 函数重载               |                       | N                                                 |     | Y    | N                                                       |

---

### Ruby

bang!函数最佳实践

method(sym) → method

函数中再定义函数?


---

### Javascript

#### 函数的属性和方法

* 属性 length: 形参个数
* 属性 prototype: 见[面向对象]()
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

### Lua

---

### Java

---

### Go

* 支持命名返回值
* defer 延迟调用


----

单独写一个匿名函数
