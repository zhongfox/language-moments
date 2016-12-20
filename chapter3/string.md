# 3.8 字符串

|            | Ruby                                                                                                      | Javascript       | Lua            | Java   | Go                                           |
|------------|-----------------------------------------------------------------------------------------------------------|------------------|----------------|--------|----------------------------------------------|
| 存储       | 1.8: 字节序列<br>1.9字符序列                                                                              | UCS-2            |                |        | Unicode                                      |
| 可变性     | 可变<br>`str[-1]='hi'`                                                                                    | 不可变           | 不可变         | 不可变 | 不可变                                       |
| 字面量     | 单引号, 双引号<br>单双有别, 双引号支持模板                                                                | 单引号, 双引号   | 单引号, 双引号 | 双引号 | 双引号                                       |
| 长度       | 字节: `String#bytesize`<br>字符:`String#length` `String#size`                                             | 字符: `len(str)` |                |        | 字节: `len(str)`<br>字符: `len([]rune(str))` |
| 遍历       | 字节:String#each_byte<br>字符:String#each_char<br>字符码:String#each_codepoint<br>行遍历:String#each_line |                  |                |        | 字节: `for i`<br>字符: `for range`           |
| 索引访问   | 支持: `str[1]` 倒叙索引: `str[-1]`                                                                        |                  |                |        | 支持: `str[1]`                               |
| 索引越界   | nil                                                                                                       |                  |                |        |                                              |
| 片段       | 返回全新字符串:<br>`str[start, len]`<br>`str(start..end)`                                                 |                  |                |        | 切片语法: `str[start:end]` 左闭右开          |
| 拼接       | 1. `str1+str2`<br>2. 模板字符串内插                                                                       |                  |                |        | `str1 + str2`                                |
| to 数组    | String#split(pattern=nil, [limit])                                                                        |                  |                |        | 字节: `byte(str)`<br>字符: `[]rune(str)`     |
| from 数组  | Array#join(separator=$,)                                                                                  |                  |                |        | `string(byte_or_rune_array)`                 |
| 字符串模板 | 支持                                                                                                      | ES6支持          |                |        | 支持                                         |

---

### Ruby

* 字符串可变

  > str[-1] = 'hi'  
  > str[2, 5] = 'hello'  
  > str[5, 6] = '' //相当于删除

  每次遇到一个字符串字面量, 都会新建一个字符串对象

  最佳实践: 减少在循环中使用字符串字面量

* 单双有别

  字符串字面量同时支持单引号和双引号, 双引号支持模板字符串, 双引号不支持

  最佳实践: 在不需要模板字符串的地方, 始终使用单引号


* 拼接

  * `str + other_str → new_str`: 不会自动转换类型, 要求操作数都是字符串, 返回新对象
  * 模板字符串内测: 会自动调用`to_s`, 不创建新对象, 效率更高
  * `str << integer → str` 数字将解释为码点  
    `str << obj → str` 拼接前会自动转换右值
  * `str * integer → new_str`

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

  * 双引号字符串支持模板字符串

    `"PI is #{Math::PI}" => "PI is 3.141592653589793"`

  * 单引号字符串不支持模板字符串

  * Here document

    * 以`<<`或者`<<-`开头
    * 后面紧跟标识符字符串
    * 从下一行开始到结束标识符字符串中内容, 都是字符串字面量内容
    * 结尾相同的字符串结束符需要独占一行, 连注释也不能有

    ```ruby
    def print_heredoc(v)
      puts <<-EOF
        this is the first line #{v}
        this is the second line
      EOF
    end

    print_heredoc(1)

    输出:
    this is the first line 1
    this is the second line
    ```

    * here doc 默认支持模板字符串, 类似一个双引号的字符串
    * 如果使用单引号围绕标识符, 那么含义类似单引号的字符串, 不支持模板字符串

    ```ruby
    def print_heredoc(v)
      puts <<-'EOF' #这里使用了单引号, 将不支持变量内插
        this is the first line #{v}
        this is the second line
      EOF
    end

    print_heredoc(1)

    输出:
    this is the first line #{v}
    this is the second line
    ```

    另外here doc作为参数还有些使用技巧, 参考 [Ruby 多行字符串 heredoc 详解](https://ruby-china.org/topics/25983)

* Javascript

  ES6:

  > <code>`</code>${start}, my name is ${getName()}, ${conf.fav} is my favourite<code>`</code>

* Lua

* Java

* Go

---

## 字符编码

# 编码

### Ruby



---

### Javascript

<http://www.alloyteam.com/2016/12/javascript-has-a-unicode-sinkhole/>
 <http://www.jeffjade.com/2016/11/24/116-JavaScript-string-operation>

---

### Lua

---

### Java

---

### Go


