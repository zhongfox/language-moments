# 4.6 拆聚

|              | Ruby | Javascript ES6 | Go | Lua |
|--------------|------|----------------|----|-----|
| 并行赋值     | Y    | Y              | Y  | Y   |
| 函数参数拆聚 | Y    | Y              | Y  | Y   |
| 函数多返回值 | Y    | N              | Y  | Y   |

多值的拆解/聚合主要可以用在:

* 并行赋值/解构赋值
* 函数变长参数列表
* 函数多返回值
* 数组展开为多个实参

在ruby, ES6中都有拆聚操作符, ruby中是`*`, ES6中是`...`, 表现形式基本一致:

* 拆聚操作符+变量: 表示将把传入/匹配的多个单值, 聚合为一个数组, 赋给此变量
* 拆聚操作符+数组: 表示数组将被展开多个单值

Go, Lua 的函数多返回值, 是分离的值, 可以作为另外一个函数的调用实参, 或者赋给右值, Ruby的函数多返回值, 是聚合到一个数组里, 也可以赋值给右值, 但是如果希望作为另一个函数的调用实参, 需要按需打散.

TODO 补充一个例子

---

### 1. Ruby

* 并行赋值:
  ```ruby
  x, y, z = 1, 2, 3
  x, y, z = [1, 2, 3] #同上
  x = 1, 2, 3         # x = [1, 2, 3]
  x, = 1, 2, 3        # x =1 类似模式匹配
  x, y = 1, 2, 3      # x=1, y=2 多余的右值丢弃
  x, y, x = 1, 2      # x=1, y=2, z=nil 匹配不成功的就是nil
  x, (y, z) = a, b    # x = a; y, z = b
  ```

* 聚

  * 赋值聚合
    ```ruby
    x, *y, z = 1, 2, 3, 4      # x=1, y=[2, 3], z=4
    ```

  * 函数变长参数列表
    ```ruby
    def max(first, *rest) # * 起到聚合多个实参的作用
      max = first
      rest.each {|x| max = x if x > max}
      max
    end
    max(1, 2, 3) #3
    ```

* 拆

  * 赋值展开
    ```ruby
    x, y, z, k = 1, *[2, 3], 4 # x, y, z, k = 1, 2, 3, 4
    ```

    不过拆聚操作符`*some_value` 不能作为独立的表达式而存在

  * 数组拆为多个实参
    ```ruby
    data = [1, 2, 3]
    m = max(*data)
    ```

---

### 2. Javascript

ES5: 伪数组arguments可以用于实现变长参数列表

ES6:

* 并行赋值
  ```javascript
  let [a, b, c] = [1, 2, 3];
  let [foo, [[bar], baz]] = [1, [[2], 3]]; //foo=1, bar=2, baz=3 基于模式匹配
  let [x, , y] = [1, 2, 3];                // x=1, y=3 基于模式匹配
  let [x, y] = [1, 2, 3];                  // x=1, y=2 多余右值丢弃
  let [x, y] = ['a'];                      //x="a", y=undefined  匹配不到就是undefined
  ```

* 聚:

  * 赋值聚合
    ```javascript
    const [head, ...tail] = [1, 2, 3, 4];   //head=1, tail=[2, 3, 4]
    ```

  * 函数变长参数列表
    ```javascript
    const getOptions = function(...args){ //args真数组
    ```

* 拆:

  * 赋值展开
    ```javascript
    const opts = ['one', 'two', 'three', 'four'];
    const config = ['other', ...opts];
    ```

  * 数组拆为多个实参
    ```javascript
    const numbers = [1, 2, 3];
    max(...numbers);
    ```

---

### 3. Go

* 并行赋值
  ```go
  x, y := 1, 2
  ```

* 拆:
  ```go
  append([]byte("abc"), "xyz"...)         //拆字符串
  append([]byte("abc"), []byte("abc")...) //拆数组
  ```

* 聚:
  ```go
  func (slice_inside ...int)
  ```

---

### 4. Lua

* 并行赋值
  ```lua
  a, b = 10, 20
  ```

* 聚

  * 函数变长参数列表:
    ```lua
    function select (n, ...)
      return arg[n]
    end

    select(2, 10, 20, 30) -- 20
    ```

    变成参数聚合到指定命名的table arg中, arg中还有一个域n表示参数的个数

* 拆

  * 数组拆为多个实参, 利用内置函数`unpack`
    ```lua
    data = {10, 20, 30}
    select(2, unpack(data)) -- 20
    ```

    在5.2中使用`table.unpack`, 也可以手动指定需要的范围.
    ```lua
    table.unpack({10, 20, 30}) -- 10 20 30

    print(table.unpack({10,20,30,40}, 2, 3)) -- 20, 30 左闭右闭
    ```

* 函数多返回值

  ```lua
  function foo0 () end
  function foo1 () return 'a' end
  function foo2 () return 'a','b' end

  x,y = foo2()         -- x='a', y='b'
  x = foo2()           -- x='a', 'b' is discarded
  x,y,z = 10,foo2()    -- x=10, y='a', z='b'
  x,y = foo0()         -- x=nil, y=nil
  x,y = foo1()         -- x='a', y=nil
  x,y,z = foo2()       -- x='a', y='b', z=nil

  x,y = foo2(), 20     -- x='a', y=20
  x,y = foo0(), 20, 30 -- x='nil', y=20, 30 is discarded
  ```

  如果做为另一个函数的参数的函数调用不是最后一个参数的话, 此函数返回值将只有第一个; 多值函数在表达式中会自行调整适应.

  特别的是, 函数调用可以直接用于初始化table, 同样的, 只有最后一个函数调用的多值才完全保留, 其他位置的多值只保留第一个.

  ```lua
  a = {foo0()}  -- a = {} (an empty table)
  a = {foo1()}  -- a = {'a'}
  a = {foo2()}  -- a = {'a', 'b'}

  a = {foo0(), foo2(), 4} -- a[1] = nil, a[2] = 'a', a[3] = 4
  ```

  多返回值可以使用`()`保留第一个值:

  ```lua
  print((foo2())) --> a
  ```

---

[comment]: <> (补充其他函数多返回值)
