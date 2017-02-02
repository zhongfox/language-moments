# 6.1 异常处理

|                  | Ruby            | Javascript                       | Go                          | Lua                                |
|------------------|-----------------|----------------------------------|-----------------------------|------------------------------------|
| 错误             | Exception 实例  | Error 实例                       | Error 实例                  | 容错函数的第二个返回值代表错误代码 |
| 异常             | raise Exception | throw anyValue                   | panic Error                 | error (message [, level])          |
| 捕获异常         | rescue          | catch                            | recover                     | pcall/xpcall                       |
| 错误传递         | 可以但并不流行  | 通常异步回调函数的一个参数是错误 | `result, error := doSome()` | `result, error = do_some()`        |
| 调用栈中异常冒泡 | Y               | Y                                | Y                           | Y                                  |
| 调用栈           | `Kernel#caller` | `console.trace()`                | `debug.PrintStack()`        | `debug.traceback()`                |

---

### 1. Ruby

Ruby 内置异常(Exception)继承链:

![Ruby Exception Hierarchy](/images/ruby_exception.png)

#### Ruby 类 Exception

创建异常对象:

* `Kernel#raise` fail 是同义词
* `Exception.new(msg=nil)`

核心实例方法:

* `message → string` 返回异常消息
* `backtrace → array` 形如`filename:linenumber in methodname`的字符串数组, 异常抛出点的调用栈.

#### 异常捕获

```ruby
begin #开始

  raise 'some message' #抛出异常

rescue [ExceptionType = StandardException] #捕获指定类型的异常 缺省值是StandardException
  $! #当前异常
  $@ #表示异常出现的代码位置
else #其余异常
  ......
ensure #不管有没有异常,进入该代码块
  ......
end #结束
```

* 全局变量`$!`表示当前处理的异常对象.
* 把异常对象赋值给变量`rescue => ex`
* rescue从句并没有开启新的变量作用域.
* 只处理指定类型异常： `rescue 异常类` 或者结合赋值`rescue 异常类 => ex`, 指定多个类型`rescue 异常类1, 异常类2 => ex`
* 多个rescue类型从句,应该把子类放在最前面,因为rescue从上往下匹配.
* resuce从句如果没有指定要处理的异常类,默认只会捕捉StandardError(及其子类).
* retry 可以在rescue中尝试retry以重新执行rescue所在的那段begin end块
* begin end块中的else： else从句在有rescue的begin end块中才有意义,执行到else里表示没有异常（逻辑上等同于把else里的语句放到begin end外）
* ensure: ensure只能出现在rescue和else之后,无论是否有异常,ensure里的代码都会被执行.

#### 最佳实践：

* 自定义异常类时, 继承 StandardError 或任何其后代子类(越精确越好). 永远不要直接继承 Exception.
* 永远不要 rescue Exception. 如果你想要大范围捕捉异常, 直接使用空的rescue 语句（或者使用 `rescue => e` 来访问错误对象）

当你使用空的 rescue 语句时,它会捕捉所有继承自 StandardError 的异常,而不是 Exception.
如果你使用了 rescue Exception(当然你不应该这样), 你会捕捉到你无法恢复的错误(比如内存溢出错误). 而且, 你会捕捉到 SIGTERM 这样的系统信号，导致你无法使用 CTRL+C 来中止你的脚本.

---

### 2. Javascript

#### Exceptions vs. Errors

* Exception: 通过throw抛出的任意值, 不要求是Error的实例
* Error: Error实例

#### 错误类型 Error

Error 主要属性:

* `Error.captureStackTrace(targetObject[, constructorOpt])`

  在targetObject上创建代表代码定位的属性stack.

  可选参数constructorOpt是一个function, 表示在这个function以及之上的调用帧, 都自动去掉. 主要是起到去噪的作用

* `Error.stackTraceLimit` 返回Error捕获帧的数目, 可修改, 默认是10.

Error 实例重要属性:

* `Error#message` 错误消息
* `Error#stack` 错误栈是描述Error对象**初始化时**的调用栈

  错误栈的首行是`<error class name>: <error message>`, 后面跟着若干行的调用帧(stack frames)

**最佳实践**: 不用使用string代表错误, 在throw或者异步方式中, 都使用Error对象.

* 错误处理方不能使用`instanceof Error` 进行错误归类识别
* Error 对象有很多调试的重要信息, 使用string无法提供, 比如错误堆栈

