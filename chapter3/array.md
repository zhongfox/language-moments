# 3.10 数组

|          | Ruby                                       | Javascript                                | Go                                                | Lua                                 |
|----------|--------------------------------------------|-------------------------------------------|---------------------------------------------------|-------------------------------------|
| 起始索引 | 0                                          | 0                                         | 0                                                 | 1                                   |
| 倒序索引 | `arr[-1]`                                  | 不支持                                    | 不支持                                            | 不支持                              |
| 索引越界 | nil                                        | undefined                                 | panic                                             | nil                                 |
| 长度     | `length`<br>`size`                         | `属性 length`                             | 内置函数`len()`                                   | `#`<br>`table.getn(t)`              |
| 长度可写 | 不支持                                     | `arr.length = 3`                          | 不支持                                            | 不支持                              |
| 追加     | `Array#<<`<br> `push`                      | `arr.push([element1[, ...[, elementN]]])` | `func append(slice []Type, elems ...Type) []Type` | `table.insert (list, [pos,] value)` |
| 连接     | `Array#+(other)`<br>`concat(ary1,ary2...)` | `concat(ary1,ary2...)`                    | `append(slice1, slice2...)`                       | 自行实现                            |

---

### 1. Ruby

* 无空格短字符串组成的数组, 有额外的表达方式:

  `words = %w[hello ruby]`

* Ruby Array的语法糖很多, 举几个例子:

  ```ruby
  [1,2] * 3     # [1,2,1,2,1,2]
  [1,2] & [2,3] # [3]
  [1,2] | [2,3] # [1,2,3]
  ```

---

### 2. Javascript

#### 长度可写

这算是几门语言中, Javascript比较特殊的地方:

```javascript
let arr = [1, 2, 3];
arr.length = 2; //[1, 2]
```

#### ES6 扩展

ES6 对Array进行了一系列扩展, 介绍几个:

##### find 和 findIndex

Array.prototype.find(callback[, thisArg])

```javascript
[1, 4, -3, 10].find((n) => n < 0)
// -3
```

还可以接受三个参数:

```javascript
[1, 5, 10, 15].find(function(value, index, arr) {
  return value > 9;
}) // 10
```

可选参数thisArg, 用来绑定回调函数的this对象.

类似的方法还有`Array.prototype.findIndex(Array.prototype.findIndex())`

#### entries(), keys()和values()

Array.prototype 新增了三个方法, entries keys 和 values 都可以返回一个[遍历器对象](TODO), 分别可以遍历键名, 键值, 以及键值对.

---

### 3. Go

TODO slice

---

### 4. Lua

Lua 使用table来实现数组, Lua 中叫做列表(list), 索引从1开始.

> days = {"Sunday", "Monday", "Tuesday", "Wednesday",  
>              "Thursday", "Friday", "Saturday"}

table 中如果有nil, table的长度是不确定的. 因此在使用Lua list时, 尽量避免空洞nil.

---

<!--
TODO:

切片


lua: table.remove 而不要用赋值nil, 赋值nil会造成空洞list

-->
