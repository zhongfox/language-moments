# 8.3 导入

|            | Ruby                | Node.js                                         | Go                                          | Lua                                 |
|------------|---------------------|-------------------------------------------------|---------------------------------------------|-------------------------------------|
| 缓存加载   | require             | require                                         | import                                      | require                             |
| 缓存       | $LOADED_FEATURES    | require.cache                                   | $GOPATH/pkg/                                | package.loaded                      |
| 非缓存加载 | load                | 无                                              | 无                                          | dofile                              |
| 搜索路径   | $LOAD_PATH:全局固定 | module.paths:当前文件相关<br>NODE_PATH:全局固定 | 递归vendor:当前文件相关<br>$GOPATH:全局固定 | package.path/package.cpath:全局固定 |

---

### 1. Ruby
* `require 相对路径/绝对路径`: 直接寻找绝对路径文件加载, 并加入`$LOADED_FEATURES`
* `require 模块标识`: 从`$LOAD_PATH`中寻找文件并加载, 加入`$LOADED_FEATURES`. 这是ruby加载gem的正确姿势
* `require_relative 相对路径`: 相对于当前执行文件, 寻找相对路径的文件并加载, 加入`$LOADED_FEATURES`
* `load 模块标识/绝对路径.rb` 从`$LOAD_PATH`中寻找, 需要扩展名, 但是不加入`$LOADED_FEATURES`, 可以重复加载

|                            | require                | require_relative | load                   |
|----------------------------|------------------------|------------------|------------------------|
| 参数                       | 相对/绝对路径/模块标识 | 相对路径         | 相对/绝对路径/模块标识 |
| 利用`$LOADED_FEATURES`缓存 | Y                      | Y                | N                      |
| 省略扩展名                 | 允许                   | 允许             | 不允许                 |

和源文件加载相关的2个全局变量:

* `$:`/`$LOAD_PATH`:  default search path (array of paths)
* `$"`/`$LOADED_FEATURES` 已经加载过的文件

#### require:

`Kernel#require(name) → true or false`

若是相对路径，则从全局变量`$LOAD_PATH`中查找, 若省略扩展名则按以下顺序查找[.rb,.so,.dll].

rb文件作为源文件载入, 其它作为扩展载入.

已经载入的文件存放于`$LOADED_FEATURES`中, require不会载入已存在于`$LOADED_FEATURES`中的文件，如果载入成功返回true，否则false

#### require_relative

`require_relative(string) → true or false`

这个相对关系是相对于调用`require_relative`的文件，因此如果在irb(不在文件里)里调用会得到`LoadError: cannot infer basepath`, 加载成功后返回true, 把文件放入`$LOADED_FEATURES`

重复加载返回false, 文件不存在报错.

ruby1.9.2 中因为安全的原因从$LOAD_PATH移除了当前目录, 因此不能用require去加载当前目录文件, 引入的`require_relative` 则是用来加载当前目录文件的方法

require 和require_relative的区别在于引入类似`require 'mytest.rb'` 这种形式，对于`require './mytest.rb'` 都可以成功加载相对路径文件

#### load:

`Kernel#load(filename, wrap=false) → true`

load加载文件, 不记录在`$LOADED_FEATURES` 中，可以多次加载, 必须要加扩展名.

load的作用：在development模式下，当你修改一段代码后，不用重启服务器，你的代码更改会被自动reload，这就是load的作用 而如果你使用require的话，多次require并不会起作用.

You use load to execute code, and you use require to import libraries. load 和 require都使自己定义的常量残留下来, load提供了一个解决方案`load(file_name, true)` If you load a file this way, Ruby creates an anonymous module, uses that module as a Namespace to contain all the constants from motd.rb , and then destroys the module.

#### autoload(module, filename): 已经不推荐使用

`Kernel#autoload(module, filename) → nil`

module 可以是字符串或者符号, 注册以后将会被加载的文件，当module被首次使用时, 加载时使用的是`Kernel::require`

`autoload(:MyModule, "/usr/local/lib/modules/my_module.rb")`

应用场景:

* 懒加载
* 多个文件依赖同一个模块, 但是不确定哪个文件先被执行(不确定`require`应该出现在哪个文件)

---

### 2. Node.js

#### require 的模块加载过程:

优先从缓存加载

* 每次require都会先检查加载缓存, 优先检查核心模块缓存, 然后在检查文件模块缓存
* require 成功加载文件模块后, 都会把路径、exports等相关信息缓存到对象`require.cache` 中.
* 如果想要重新加载一个js/json文件, 比如这个文件会动态变化, 可以手动删掉缓存: `delete require.cache[require.resolve('./test.js')]`

require 允许使用相对路径, 绝对路径, 以及模块标识:

* 相对路径, 绝对路径: 用于加载文件模块, require会将相对路径转换为绝对路径, 作为真是的索引, 会使用加载缓存.
* 模块标识: 用于加载核心模块和自定义模块(也是文件模块, 通常是一个包)

对于使用模块标识进行加载的情况, 除了先进行缓存查找外, 还有以下几个步骤:

1. 路径分析

  Node.js require的搜索路径是动态的, 是相对于发起require的源文件而言的, 具体规则是:

  * 当前文件目录下的`node_modules`目录
  * 父目录下的`node_modules`目录
  * 父目录的父目录下的`node_modules`目录
  * 逐级递归到根目录的`node_modules`目录

  搜索路径可以在当前文件中通过`module.paths`输出.

  如果`module.paths`无法找到合适的模块, Node还会到`$NODE_PATH`中指定的目录进行查找, 这是用于全局共享的配置.

