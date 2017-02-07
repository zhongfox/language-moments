# 8.5 环境变量

包管理系统常常利用环境变量来确定包的安装目录, 执行目录等等.

### 1. Ruby

> % gem environment

* EXECUTABLE DIRECTORY: 存放各gem中bin中的可执行文件
* INSTALLATION DIRECTORY: the path where gems are installed
* GEM PATHS: path used to search for gems

相关常量

* `$LOAD_PATH` `$:`: require的搜索目录
* `$LOADED_FEATURES` `$"`: 已经被require后的文件, 再次require不会重复执行

---

### 2. Node.js

* `NODE_PATH`:

  `export NODE_PATH="/usr/lib/node_modules;/usr/local/lib/node_modules" #指定 NODE_PATH 变量`

  用`;`分割多个不同的目录, 当require在递归搜索`node_modules`没有找到合适的模块后, Node还会依次查找`NODE_PATH`指定的目录

  该机制通常用于实现模块的跨项目共享.

<!--TODO 移走-->
Node.js 依赖的环境变量较少, 主要有以下几个原因:

* 标准库(核心模块)是提前编译的二进制文件, Node进程启动时, 部分核心模块就加载了, require时无需进行文件定位和编译

* 全局安装可执行命令(`install -g`)的位置, 也无需用环境变量定位, 它的位置是相对于Node的可执行程序的位置:

  `path.resolve(process.execPath, '..', '..', 'lib', 'node_module')`

  `process.execPath` 是可执行程序node的位置

  该目录是可配置的:

  ```
  # in *nix
  npm config set prefix /path/to/global
  # in windows
  npm config set prefix C:\\Users\\pc\\global
  ```

---

### 3. Go

* `export GOPATH=/Users/zhonghua/code/work/go` 工作空间
* `export GOROOT=/usr/local/opt/go/libexec` Go安装目录
* `export PATH=$PATH:$GOPATH/bin:$GOROOT/bin`
* `GOBIN`: 作为`go install` 目标保存目录, 避免所有工作空间bin需要加入PATH

---

### 4. Lua

* `LUA_PATH`: 用于初始化`package.path`(搜索路径) 的环境变量, 执行require会用到该值

  `export LUA_PATH="/usr/local/luarocks-2.2.2/share/lua/5.1/?.lua;;"` 

  在lua5.2中, Lua 初始化`package.path`的优先级如下:

  环境变量`LUA_PATH_5_2` > 环境变量 `LUA_PATH` > `luaconf.h`中的预定义值

* `LUA_CPATH` 用于初始化`package.cpath`的环境变量, 执行require会用到该值

  `export LUA_CPATH="/usr/local/luarocks-2.2.2/lib/lua/5.1/?.so;;"` 

  在lua5.2中, Lua 初始化`package.cpath`的优先级如下:

  环境变量`LUA_CPATH_5_2` > 环境变量 `LUA_CPATH` > `luaconf.h`中的预定义值

<!--
### Java
* `JAVA_HOME` 存储jre的目录
-->
