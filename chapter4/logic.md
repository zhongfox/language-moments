# 3.6 逻辑运算

|              | Ruby                   | Javascript                    | Go                          | Lua             |
|--------------|------------------------|-------------------------------|-----------------------------|-----------------|
| 三元逻辑运算 | 支持                   | 支持                          | 不支持                      | 不支持          |
| 逻辑false    | false,nil              | false,null,undefined,'',0,NAN | false<br>逻辑运算只能用bool | fasle, nil      |
| 逻辑赋值运算 | Y<br>x &#124;&#124;= 5 | N<br>x = x &#124;&#124; 5     | 不支持                      | N<br>x = x or 5 |


短路运算: 在逻辑运算中, 对最后结果已经确定的表达式中, 不再执行后续的子表达式, 直接返回结果. 

具体的情况是:
* 在逻辑与表达式中, 已经有前置表达式为假, 不再执行后续表达式
* 在逻辑或表达式中, 已经有前置表达式为真, 不再执行后续表达式

对于逻辑运算, 动态类型语言通常返回表达式实际值, 而静态类型语言总是返回布尔值

---

### 1. Ruby

* 逻辑false

  只有2个值: false, nil

* 逻辑true: 除了上述2个值以外的所有值

#### 逻辑运算

* 短路运算
* 返回值是2个操作值其中之一, 而不一定是返回布尔值

* 逻辑与`&&`:
  ```ruby
  Im_false && this_will_not_run #短路
  Im_ture && will_get_my_value  #返回实际值
  1 && 2   #=> 2
  nil && 2 #=> ''
  ```

  在实际编码中, 有时使用逻辑与代替if操作:
  ```ruby
  like_it? && buy_it!
  # 等价于 buy_it! if like_it?
  ```

* 逻辑非`||`:
  ```ruby
  Im_false || will_get_my_value #短路
  Im_ture || this_will_not_run  #返回实际值
  1 || 2   #=> 1
  nil || 2 #=> 2
  ```

  在实际编码中, 利用逻辑或实现默认值:

  ```ruby
  flag ||= default_flag
  ```

  不过Ruby中`x ||= 5` 和 `x = x || 5` 稍有不同, 在x为逻辑false的情况下, 前者不会发生赋值操作, 但是后者无论如何都会发生赋值操作

* ruby 还有一组逻辑运算: `and` `or`, 需要注意的是, 它们也是短路运算, 不过它们的优先级比较低

---

### 2. Javascript

* 逻辑false

  > false, "", 0, NaN, null, undefined

* 逻辑true: 除了上述6值以外的所有值

#### 逻辑运算

* 短路运算
* 返回值是2个操作值其中之一, 而不一定是返回布尔值

* 逻辑与`&`:
  ```javascript
  ImFalse() && thisWillNotRun()
  ImTure() && willGetMyValue()
  1 && 2 //=> 2
  '' && 2 //=> ''
  ```

* 逻辑非`||`:

  ```javascript
  ImFalse() || willGetMyValue()
  ImTure() || thisWillNotRun()
  1 || 2 //=> 1
  '' || 2 //=> 2
  ```

  在实际编码中, 经常利用逻辑非的短路特性, 实现函数参数的默认值:

  ```javascript
  function (flag) {
    flag = flag || defaultFlag()
    ...
  }
  ```

  不过要注意, javascript中逻辑假的值有6个(见上)

* 需要注意Javascript里没有`||=`, 只能写出 `x = x || default`


---

### 3. Go

Go没有隐式类型转换, 内置也没有提供其他类型转为bool的方法, 因此在条件判断, 逻辑运算中只能使用真正的bool值, 连nil都不能使用

```go
1 && nil       // 报错
0 || ""        // 报错
bool(0)        // 报错
if (nil) {...  // 报错
```

---

### 4. Lua

Lua 中没有三元运算符, 很多资料提供的变通方法是:

> x = a and b or c

但其实并不准确, 如果a为true, b 是 nil, 以上方式执行将返回c, 但是`true ? nil : c`期望的返回是nil. 如果的确希望用and/or进行变通, 可以这样写:

> (a and {b} or {c})[1]

不过可读性比较差, 还不如直接写if else.


---
