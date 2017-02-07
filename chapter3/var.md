# 3.3 变量

|                                    | Ruby                                | Javascript                                                         | Go                     | Lua                       |
|------------------------------------|-------------------------------------|--------------------------------------------------------------------|------------------------|---------------------------|
| 使用未声明变量                     | NameError                           | ReferenceError                                                     | 编译错误:undefined XXX | nil                       |
| 判断未声明变量                     | `defined? nonexist_var #nil`        | `typeof nonexistVar //undefined`                                   | TODO                   | 任何不存在的变量即为nil   |
| 未赋值初值                         | nil                                 | undefined                                                          | 二进制零值             | nil                       |
| 未使用的局部变量                   | no problem, but bad style           | no problem, but bad style                                          | 编译错误               | no problem, but bad style |
| [变量作用域](/chapter3/scope.html) | `class` `module` `def` 开启新作用域 | ES5: 函数开启闭包作用域<br>ES6: 增加`let` `const` 遵循块`{}`作用域 | 块作用域`{}`           | 块作用域`do end`          |
| 赋值                               | 值拷贝                              | 值拷贝                                                             | 值拷贝                 | 值拷贝                    |

Ruby, Javascript, Lua 变量都有局部变量和全局变量之分, Go语言只有包内的全局变量<!--TODO-->, 通常来说, 应该尽可能使用局部变量, 使用局部变量至少有以下2个好处:

1. 避免命名冲突.
2. 访问局部变量的速度比全局变量更快.

---

### 1. Ruby

Ruby 是一门非常纯粹的面向对象的语言, 所有值都是对象, 没有基本类型(primitive)和对象类型的区别.

#### 赋值: 值拷贝

当我们在Ruby中使用对象时, 其实是在使用对象的引用. Ruby复制是执行的值拷贝, 只是这个「值」刚好是对象的引用.[^注1]

```ruby
str1 = 'hello'
str2 = str1
str1 << ' Ruby'

puts str1 # hello Ruby
puts str2 # hello Ruby
```

不过要注意的是, 给一个变量赋予另一个变量, 和给一个变量赋予一个字面量, 容易引起误解:

* 给变量a赋予另一个变量b: a的引用将和b的引用一致, 因此a和之前自己的引用完全分离
* 给变量a赋予一个字面量C, a的引用指向这个新的字面量, 同样a和之前自己的引用完全分离

如下:

```ruby
str1 = 'hello'
str2 = str1
str1 << ' Ruby'

puts str1 # hello Ruby
puts str2 # hello Ruby

str1 = 'hello Javascript' # 这里str1和原来指向的和str2相同的引用分离
puts str1 # hello Javascript
puts str2 # hello Ruby
```

---

### 2. Javascript

* primitive 需要的内存空间固定, 存于stack, 直接存储在变量访问的位置, 传递时复制值
* 引用类型需要的内存空间可变, 存于heap, 传递时复制heap内存地址
* 引用类型(Object)的内存地址(引用)是固定大小的, 因此对象的引用是存于stack
* primitive 类型无法动态增加属性, 引用类型可以

#### 赋值: 值拷贝

Javascript复制是执行的值拷贝, 对于primitive来说, 「值」是stack中的内容数据, 而对于Object来说, 「值」是对象的引用

因此看起来结果类似这样: primitive传递的是值, 而Object传递的是「引用」

```javascript
var a = {name: 'zhong'};
var b = a;
b.name = 'fox';
console.log(a.name); // fox
```

以上代码中变量a和b只有同一个在堆上的对象引用, 对于primitive类型, 两个变量将持有2份独立的primitive值:

```javascript
var a = 'Im a primitive';
var b = a; //2份独立的primitive值, 只是内容一样
```

不过primitive类型是无法改变的, 只能重新赋值.

---

### 3. Go

#### 零值

未初始化的变量初始值将是二进制零值:

* bool       --> false
* numbers    --> 0
* string     --> ""
* pointers   --> nil
* slices     --> nil 零值slice可append
* maps       --> nil 零值的map能读(返回value零值), 但不能写
* channels   --> nil
* functions  --> nil
* interfaces --> nil
* struct     --> 完整的struct 各字段是对应零值

#### 赋值: 值拷贝

TODO 尽量使用指针

---

### 4. Lua

访问一个么初始化的变量不会出错, 只会得到nil, 给一个变量赋值nil, 等同于删除这个变量.

---

[^注1]: Ruby 中并非所有数据都是引用, 立即值对象(Fixnum, Symbol)并无引用.
