# 3.5 作用域

|          | Ruby                                | Javascript                                                         | Go           | Lua                |
|----------|-------------------------------------|--------------------------------------------------------------------|--------------|--------------------|
| 作用域   | `class` `module` `def` 开启新作用域 | ES5: 函数开启闭包作用域<br>ES6: 增加`let` `const` 遵循块`{}`作用域 | 块作用域`{}` | 函数开启闭包作用域 |
| 闭包     | 不支持                              | 支持                                                               | 支持         | 支持               |
| 全局变量 | 以`$`开头                           | 无修饰符声明                                                       | 无区分       | 无修饰符声明       |
| 局部变量 | 非`$`开头                           | var/let/const修饰                                                  | 无区分       | local声明          |

通常来说, 语言中过多的使用全局变量是一种不好的实践, 全局变量容易污染顶层命名空间, 而且查找全局变量可能会比局部变量更慢.

因此, 如果在语言中, 不用修饰符/特殊字符声明的变量默认是局部变量, 这应该是更好的设计. Ruby正是这样的. 但是遗憾的是, Javascript和Lua不是这样的, 这2门语言中, 局部变量需要显式的使用关键字`var` `local`, 对于经验不足的程序员来说, 可能导致更多的全局变量的产生.

---

### 1. Ruby

* `class` `module` `def` 会开启新的变量作用域
* 利用block实现扁平化作用域
* 函数不支持闭包, 不过lambda, proc提供闭包功能

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

#### 全局变量

其实Go没有全局变量, 在函数外定义的变量严格的说应该是包内的局部变量, 在包内的所有源文件中可以引用, 但是在包外是不可用的(除非大写导出).

#### 闭包

Go 语言支持闭包, 和javascript很像, 例子就省了.

关于 `:=` 作用域的坑:

```
package main
import (
    "fmt"
)
type A struct {
    s string
}
func main() {
    var a *A
    if check(a) {
        a, err := generate()
        fmt.Println(a.s, err)
    }
    fmt.Println(a.s) // panic: runtime error: invalid memory address or nil pointer dereference
}
func generate() (*A, error) {
    return &A{s: "b"}, nil
}
func check(a *A) bool {
    return true
}
```

以上代码会导致`invalid memory address`

代码源码意图是 `:=` 只创造新变量err, 而变量a沿用上层作用域定义.

但实际情况是, 对于使用`:=`定义的变量, 如果新变量与那个同名已定义变量不在一个作用域中时，那么golang会重新定义这个变量, 这就是导致这个问题的真凶.

导致以上异常的原因是`:=`

---

### 4. Lua

与Ruby和Javascript不同的是, Lua采用的是代码块作用域, 代码块包括: 一个控制结构(`do end`), 一个函数体, 或者一个chunk(变量声明所在的那个文件或者文本).

在REPL中, 一条完整的语句是一个chunk, 因此局部变量在语句之间是无法共享的. 如果需要共享, 可以把多条语句`do end`代码块中.

#### 全局变量

Lua全局变量不需要声明, 给一个变量赋值后就创建了全局变量(不使用local)

----

参考资料:

* [go语言作用域踩坑](http://michaelyou.github.io/2017/11/03/go-scope/)

