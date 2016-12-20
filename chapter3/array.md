# 3.9 数组

|          | Ruby                           | Javascript                                | Lua | Go                                                |
|----------|--------------------------------|-------------------------------------------|-----|---------------------------------------------------|
| 起始索引 | 0                              | 0                                         | 1   | 0                                                 |
| 倒序索引 | `arr[-1]`                      | 不支持                                    |     |                                                   |
| 索引越界 | nil                            | undefined                                 |     |                                                   |
| 长度     | `Array#length`<br>`Array#size` | `length`                                  |     |                                                   |
| 长度可写 | N                              | `arr.length = 3`                          |     |                                                   |
| 追加     | `Array#<<`                     | `arr.push([element1[, ...[, elementN]]])` |     | `func append(slice []Type, elems ...Type) []Type` |
| 连接     | `Array#+(other)`               |                                           |     |                                                   |


### Ruby

* 无空格短字符串组成的数组, 有额外的表达方式:

  `words = %w[hello ruby]`

* Ruby Array的语法糖很多, 举几个例子:

  > [1,2] * 3     # [1,2,1,2,1,2]  
  > [1,2] & [2,3] # [3]  
  > [1,2] | [2,3] # [1,2,3]

---

### Javascript

* 长度可写:

  ```javascript
  let arr = [1, 2, 3];
  arr.length = 2; //[1, 2]
  ```

---

### Lua

---

### Java

---

### Go