参见[A String is not an Error](http://www.devthought.com/2011/12/22/a-string-is-not-an-error/)

#### 自定义错误类型

```javascript
var util = require('util');
var AbstractError = function (message) {
  Error.call(this); // 继承相关
  Error.captureStackTrace(this, arguments.callee);
  this.message = message;
  this.name = errorClass;
};
util.inherits(AbstractError, Error)
```

#### 异常传递方式

* 同步: throw
* 异步:
  * callback
  * EventEmitter

全局处理方式:

* domains
* process.on('uncaughtException')

#### 异常处理规范

什么时候用throw, 什么时候用callback, 什么时候又用 EventEmitter? 这取决于两件事：

1. 这是操作失败还是程序员的失误?
2. 这个函数本身是同步的还是异步的?

<table><tbody>
  <tr>
    <th>错误类型</th>
    <th>操作类型</th>
    <th colspan="1" contenteditable="true">场景</th>
    <th>传递方式</th>
    <th>调用者</th>
    <th>实例</th>
  </tr>
  <tr>
    <td>程序员错误</td>
    <td>均可</td>
    <td colspan="1" width="" contenteditable="true">均可</td>
    <td>throw</td>
    <td>crash</td>
    <td>`fs.stat` null for filename</td>
  </tr>
  <tr>
    <td rowspan="3">操作错误</td>
    <td>同步</td>
    <td colspan="1" contenteditable="true">均可</td>
    <td>throw</td>
    <td>try/catch</td>
    <td>`JSON.parse` 非法json</td>
  </tr>
  <tr>
    <td rowspan="2">异步</td>
    <td colspan="1" contenteditable="true">简单场景</td>
    <td>callback</td>
    <td>按需处理错误</td>
    <td>`fs.stat` file not found</td>
  </tr>
  <tr>
    <td colspan="1"  contenteditable="true"><p>复杂场景</p><p>大量异步事件</p></td>
    <td>EventEmitter</td>
    <td>按需处理错误</td>
    <td>网络操作中的错误事件</td>
  </tr>
</tbody></table>

---

### 3. Go

#### 错误

* Go 预定义了`error`类型, 不过居然不是大写, 但是确实是可以在任何包内使用:

  ```go
  type error interface {
    Error() string
  }
  ```

* 可以看到`error`类型只要求实现一个`Error`方法, 因此可以如下实现自定义错误:

  ```go
  func (myError) Error() string {
    return "my error message"
  }
  ```

* 包errors提供了快速创建`error`实例的方法:

  `errors.New(text String) error`

#### 异常

* 异常抛出: `func panic(interface{})`

  异常的抛出会立即中断函数执行, 开始执行defer

  在defer中调用panic, 不会终止后续defer的执行

* 异常捕获: `func recover() interface{}`

  同一个函数中连续调用(第二个应该是在defer中调用)panic, 只有最后执行那个能被recover捕获

  异常捕获后, 可以再次抛出

  recover 必须在defer调用的函数中才有效, 如`defer recover()`无效

  异常被recover后, 函数的返回值: 如果在panic之前命名返回值已经赋值, 将返回该值, 否则返回对应的零值. recover 后续代码中也可以调整继续改变命名返回值

  panic如果没有处理,会传递给它的调用者,这就是panic的bubble

* 异常捕获实例:

  ```go
  defer func() {
    if r := recover(); r != nil {
      log.Printf("Runtime error caught: %v", r)
      debug.PrintStack() //打印异常栈, 需要import "runtime/debug"
    }
  }()
  ```

* 打印异常栈: `debug.PrintStack()` 需要`import "runtime/debug"`

#### 命名规范:

* 错误变量以`err`开头, 错误消息全小写, 不要结束标点
* 自定义错误类型通常以`Error`结尾

#### 调试:

* `PrintStack` from `runtime/debug` prints to standard error the stack trace returned by Stack.
* To print the stack trace for all goroutines use `Lookup` and `WriteTo` from `runtime/pprof`.

---

### 4. Lua

* 函数错误通常指导原则: 易于避免的异常引发一个错误(调用error), 否则返回错误代码(错误消息)

* 多数函数设计的返回值是: `value, message = callsome()` 我把这种函数叫做容错函数

  如果发生错误, value 为nil, message 为错误消息; 如果没有错误, value为正常返回值

* `assert(dosomething, message)` 类似于 `dosomething ? dosomething : error(message)`

  lua常见技巧: `result = assert(容错函数调用)`; 期望得到正确的返回值, 否则就抛异常

#### lua中的try-catch: pcall和xpcall

**pcall**

`status, error_or_result = pcall(somefunction)`

成功的话, status是true, `error_or_result`是somefunction的返回值, 发生异常的话, status是false, `error_or_result`是错误消息, 不一定是字符串, 是抛出异常时调用error时的参数

**xpcall**

通常在错误发生时，希望落得更多的调试信息，而不只是发生错误的位置。但pcall返回时，它已经销毁了调用桟的部分内容。Lua提供了xpcall函数，xpcall接收第二个参数: 一个错误处理函数，当错误发生时，Lua会在调用桟展开（unwind）前调用错误处理函数，于是就可以在这个函数中使用debug库来获取关于错误的额外信息了:

`status, error_or_result = xpcall(somefunction, errorHandler)`

debug库提供了两个通用的错误处理函数:

* debug.debug：提供一个Lua交互环境, 环境中保留了当前lua的堆栈, 非常利于调试.

  `xpcall(dangerous_func, function () debug.debug() end)`

* debug.traceback：根据调用桟来构建一个扩展的错误消息.

  `pcall(dangerous_func, function () print(debug.traceback()) end)`

---

参考资料

* [Error Handling in Node.js](https://www.joyent.com/developers/node/design/errors)
* [Why is it bad style to 「rescue Exception => e」 in Ruby?](http://stackoverflow.com/questions/10048173/why-is-it-bad-style-to-rescue-exception-e-in-ruby)
* [Node.js Documentation Error](https://nodejs.org/api/errors.html)
