# 3.10 数组

|          | Ruby                                       | Javascript                                | Go                                                | Lua  |
|----------|--------------------------------------------|-------------------------------------------|---------------------------------------------------|------|
| 起始索引 | 0                                          | 0                                         | 0                                                 | 1    |
| 倒序索引 | `arr[-1]`                                  | 不支持                                    | 不支持                                            |      |
| 索引越界 | nil                                        | undefined                                 | panic                                             |      |
| 长度     | `length`<br>`size`                         | `属性 length`                             | 内置函数`len()`                                   |      |
| 长度可写 | 不支持                                     | `arr.length = 3`                          | 不支持                                            |      |
| 追加     | `Array#<<`<br> `push`                      | `arr.push([element1[, ...[, elementN]]])` | `func append(slice []Type, elems ...Type) []Type` |      |
| 连接     | `Array#+(other)`<br>`concat(ary1,ary2...)` | `concat(ary1,ary2...)`                    | `append(slice1, slice2...)`                       | TODO |


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

* 长度可写:

  ```javascript
  let arr = [1, 2, 3];
  arr.length = 2; //[1, 2]
  ```

---

### 3. Go

TODO slice

---

### 4. Lua

<!--
TODO:

切片

-->
