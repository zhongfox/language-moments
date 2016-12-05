# 3.7 字符串

|            | Ruby           | Javascript       | Lua            | Java   | Go                                           |
|------------|----------------|------------------|----------------|--------|----------------------------------------------|
| 存储       |                | UCS-2            |                |        | Unicode                                      |
| 可变性     | 可变           | 不可变           | 不可变         | 不可变 | 不可变                                       |
| 字面量     | 单引号, 双引号 | 单引号, 双引号   | 单引号, 双引号 | 双引号 | 双引号                                       |
| 长度       |                | 字符: `len(str)` |                |        | 字节: `len(str)`<br>字符: `len([]rune(str))` |
| 遍历       |                |                  |                |        | 字节: `for i`<br>字符: `for range`           |
| 索引访问   |                |                  |                |        | 支持: `str[1]`                               |
| 片段       |                |                  |                |        | 切片语法: `str[start:end]` 左闭右开          |
| 拼接       |                |                  |                |        | `str1 + str2`                                |
| to 数组    |                |                  |                |        | 字节: `byte(str)`<br>字符: `[]rune(str)`     |
| from 数组  |                |                  |                |        | `string(byte_or_rune_array)`                 |
| 字符串模板 | 支持: "#{var}" | ES6支持          |                |        | 支持                                         |

---

### Ruby

### Javascript

ES6 对js的字符串进行了扩展支持, 参见[Unicode与JavaScript详解](http://www.ruanyifeng.com/blog/2014/12/unicode.html)

### Lua

### Java

### Go

* 加法拼接字符串, 每次需要重新分配内存, 在构建超大字符串时, 性能低下

  优化: 使用`strings.Join(a []string, sep string)` 会一次性计算并分配需要的内存

---

## 字符串模板

* 多行
* html
* 单双引号混用

|          | Ruby | Javascript | Lua | Java | Go |
|----------|------|------------|-----|------|----|
| 内嵌     |      |            |     |      | N  |
| 转义处理 |      |            |     |      | N  |
| 多行     |      |            |     |      | Y  |

* Ruby

* Javascript

  ES6:

  > <code>`</code>${start}, my name is ${getName()}, ${conf.fav} is my favourite<code>`</code>

* Lua

* Java

* Go

