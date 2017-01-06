# 4.4 迭代

|          | Ruby                                                          | Javascript                | Go | Lua  |
|----------|---------------------------------------------------------------|---------------------------|----|------|
| String   | `each_byte`<br>`each_char`<br>`each_codepoint`<br>`each_line` | ES6 Iterator              |    |      |
| Array    | mixin Enumerable<br>`each_index`                              | ES6 Iterator<br>`forEach` |    |      |
| Hash/Map | mixin Enumerable<br>`each_key`<br>`each_pair`<br>`each_value` | ES6 Iterator<br>`forEach` |    |      |
| Set      | mixin Enumerable<br>`each_key`<br>`each_pair`<br>`each_value` | ES6 Iterator<br>`forEach` |    | TODO |


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

---


### 4. Lua

---
