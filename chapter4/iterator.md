# 4.4 遍历与迭代器

> 迭代器是一种支持(类)指针的结构, 它可以遍历集合的每一个元素. 迭代器需要保留上一次成功调用的状态和下一次成功调用的状态, 也就是他知道来自哪里和将要去向哪里.

|          | Ruby                                                          | Javascript                | Go                        | Lua                                |
|----------|---------------------------------------------------------------|---------------------------|---------------------------|------------------------------------|
| String   | `each_byte`<br>`each_char`<br>`each_codepoint`<br>`each_line` | ES6 Iterator              | `for i, v := range str {` | `for i = 1, #str do ... end`       |
| Array    | mixin Enumerable<br>`each_index`                              | ES6 Iterator<br>`forEach` | `for i, v := range arr {` | `for k, v in ipairs(a) do ... end` |
| Hash/Map | mixin Enumerable<br>`each_key`<br>`each_pair`<br>`each_value` | ES6 Iterator<br>`forEach` | `for k, v := range m {`   | `for k, v in pairs(m) do ... end`  |
| Set      | mixin Enumerable<br>`each_key`<br>`each_pair`<br>`each_value` | ES6 Iterator<br>`forEach` | 内置无Set[^注1]           | 内置无Set[^注2]                    |


<!--
TODO: js Object 只能for in
TODO: 遍历格式写完整
-->
在Ruby中mixin Enumerable的类, 需要实现each方法.

在ES6中, 实现了Iterator接口的数据结构可以使用`for of`迭代器, 原生具备Iterator接口的数据结构有: String, Array, Set, Map.

上表中, Hash/Map在Javascript中, 指的是ES6提供的Map, ES5中的Object迭代只能使用`for in`进行遍历.

---

### 1. Ruby

#### Enumerable

Ruby 标准库中提供了Enumerable模块, 其他类在mixin该模块后, 可以获得内部迭代器的功能. 该模块要求宿主类必须实现名为`each`的实例方法, 并且每个将被迭代的元素都实现了 `<=>`, 在付出这些小小的代价后, Enumerable会为宿主类加入一些列非常有用的方法, 介绍其中几个:

* `all? [{ |obj| block } ] → true or false`
* `any? [{ |obj| block }] → true or false`
* `find(ifnone = nil) { |obj| block } → obj or nil`
* `include?(obj) → true or false`
* `min`
* `max`
* `sort → array` `sort { |a, b| block } → array`
* `sort_by { |obj| block } → array` `sort_by → an_enumerator`
* `each_with_index(*args) { |obj, i| block } → enum`

mixin Enumerable的常见类有Array, Hash. 令人意外的是String并没有混入Enumerable模块.

---

### 2. Javascript

#### Array

* 最初想遍历一个数组, 只能使用`for (var i = 0; i < len; i++) ...`(简称for i).
* `for in` 通常用于遍历Object的属性, 不过也可以用于数组, 进行index遍历.
* Javascript ES5 提供了`Array.prototype.forEach(callback[, thisArg])`
* ES6 对Array部署了Iterator, 可以使用`for of`迭代

#### Object

Object可以使用`for in`迭代属性, 可迭代的属性值包括原型链的上属性, 需要使用`Object.prototype.hasOwnProperty(prop)`进行过滤

#### Set

* `Map.prototype.forEach(callback[, thisArg])`

  callback 的参数列表为 `value, key, map`.

  可选参数thisArg用于指定callback中的this对象.

* Set原生具备Iterator接口, 可以直接使用`for of`迭代.

  ```javascript
  var set = new Set([1, 2, 3, 4, 4]);
  for (let i of set) {
    console.log(i);
  }
  // 1,2,3,4
  ```

#### Map

* `Map.prototype.forEach(callback[, thisArg])`

* Map 原生具备Iterator接口, 可以直接使用`for of`迭代, 迭代个体为键值对数组:

  ```javascript
  var map = new Map([['x', 1], ['y', 2]]); // Map { 'x' => 1, 'y' => 2 }

  for (let item of map) {
    console.log(item); //['x', 1], ['y', 2]
  }

  //直接迭代Map等同于迭代map.entries()
  for (let item of map.entries()) {
    console.log(item); //['x', 1], ['y', 2]
  }

  //还可以借助赋值拆解分离出key和value
  for (let [key, value] of map.entries()) {
    console.log(key, value);
  }
  ```

  Map 除了提供了`entries()`, 还提供了`keys()`和`values()`, 返回的也是一个迭代器.

---

### 3. Go

TODO

---


### 4. Lua

#### numeric for

```lua
for 控制变量=初始值, 终止值, step do
   loop-part
end
```

* 三个表达式只会被计算一次, 并且是在循环开始前
* 控制变量 自动声明为局部变量, 并且只在循环内有效
* step 可以省略, 默认是1
* 循环中不要改变控制变量的值, 那样做的结果不可预知.

#### Generic for

```lua
for 若干控制变量 in 迭代器 do
   loop-part
end
```

* 控制变量自动声明为局部变量, 并且只在循环内有效.
* 循环中不要改变控制变量的值, 那样做的结果不可预知.

常见的迭代器:

* `pairs` 用于遍历一个table的键值对, 如 `for k, v in pairs(a) do print(k, v) end`

  pairs 会迭代所有键值对, 包括数字索引(数组)和其他所有, 注意不保证顺序, 连数字索引也不保证顺序.

  ipairs 只遍历从1开始的连续数字索引, 保证顺序.

* `io.lines`
* `string.gmatch`

---

[^注1]: 第三方实现 GO Set, 见<https://godoc.org/github.com/deckarep/golang-set>
[^注2]: Lua可以利用table实现Set, 见<http://lua-users.org/wiki/SetOperations>

---