2. 文件定位

  确定搜索路径后, Node.js 会进行文件定位. CommonJS规范允许省略扩展名, Node会按照`.js` `.node` `.json`次序补足扩展名, 依次尝试.

  尝试的过程是利用`fs`进行同步阻塞判断, Node是单线程, 这可能有小小的问题, 在require时, 带上准确扩展名进行加载, 可以稍稍加快速度.

  如果以上文件扩展名都未查到, 但是却有一个同名的目录, 该目录会作为包进行加载, 见下面包的加载过程.

3. 编译执行

#### 包的加载过程

首先Node会从该包中取出规格文件`package.json`, 取出属性`main`对应的模块进行加载.

如果没有`package.json`文件, 或者缺乏属性`main`, Node将依次查找该目录下的`index.js` `index.node` `index.json`并加载.

否则, 抛出查找失败的错误.

---

### 3. Go

* 全局导入

  `import "some/package/path"` 包的路径, 包级别

  搜索顺序:

  1. vendor:
     * 当前目录(当前编译包)的vendor是否存在
     * 向上查找vendor中是否存在
  2. 工作空间foreach
     * 静态包:`$GOPATH/pkg/具体平台/`
     * 源码:`$GOPATH/src`
  3. 标准库:
     * 静态包: `$GOROOT/pkg/具体平台/`
     * 源码: `$GOROOT/src/`

* 相对路径导入

  `import "../lib"` 仅支持 build/run/test, 无法支持install

---

### 4. Lua

|                        | loadstring/load                                         | loadfile            | dofile        | require        |
|------------------------|---------------------------------------------------------|---------------------|---------------|----------------|
| 自动执行               | N                                                       | N                   | Y             | N              |
| 异常                   | 返回 nil 和错误信息                                     | 返回 nil 和错误信息 | 抛出          | 抛出           |
| 使用package.loaded缓存 | N                                                       | N                   | N             | Y              |
| 参数                   | loadstring: string chunk<br>load: string chunk/function | 相对/绝对路径       | 相对/绝对路径 | 模块标识       |
| 文件后缀               | 不涉及                                                  | 必须带              | 必须带        | 必须省略[^注1] |

<!--
* `loadfile` `dofile` 如果传入模块标识(`somepackage/somelib.lua`), 将自动加上`./` 转换为相对路径.
* `require` 如果传入相对路径(`./somepackage/somelib`), 将自动去掉`./` 转换为模块标识.
-->


#### loadstring/load

* 5.1: `loadstring (string [, chunkname])`

* 5.2: `load (ld [, source [, mode [, env]]])`

编译代码成中间码并且返回编译后的 chunk 作为一个函数, 而不执行代码, 该函数总是在全局环境中编译他的串.

loadstring 是容错函数, 发生错误的情况下, 返回 nil 和错误信息.

5.1 中的loadstring在5.2 中替换为load:
> Function loadstring is deprecated. Use load instead; it now accepts string arguments and are exactly equivalent to loadstring

#### loadfile

`loadfile ([filename [, mode [, env]]])`

编译代码成中间码并且返回编译后的 chunk 作为一个函数, 而不执行代码.

容错函数, 发生错误的情况下, 返回 nil 和错误信息.

<!-- 运行一个文件多次的话, loadfile 只需要编译一次,但可多次运行。dofile 却每次都要编译 TODO  没有证明-->

#### dofile:

`dofile ([filename])`

调用了loadfile, 并执行编译后的函数,  如果 loadfile 失败, dofile 使用 assert 抛出错误.

dofile不会记录加载历史, 多次dofile相同文件会重复执行.

#### require

`require (modulename)` 参数是不带后缀名的「模块」名.

<!-- 5.2  增加了 package.searchers -->
Lua将require搜索的模式字符串放在变量package.path中. 当Lua启动后, 便以环境变量LUA_PATH的值来初始化这个变量. 如果没有找到该环境变量, 则使用一个编译时定义的默认路径来初始化. 如果require无法找到与模块名相符的Lua文件, 就会找C程序库. C程序库的搜索模式存放在变量package.cpath中. 而这个变量则是通过环境变量LUA_CPATH来初始化的.

先搜索`package.path` 如果找到用`loadlib` 加载; 如果没找到, 搜索`package.cpath` 如果找到用`loadlib`加载.

require 会判断是否文件已经加载避免重复加载同一文件, 在`package.loaded` 记录了所有加载过的模块(模块有返回值则记录返回值, 如果没有返回值则记录true)

一个文件将被作为一个function来执行加载, 因此文件中的局部变量, 局部函数将对外不可见, 全局变量, 全局函数对外可见. 可以在文件最后加上return返回想要输出的对象.

传入require中的dot, 会被解释成为目录分隔符:
> require will change each interrogation mark in the template by filename,which is modname with each dot replaced by a "directory separator"(such as "/" in Unix))

---

<!--
### Java

  * `import newpack1.Klass1;` 到类级别
  * `import newpack1.*;` 导入所有包下的类
-->

[^注1]: 省略扩展名是由 `package.path` 和 `package.cpath`的模式决定的, 这2个搜索路径中的模式通常是这样: `/somepath/?.lua;`
