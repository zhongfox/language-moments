# 3.4 作用域

|          | Ruby                                | Javascript                                                         | Go           | Lua                |
|----------|-------------------------------------|--------------------------------------------------------------------|--------------|--------------------|
| 作用域   | `class` `module` `def` 开启新作用域 | ES5: 函数开启闭包作用域<br>ES6: 增加`let` `const` 遵循块`{}`作用域 | 块作用域`{}` | 函数开启闭包作用域 |
| 闭包     | 不支持                              | 支持                                                               | 支持         | 支持               |
| 全局变量 | 以`$`开头                           | 全局执行环境下的变量                                               | 没有         |                    |

---

### 1. Ruby

* `class` `module` `def` 会开启新的变量作用域
* 利用block实现扁平化作用域
* 不支持闭包

#### 扁平化作用域

与`class` `module` `def`对应的通过传递block来扁平化作用域的办法有：`Class.new` `Module.new` `Module#define_method`

其他常见的扁平化作用域还有：`Object#instance_eval` `Module#class_eval` `Module#module_eval`

可以使用多个代码块来共享一个局部变量:

```ruby
lambda {
  share = 10

  Kernel.send :define_method, :increase_a do
    share += 1
  end

  Kernel.send :define_method, :show_a do
    puts share
  end
}.call
```

这和ES5最佳实践中，每个文件都定义一个马上运行的匿名函数如出一辙：

```javascript
(function () {
  var share = 10;

  function increaseA()  {
    share++;
  }

  function showA()  {
    alert(share);
  }

  window.increaseA = increaseA;
  window.showA = showA

} ());
```

#### 全局变量

以`$`开头的变量是全局变量, 引用未初始化的全局变量不会报错, 返回nil

---

### 2. Javascript

#### 执行环境和作用域

* **执行环境**(execution context): 定义了变量或者函数有权访问的其他数据, 决定了它们各自的行为
* **变量对象**(variable object): 每个执行环境都有一个与之关联的变量对象, 环境中定义的所有变量和函数都保存在对象中
* 最外层的执行环境叫做**全局执行环境**, 浏览器中全局执行环境是window对象
* 每个函数都有自己的执行环境, 可以叫做**局部执行环境**
* 在函数的执行环境中, 函数的活动对象作为变量对象, 最初活动只包括`arguments`对象
* 当代码在一个执行环境中执行, 会创建变量对象的一个**作用域链**, 作用域链的作用是保证对执行环境有权限访问的所有变量和函数的有序访问  
  作用域链的前端, 始终是当前执行环境的变量对象  
  函数中作用域链: 本函数的变量对象->包含该函数定义时的执行环境的变量对象->...->一直到全局执行环境

个人觉得可以简单地提炼: javascript函数开启闭包作用域

* ES5没有块作用域, ES6增加的`let` `const` 遵循块`{}`作用域
* var声明的变量, 将被自动添加到距离最近的可用执行环境中
* 不声明而直接初始化的变量, 将自动加入全局执行环境

最佳实践:

* 不声明而直接初始化变量是一个常见的错误做法, 建议在初始化变量前, 一定要进行声明.
* 变量需要向上搜索作用域链, 因此, 访问局部变量比访问全局变量快, 应该优先使用.

#### 全局变量

全局执行环境, 在浏览器中是window对象, 在Node.js中是global对象.

#### 闭包

TODO

---

### 3. Go

Go 支持块作用域, 同时也实现了函数闭包

```go
if true {
  num := 8          // 内部作用域内的变量
  fmt.Println(num)  // 8
}
// 外部作用域, 无法读取
fmt.Println(num)   // undefined: num
```

```go
num := 9

if true {
  num := 8         // 内部作用域内的独立声明变量
  fmt.Println(num) // 8
}
// 外部作用域读取外层变量, 互不干扰
fmt.Println(num)   // 9
```

```go
num := 9

if true {
  num = 8         // 内部作用域可以读写外部变量
  fmt.Println(num) // 8
}
// 外层变量被改变
fmt.Println(num)   // 8
```

#### 闭包

Go 语言支持闭包, 和javascript很像, 例子就省了.

---

### 4. Lua

