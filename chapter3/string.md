# 3.9 字符串

|            | Ruby                                                                           | Javascript                                             | Go                                       | Lua                                                            |
|------------|--------------------------------------------------------------------------------|--------------------------------------------------------|------------------------------------------|----------------------------------------------------------------|
| 存储       | 1.8 字节序列<br>1.9 字符序列                                                   | UCS-2                                                  | Unicode                                  | 8位字节                                                        |
| 可变性     | 可变<br>str[-1]='hi'                                                           | 不可变                                                 | 不可变                                   | 不可变                                                         |
| 字面量     | 单引号, 双引号<br>单双有别, 双引号支持模板                                     | 单引号, 双引号                                         | 双引号:支持转义<br>反引号:不支持转义     | 单引号, 双引号                                                 |
| 长度       | 字节: bytesize<br>字符:length size                                             | 字符: length属性                                       | 字节: len(str)<br>字符: len([]rune(str)) | 字符: #                                                        |
| 遍历       | 字节: each_byte<br>字符:each_char<br>字符码:each_codepoint<br>行遍历:each_line | 字符: for i<br>ES6: for of                             | 字节: for i<br>字符: for range           | for i = 1, #str do ... end                                     |
| 索引访问   | str[i]<br>倒序索引: str[-1]                                                    | str[i] charAt[i]<br>ES6: at(i)                         | str[i]                                   | string.sub(str,start,end)<br>左闭右闭,索引从0开始,支持负数倒序 |
| 索引越界   | nil                                                                            | undefined                                              | panic                                    | 空字符串                                                       |
| 片段       | str[start, len]<br>str(start..end)                                             | substring(start,end)左闭右开<br>substr(start[,length]) | 切片语法: str[start:end] 左闭右开        | string.sub(str,start,end)                                      |
| 拼接       | str1+str2<br>模板字符串内插<br>`String#<<`                                     | str1+str2<br>concat(str1,...)                          | str1 + str2                              | str1+str2                                                      |
| to 数组    | split(pattern=nil, [limit])                                                    | split([separator[, limit]])                            | 字节: byte(str)<br>字符: []rune(str)     | 无内置实现                                                     |
| from 数组  | Array#join(separator=$,)                                                       | Array#join([separator])                                | string(byte_or_rune_array)               | table.concat (list [, sep [, i [, j]]])                        |
| 字符串模板 | 支持                                                                           | ES6支持                                                | 支持                                     | 无内置实现                                                     |

---

### 1. Ruby

#### 字符串可变

```ruby
str[-1] = 'hi'
str[2, 5] = 'hello'
str[5, 6] = '' //相当于删除
```

每次遇到一个字符串字面量, 都会新建一个字符串对象

**最佳实践**: 减少在循环中使用字符串字面量

#### 单双有别

字符串字面量同时支持单引号和双引号, 双引号支持模板字符串, 双引号不支持

**最佳实践**: 在不需要模板字符串的地方, 始终使用单引号


#### 拼接

* `str + other_str → new_str`: 不会自动转换类型, 要求操作数都是字符串(体现了Ruby的强类型), 返回新对象
* 模板字符串内插: 会自动调用`to_s`, 不创建新对象, 效率更高
* `str << integer → str` 数字将解释为码点  
  `str << obj → str` 拼接前会自动转换右值
* `str * integer → new_str`

**最佳实践**: 优先使用模板字符串

---

### 2. Javascript

ES6 对字符串进行了很多扩展.

#### 字符串模板

```javascript
`${start}, my name is ${getName()}, ${conf.fav} is my favourite`
```

#### 遍历器接口

```javascript
for (let char of '语言的学习') {
  console.log(char);
}
```

---


### 3. Go

> string is the set of all strings of 8-bit bytes, conventionally but not necessarily representing UTF-8-encoded text. A string may be empty, but not nil. Values of string type are immutable.

字符串是一系列8位字节的集合，通常但不一定代表UTF-8编码的文本。字符串可以为空，但不能为nil。而且字符串的值是不能改变的, 定义大致如下:

```go
type stringStruct struct {
  str unsafe.Pointer
  len int
}
```

其中str指向了一个byte数组.

* 内建的len方法可以得到字符串的长度，每个字节可以根据索引得到，不能像 C 语言一样得到某个字节的地址，&s[i]这样做是非法的.

* 加法拼接字符串, 每次需要重新分配内存, 在构建超大字符串时, 性能低下

  优化: 使用`strings.Join(a []string, sep string)` 会一次性计算并分配需要的内存

#### []byte

byte是uint8, 定义如下

```go
type slice struct {
	array unsafe.Pointer
	len   int
	cap   int
}
```

#### string和[]byte 的区别

* string可以直接比较，而[]byte不可以，所以[]byte不可以当map的key值
* 因为无法修改string中的某个字符，需要粒度小到操作一个字符时，用[]byte
* string值不可为nil，所以如果你想要通过返回nil表达额外的含义，就用[]byte
* []byte切片这么灵活，想要用切片的特性就用[]byte
* 需要大量字符串处理的时候用[]byte，性能好很多

string和[]byte的相互转换:
* 将string转为[]byte: `[]byte(string)`
* 将[]byte转为string: `string([]byte)`

---

### 4. Lua

#### 单双有别

* 单引号字符串不解释反斜杠转义序列
* 双引号字符串解释反斜杠转义序列

Lua 标准库的吝啬, 在string的API上可见一斑, 很多其他语言操作字符串的API, 在lua中都没有原生的支持, 不过通常在lua社区都可以找到实现:

* 字符串split: <http://lua-users.org/wiki/SplitJoin>
* 字符串模板: <http://lua-users.org/wiki/TextTemplate> <http://lua-users.org/wiki/StringInterpolation>

---

参考资料:

* [lua-users Sample Code](http://lua-users.org/wiki/SampleCode) 有很多不错的lua扩展
* [golang string和[]byte的对比](https://sheepbao.github.io/post/golang_byte_slice_and_string/)







<!--


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

ES6 对js的字符串进行了扩展支持, 参见[Unicode与JavaScript详解](http://www.ruanyifeng.com/blog/2014/12/unicode.html)
---

### Lua

[[
不解释转义序列
]]

---

### Java

---

### Go

-->
