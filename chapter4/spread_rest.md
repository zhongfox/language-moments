# 4.6 拆聚

多值的拆解/聚合主要可以用在:

* 并行赋值/解构赋值
* 函数变长参数列表
* 数组展开为多个实参

在ruby, ES6中都有拆聚操作符, ruby中是`*`, ES6中是`...`, 表现形式基本一致:

* 拆聚操作符+变量: 表示将把传入/匹配的多个单值, 聚合为一个数组, 赋给此变量
* 拆聚操作符+数组: 表示数组将被展开多个单值

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

