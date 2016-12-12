# 3.2 变量

|            | Ruby                                | Javascript                                                         | Lua                | Java | Go           |
|------------|-------------------------------------|--------------------------------------------------------------------|--------------------|------|--------------|
| 类型       | 弱类型                              | 弱类型                                                             |                    |      |              |
| 未声明变量 |                                     | 不可以直接使用, `typeof 返回 undefined`                            |                    |      |              |
| 未赋值初值 | nil                                 | undefined                                                          |                    |      | 二进制零值   |
| 变量作用域 | `class` `module` `def` 开启新作用域 | ES5: 函数开启闭包作用域<br>ES6: 增加`let` `const` 遵循块`{}`作用域 | 函数开启闭包作用域 |      | 块作用域`{}` |
| Evaluation |                                     | 值传递                                                             |                    |      | 值传递       |

---

### Ruby

---

### Javascript

* primitive 需要的内存空间固定, 存于stack, 直接存储在变量访问的位置, 传递时复制值
* 引用类型需要的内存空间可变, 存于heap, 传递时复制heap内存地址
* 引用类型(Object)的内存地址(引用)是固定大小的, 因此对象的引用是存于stack
* primitive 类型无法动态增加属性, 引用类型可以

#### 复制: 值拷贝

* Javascript复制是执行的值拷贝

  对于primitive来说, 「值」是stack中的内容数据, 而对于Object来说, 「值」是对象的引用

  因此看起来结果类似这样: primitive传递的是值, 而Object传递的是「引用」

  ```javascript
  function change(num, obj) {
    num += 1;
    obj.name = 'fox';
  }

  var primitiveValue = 5;
  var referenceVlaue = {};

  change(primitiveValue, referenceVlaue);
  console.log(primitiveValue); //5, primitive的值没有变化
  console.log(referenceVlaue); //{ name: 'fox' } Object值发生变化
  ```

---

### Lua

---

### Java

---

### Go

