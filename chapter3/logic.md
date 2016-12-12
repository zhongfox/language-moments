# 3.6 逻辑运算

### Ruby

---

### Javascript

* 逻辑false

  > false, "", 0, NaN, null, undefined

* 逻辑true: 除了上述6值以外的所有值

* 逻辑运算

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
      flag ||= defaultFlag()
      ...
    }
    ```

    不过要注意, javascript中逻辑假的值有6个(见上)





---

### Lua

---

### Java

---

### Go

