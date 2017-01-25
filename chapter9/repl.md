# 9.1 REPL

> REPL: read–eval–print loop

---

### Ruby

安装ruby自带REPL: `irb`, 表示 interactive ruby

---

### Javascript

* Chrome: `开发者工具`>`Console`

* Node.js 自带REPL: `node`

---

### Lua

自带REPL: `lua`

* 参数 `-e` 执行字符串语句
* 参数`-i`交互模式 :`%lua -i hello_world.lua` 表明执行完此文件后打开交互Lua, 对调试非常有用
* dofile 动态加载: 如在交互Lua中`> dofile("path_to_your_luafile.lua")` 该文件就会加载到交互Lua中
* 在REPL中, 一条完整的语句是一个chunk, 因此局部变量在语句之间是无法共享的. 如果需要共享, 可以把多条语句`do end`代码块中.

---

### Go

第三方扩展: [gore](https://github.com/motemen/gore)

---

<!--
### Java

* 第三方扩展: [java-repl](http://github.com/albertlatacz/java-repl)

  MacOS 可以通过brew安装: `brew install javarepl`

* JAVA9: jshell

-->
