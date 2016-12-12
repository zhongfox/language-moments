# 5.1 函数比较

> 函数对任何语言来说都是一个核心的概念

|                    | Ruby                  | Javascript                                        | Lua | Java | Go                                                      |
|--------------------|-----------------------|---------------------------------------------------|-----|------|---------------------------------------------------------|
|                    | def                   | function                                          |     |      | func                                                    |
| First Class Object | N                     | Y                                                 |     |      | Y                                                       |
| 匿名函数与闭包     | N<br>lambda有类似效果 | Y                                                 |     |      | Y                                                       |
| 参数传递           |                       | 值拷贝                                            |     |      | 值拷贝                                                  |
| 参数默认值         |                       | ES5: N<br>ES6: Y                                  |     |      | N                                                       |
| 不定长变参         |                       | ES5: arguments<br>ES6: `function(...arrayInside)` |     |      | Y<br>`func (slice_inside ...int)`                       |
| 命名参数           |                       | N<br>使用object代替                               |     |      | N<br>使用struct代替                                     |
| 多返回值           |                       | N                                                 |     |      | Y<br>不能用数组切片接收<br>可用于函数实参, 或者赋值右值 |
| 默认返回值         | 最后一个表达式的值    | undefined                                         |     |      | N                                                       |
| 函数重载           |                       | N                                                 |     | Y    | N                                                       |

---

### Ruby

bang!函数最佳实践


---

### Javascript

#### 参数

* arguments

  和大多数语言不同, Javascript函数不介意传递进来多少参数, 原因在于Javascript中参数在内部是用一个数组来表示, 函数接收到的始终都是这个数组, 而不关心数组里有哪些参数.

  这个数组就是arguments, 它是一个伪数组, 不是Array的实例, 但是可以使用`[]`进行索引, 也有length属性

* 没有传递的参数自动赋予undefined

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

重复定义的函数, 后面讲覆盖前面的, 不论其参数列表是否相同

---



### Lua

---

### Java

---

### Go

* 支持命名返回值
* defer 延迟调用
