# 8.2 模块

### 1. Ruby

Ruby语言本身有module的概念, 但没有文件模块的概念, 一个Ruby源文件可以有若干个module, Ruby源文件并没有显示的导出功能, 源文件加载后的数据导出, 依赖语言定义的命名空间和作用域.

---

### 2. Node.js

模块分为核心模块和文件模块:

* 核心模块: 编译为二进制的常用模块, 随node一起发行. 核心模块总是优先加载.
* 文件模块: 用户自编写的node源文件, 在Node.js中, 源文件即是模块.

模块在执行之前, Node会将文件内容包裹到一个function中, 形如:

```javascript
(function (exports, require, module, __filename, __dirname) {
// Your module code actually lives in here
});
```

包裹function实现了以下几个目的:

* 顶层变量局限于本模块内, 不干扰全局变量
* `exports` `module` 用于模块导出
* `__filename` `__dirname`包含本模块的绝对路径.

---

### 3. Go

Go 语言没有文件模块概念, 源文件都属于某个包.

---

### 4. Lua

Lua 源文件可以作为模块, 模块文件约定是返回一个全局的table, 模块文件如果没有显示return, 返回值将是true.

模块定义的几种格式:

1. 最原始: 写死模块名, 完整模块名为内部调用命名空间

  ```lua
  complex = {}
  function complex.new (r, i) return {r=r, i=i} end
  -- defines a constant `i'
  complex.i = complex.new(0, 1)
  function complex.add (c1, c2)
     return complex.new(c1.r + c2.r, c1.i + c2.i)
  end
  function complex.sub (c1, c2)
     return complex.new(c1.r - c2.r, c1.i - c2.i)
  end
  return complex
  ```

2. 统一内部调用命名空间(不依赖固定模块名)

  ```lua
  local P = {}
  complex = P -- package name
  P.i = {r=0, i=1}
  function P.new (r, i) return {r=r, i=i} end function P.add (c1, c2)
     return P.new(c1.r + c2.r, c1.i + c2.i)
  end
  return complex
  ```

3. (文件名)动态模块名称, 便于模块改名

  ```lua
  --将模块名设置为require的参数，这样今后重命名模块时，只需重命名文件名即可。
  local modname = ...
  local M = {}
  _G[modname] = M

  M.i = {r = 0, i = 1}  --定义一个模块内的常量。
  function M.new(r,i) return {r = r, i = i} end
  function M.add(c1,c2)
     return M.new(c1.r + c2.r,c1.i + c2.i)
  end

  function M.sub(c1,c2)
     return M.new(c1.r - c2.r,c1.i - c2.i)
  end
  --返回和模块对应的table。
  return M
  ```

4. 去掉内部命名空间前缀(javascript前端代码也经常使用这样的方式)

  ```lua
  local function new (r, i) return {r=r, i=i} end
  local function add (c1, c2)
     checkComplex(c1);
     checkComplex(c2);
     return new(c1.r + c2.r, c1.i + c2.i)
  end
  ...
  --export
  complex = {
     new = new,
     add = add,
     sub = sub,
     mul = mul,
     div = div,
  }
  return complex
  ```

---

参考资料:

* <https://nodejs.org/api/modules.html>
