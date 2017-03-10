# 3.10 数组

|                | Ruby                                       | Javascript                                | Go                                                | Lua                                 |
|----------------|--------------------------------------------|-------------------------------------------|---------------------------------------------------|-------------------------------------|
| 起始索引       | 0                                          | 0                                         | 0                                                 | 1                                   |
| 倒序索引       | `arr[-1]`                                  | 不支持                                    | 不支持                                            | 不支持                              |
| 索引越界       | nil                                        | undefined                                 | panic                                             | nil                                 |
| 长度           | `length`<br>`size`                         | `属性 length`                             | 内置函数`len()`                                   | `#`<br>`table.getn(t)`              |
| 长度可写       | 不支持                                     | `arr.length = 3`                          | 不支持                                            | 不支持                              |
| 追加           | `Array#<<`<br> `push`                      | `arr.push([element1[, ...[, elementN]]])` | `func append(slice []Type, elems ...Type) []Type` | `table.insert (list, [pos,] value)` |
| 连接           | `Array#+(other)`<br>`concat(ary1,ary2...)` | `concat(ary1,ary2...)`                    | `append(slice1, slice2...)`                       | 自行实现                            |
| 数组字面量容器 | `[]`                                       | `[]`                                      | `{}`                                              | `具体数组类型{}`                                |

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

<!--TODO array的复制: http://stackoverflow.com/questions/3978492/javascript-fastest-way-to-duplicate-an-array-slice-vs-for-loop-->

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

Array.prototype 新增了三个方法, entries keys 和 values 都可以返回一个[遍历器对象](/chapter4/iterator.html), 分别可以遍历键名, 键值, 以及键值对.

---

### 3. Go

#### Array

* 数组是一个值类型（value type）

  所有的值类型变量在赋值和作为参数传递时都将产生一次复制动作

  如果将数组作为函数的参数类型, 则在函数调用时该参数将发生数据复制. 因此, 在函数体中无法修改传入的数组的内容, 因为函数内操作的只是所传入数组的一个副本, 如需修改, 可以传递数组指针或者数组的切片.

* 长度是数组类型的一部分

  定义类型时居然要带上长度: `func modify(array [10] int) {...`

  数组长度在定义后就不可更改(len 不可变, 不能使用append方法, 但是内部数据可以变)

  数组 len 和cap等值

* 初始化:

  ```go
  a := [5] int{1,2,3}
  b := [...]int{1,2,3,4} //自动长度
  c := [...]int{1: 10, 2: 20, 4: 40} //[0 10 20 0 40]
  ```

<!--
* 结构体中的数组属性, 初始化时可以省略类型
type Queue struct {
  list [3]int
}

q := Queue{
  list: {1, 2, 3}, //Missing type in composite literal
}

fmt.Println(q)
-->

* 比较:

  如果元素支持`==` `!=` 数组也支持, 如果两个数组的对应元素比较都相等, 则数组比较为相等, 否则为不等.

  如果元素不支持比较, 则数组比较会报错.

* 数组的指针和数组第一个元素的指针相同

  数组指针可以用于操作元素: `&someArray[1]`

#### slice

* 切片并非数组指针, 它有指向底层数组的独立数据结构, 限定读写范围

* 切片访问起始索引从0开始, 超过index的读写会报错: 'index out of range'

* 通过已有数组创建切片:

  * len, cap不能超出原始数组的范围, cap不能小于len
  * `someArray[开始索引:结束索引:cap索引]`, 这种数组必须是addressable的, 比如map中的数组就不是addressable
  * `cap索引`可以省略, 右开
  * `开始索引:结束索引` 左闭右开
  * len: 可读元素, `结束索引-开始索引`

* 通过切片创建切片: 受原始切片cap影响

* 切片修改对所有关联切片可见

* 初始化变量:

  `var a []int` 没初始化的slice零值是nil

  `make([]int, len, cap)` len必要, cap可省,  该数据不为nil

* 不支持比较运算

* slice 可以取地址, 但是地址和第一个元素的地址是不同的, slice的地址不能支持索引操作, 如`(&someslice)[0]`报错

* `append(someslice, item ...Type)`

  * 如果是基于底层数组, 不能超过底层数组的len
  * 如果超过本身的cap, 重新分配底层数组, cap将double(不精确), 因此性能有影响
  * 返回新的slice, 新旧slice指针不同, len, cap也是独立的, 但是底层数组相同

<!--
* copy(des, src)
-->

---

### 4. Lua

Lua 使用table来实现数组, Lua 中叫做列表(list), 索引从1开始.

> days = {"Sunday", "Monday", "Tuesday", "Wednesday",  
>              "Thursday", "Friday", "Saturday"}

table 中如果有nil, table的长度是不确定的. 因此在使用Lua list时, 尽量避免空洞nil.

如果要删除数组中指定位置的值, 建议不要直接赋值nil, 这样会造成空洞, 推荐使用`table.remove (list [, pos])`, table.remove 在删除元素后, 会把后置的元素依次前移, 避免空洞.


---

<!--
lua: table.remove 而不要用赋值nil, 赋值nil会造成空洞list

-->
