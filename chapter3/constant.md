# 3.4 常量

|          | Ruby                 | Ruby               | Javascript ES5                             | Javascript ES6 | Go                                                            | Lua    |
|----------|----------------------|--------------------|--------------------------------------------|----------------|---------------------------------------------------------------|--------|
| 实现     | 大写字母开头的是常量 | Module.freeze      | ES5:不支持, 替代方案: `Object.freeze(obj)` | ES6: const     | const<br>常量在预处理阶段直接展开, 作为指令数据<br>类型有限制 | 不支持 |
| 修改     | 允许                 | 不允许(报错)       | 不允许(无异常)                             | 允许           | 无法修改                                                      | -      |
| 重新赋值 | 允许, 会有warning    | 允许               | 允许                                       | 不允许(报错)   | 不允许                                                        | -      |


可以看到常量这个术语在不同语言中有不同的表现, 使用常量的语境有很多, 比如: 固定值, 已知值, 减少魔数, 减少字符串字面量, 统一配置等等, 不过在具体使用时, 还应该考虑的是, 这个常量是否可以被修改, 以及这个常量标识符是否可以被重新赋值.

比如在Javascript ES6中, 我们希望一个值不能被修改, 也不能被重新赋值, 最好的选择是

> const Version = Object.freeze({major: 1, minor: 2})

而在Ruby最好的方式是这样:

> Version = {major: 1, minor: 2}.freeze #不过还是不能阻止重新赋值

---

### 1. Ruby

#### 常量查找

Ruby 常量查找并不是简单的从继承链继承，总的来说，常量查找根据以下三个规则来：

1. 查找当前环境的 `Module.nesting`, 这会是一个模块（类也是模块）的数组。
2. 如果 #1 找不到，且 `Module.nesting.first` 是一个类，那么查找 `Module.nesting.first.ancestors`
3. 如果 #1 和 #2 都找不到， 那么查找`Object.ancestors` [Object, Kernel, BasicObject]

虽然规则很简单明了，但是还是有很多需要说的：

1. 前缀式的模块嵌套，并不会计入`Module.nesting`里， 比如：

  ```ruby
  module A;
    B = 10;
  end

  module A::C
    puts Module.nesting # [A::C]
    puts B              # uninitialized constant A::C::B
  end
  ```
  如果把换成一下格式就OK了

  ```ruby
  module A;
    B = 10;
  end

  module A
    module C
      puts Module.nesting # [A::C, A]
      puts B              # 10
    end
  end
  ```

2. 关于规则2，一个常见的错误是认为，在ancestors里查找是从self.class开始， 正确的是从`Module.nesting.first.ancestors.first`开始：

  ```ruby
  class A
    def get_c;
      puts self                                 #b
      puts self.class                           #B
      puts Module.nesting.first.ancestors.first #A
      puts C;                                   #uninitialized constant A::C
    end
  end

  class B < A
    C = 10
  end

  b = B.new
  b.get_c
  ```

  这个例子说明`puts C` 是查找的`A::C`(Module.nesting.first.ancestors.first) 而不是`B::C`(self.class)

3. 在顶层 Module.nesting 为空，顶层的常量定义和查找是在Object里

4. `class_eval, module_eval, instance_eval, define_method` 不会改变Module.nesting， 也就不会改变常量查找

5. 在本体类里查找常量，并不会查到这个原类的继承链，因为本体类的继承链是从Class开始的：
  ```ruby
  class A
    B = 10
  end

  class C < A

    class << C
      puts Module.nesting # [#<Class>, C]
      puts ancestors      # 1.9.3 [Class, Module, Object, Kernel, BasicObject]
                          # 2.1.1 [#<Class:C> #<Class:A> #<Class:Object> #<Class:BasicObject> Class Module Object Kernel BasicObject]
      puts B              # uninitialized constant Class::B
    end
  end
  ```
   这是一个很常见的错误，以为puts B 应该输出A::B

6. 其他:

  * Ruby常量不能在方法中赋值, 常量在赋值后才会存在, 因此常量绝对不会处于未初始化的状态
  * `Module#constants` 返回module的所有第一层级常量
  * `Module#constants` 返回所有顶级常量

---

### 2. Javascript

#### ES6 var, let, const 如何选择

首先在ES6中基本可以放弃使用var, 使用let/const总是更好的选择, 使用let/const可以避免var带来的一些糟粕, 比如变量作用域提升, 重复定义导致变量覆盖等.

而默认应该使用let还是const, 在社区还是有一定分歧的, 有两种不同的观点:

1. 默认全用 let，只在符合一些写代码的人的主观判断条件的时候用 const，下面举个这样的“主观判断条件”的例子（实际代码中用到 const 的几率大概会是 0.1%）
2. 默认全用 const，只有该变量需要被重新赋值才用 let （实际代码中用到 const 的几率大概会是 95%）

可以参考:

* [用 const 还是用 let？](http://www.cnblogs.com/ziyunfei/p/6038213.html)
* [ES6 中 的 var、let 和 const 应该如何选择？](http://www.ituring.com.cn/article/261278)

---

### 3. Go

```go
const x = 100  
const y string = 100
```

* 赋值必须是编译器可确定的字符, 字符串, 数字或布尔值
* 常量在预处理阶段直接展开, 作为指令数据, 无法取地址
* 不曾使用的常量不会引发编译错误

---

### 4. Lua

Lua 原生没有常量功能, 可以利用元表进行属性设置拦截:

```lua
function newConst( const_table )    --生成常量表功能
  local function Const( const_table )
    local mt = {
      __index = function (t, k) return const_table[k] end,
      __newindex = function (t, k, v)
        print("*can't update " .. tostring(const_table) .."[" .. tostring(k) .."] = " .. tostring(v))
      end
    }
    return mt
  end

  local t = {}
  setmetatable(t, Const(const_table))
  return t
end

local x = {}
newConst(x)

x.a = 3 -- *can't update table: 0x7fa469701490[a] = 3
```

---

参考资料:

* [Lua Immutable Objects](http://lua-users.org/wiki/ImmutableObjects) <!--todo-->
