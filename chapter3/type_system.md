# 3.1 类型系统

|          | 主要表现             | Ruby     | Javascript | Go       | Lua      |
|----------|----------------------|----------|------------|----------|----------|
| 强弱分类 | 是否容忍隐式类型转换 | 强类型   | 弱类型     | 强类型   | 弱类型   |
| 动静分类 | 变量类型是否可变     | 动态类型 | 动态类型   | 静态类型 | 动态类型 |

语言类型系统有强类型/弱类型, 静态类型/动态类型 2种维度, 概念上往往容易混淆.

---

### 学术定义

**Program Errors**

* trapped errors: 导致程序终止执行, 如除0, Java中数组越界访问
* untrapped errors: 出错后继续执行, 但可能出现任意行为. 如C里的缓冲区溢出, Jump到错误地址

**Forbidden Behaviours**

语言设计时，可以定义一组forbidden behaviors: 它必须包括所有untrapped errors, 但可能包含trapped errors.

**behaved**

* well behaved: 如果程序执行不可能出现forbidden behaviors, 则为well behaved.(红色区域外)
* ill behaved: 否则为ill behaved. (只要出现红色区域)

![Program Errors](/images/programerrors.png)

### 类型定义

* 强类型: 所有程序都是well behaved, 即不可能出现forbidden behaviors(无红色区域)

  表现形式: 偏向于不容忍隐式类型转换

* 弱类型: 否则为弱类型, 可能出现forbidden behaviors(只要出现红色区域)

  表现形式: 偏向于容忍隐式类型转换

* 静态类型: 编译时拒绝ill behaved (编译时排除红色程序)

  表现形式: 编译的时候就知道每一个变量的类型, 因为类型错误而不能做的事情是语法错误

* 动态类型: 运行时拒绝ill behaved (运行时排除红色程序)

  表现形式: 编译的时候不知道每一个变量的类型, 因为类型错误而不能做的事情是运行时错误

* type safe: 所有程序都在黄框以外.

动态, 静态类型和强弱类型没有对应关系, 比如动态类型Ruby, Python是强类型语言.

### 静态/动态

静态类型的特点是变量在编译时确定了类型, 无法改变, 如C, Go, Java等:

```go
// go
var a = 3
a = "fox" //报错
```

而动态类型中变量类型可以改变:

```ruby
# ruby
x = 3
x = "zhong"
x = :fox
```

### 强类型/弱类型

强类型偏向于不容忍隐式类型转换, 以下例子可以说明ruby是强类型:

```ruby
# ruby
x = '3'
y = 3
x + y # TypeError: no implicit conversion of Fixnum into String
```

Ruby  允许你反复修改一个变量的类型(动态类型), 但是如果你在一个语句中混合多种类型, Ruby并不情愿去猜测你想表达什么意思, 猜测太复杂, 而且有风险(有可能猜错, 比如上例中, 用户是期望返回「33」还是「6」呢), 所以Ruby直接返回错误.[^注1]

而弱类型如C, Javascript, Lua等偏向于容忍隐式类型转换:

```javascript
// javascript
var x = '3';
var y = 3;
x + y // '33'
```

```lua
-- lua
x = '3'
y = 3
x + y # 6
```

从上面的例子中可以看出来, 两种弱类型语言对混合类型运算的「猜测」不一样, 如果你经常会跨语言开发, 依赖这些隐式类型转换将可能造成潜在的bug.

**最佳实践**: 隐式类型转换造成的问题远大于它带来的好处, 不要依赖它.

### 语言类型坐标

![语言类型坐标](/images/type_system.png)

可以看出的趋势, 较新的语言, 逐渐都放弃了Weak type, 更多的采用Strong type.

---

### 参考资料

* [Type_system](https://en.wikipedia.org/wiki/Type_system)
* [Ruby is dynamically AND strongly typed](http://www.rubyfleebie.com/ruby-is-dynamically-and-strongly-typed/)
* [弱类型、强类型、动态类型、静态类型语言的区别是什么？](https://www.zhihu.com/question/19918532)

---

[^注1]: Ruby中也存在一定的隐式类型转换, 见[类型转换](/chapter3/conversion.html#1-ruby)
