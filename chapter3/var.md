# 3.2 变量

|                                    | Ruby                                      | Javascript                                                         | Go           | Lua                |
|------------------------------------|-------------------------------------------|--------------------------------------------------------------------|--------------|--------------------|
| 类型                               | 弱类型                                    | 弱类型                                                             |              |                    |
| 未声明变量                         | NameError<br>`defined? nonexist_var #nil` | ReferenceError<br>`typeof nonexistVar //undefined`                 | 编译错误     |                    |
| 未赋值初值                         | nil                                       | undefined                                                          | 二进制零值   |                    |
| 未使用的局部变量                   | no problem, but bad style                 | no problem, but bad style                                          | 编译错误     |                    |
| [变量作用域](/chapter3/scope.html) | `class` `module` `def` 开启新作用域       | ES5: 函数开启闭包作用域<br>ES6: 增加`let` `const` 遵循块`{}`作用域 | 块作用域`{}` | 函数开启闭包作用域 |
| Evaluation                         | 值传递                                    | 值传递                                                             | 值传递       | ?                  |

---

### 1. Ruby

Ruby 是一门非常纯粹的面向对象的语言, 所有值都是对象, 没有基本类型(primitive)和对象类型的区别

#### 复制: 值拷贝

当我们再Ruby中使用对象时, 其实是在使用对象的引用. Ruby复制是执行的值拷贝, 只是这个「值」刚好是对象的引用

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

#### 复制: 值拷贝

Javascript复制是执行的值拷贝, 有的也叫[Call by sharing](https://en.wikipedia.org/wiki/Evaluation_strategy#Call_by_sharing)

对于primitive来说, 「值」是stack中的内容数据, 而对于Object来说, 「值」是对象的引用

因此看起来结果类似这样: primitive传递的是值, 而Object传递的是「引用」

```javascript
function change(num, obj) {
  num += 1;
  obj.name = 'fox';
}

var primitiveValue = 5;
var referenceVlaue = {};

change(primitiveValue, referenceVlaue);
console.log(primitiveValue); //5, primitive的值没有变化
console.log(referenceVlaue); //{ name: 'fox' } Object值发生变化
```

注意区别以下情况:

```javascript
function change(obj) {
  //右值生成了一个全新的变量, 赋给了局部变量obj, 此obj和传入的对象分离, 完全没有关系了
  obj = {name: 'fox'};
}

var referenceVlaue = {name: 'zhong'};

change(primitiveValue, referenceVlaue);
console.log(referenceVlaue); //{ name: 'zhong' } Object值没有发生变化
```

---

### 3. Go

---

### 4. Lua

---

