# 8.3 包结构

### 1. Ruby

Ruby Gem 的结构如下:

```
% tree freewill
freewill/
├── bin/
│   └── freewill
├── lib/
│   └── freewill.rb
├── test/
│   └── test_freewill.rb
├── Gemfile
├── README
├── Rakefile
└── freewill.gemspec
```

ruby gem 有2个规格相关的文件: `{GEMNANE}.gemspec`(必须) 和 `Gemfile`(非必须)

#### 规格文件: `{GEMNANE}.gemspec`

重要规格说明:

* `require_paths`: 加载目录(数组)

  当这个gem 激活时, 这个路径将加入`$LOAD_PATH`, 默认是lib目录, lib 下的文件名即可以用于require

* `bindir` 可执行文件目录, 通常是`bin`

* `executables` 可执行文件(数组)

  数组内容相对于bin目录, 将会安装到`EXECUTABLE DIRECTORY` (通过命令`gem environment`可以查看)

  用于实现ruby命令行工具

* `add_runtime_dependency` 增加运行依赖

gemspec的详细规格说明请参考[SPECIFICATION REFERENCE](http://guides.rubygems.org/specification-reference/)

#### `Gemfile` 和 `{GEMNANE}.gemspec`的区别

首先要知道开发一个gem和开发一个应用的区别：

* gem 不关心dependency gem的来源(依赖的gem只是一个符号，没有指定来源)，应用要保证代码在所有机器完全一致

* gem 开发不能控制deployment，但是应用开发可以，应用开发使用`Gemfile.lock`写死版本和来源

* gem 看中的是长期适用（longevity）和灵活（flexibility），而应用看中的是代码绝对一致的保证

因此:

* gem 不应该 把 `Gemfile.lock`放在版本库，而应用开发应该. 原因在于：`Gemfile.lock` 将会把所有依赖的gem限定确定的版本，但是其实gem 是适用于一定的版本区间的。

* gem的Gemfile应该只包含

  ```
  source "http://www.rubygems.org"
  gemspec
  ```

  其中`gemspec` 告诉bundler使用同目录的` .gemspec` 去解决gem的依赖关系

---

### 2. Node.js

```
├── LICENSE
├── README.md
├── bin/
│   └── freewill
├── lib/
│   └── freewill.js
├── index.js
├── package.json
└── node_modules/
    └── ...
```

#### 规格文件: `package.json`

重要规格说明:

* `main`: 包的入口文件, 也就是当包被require时执行的文件

  通常来说应该是包根目录下, 与包同名的文件

* `bin`: 命令映射, 支持多个命令

  * 全局安装: 命令将链接到`prefix/bin`,(通过`npm prefix -g`查看prefix) 因为`prefix/bin` 在PATH中, 可以直接执行
  * 项目安装: 命令将链接到本项目的`node_modules/.bin/`里, 需要按照路径执行

* `scripts`: 脚本映射, 支持多个脚本

  `start`, `install` 有默认执行机制 TODO

  通过`npm run 脚本名称`执行

* `dependencies`: 运行时依赖声明

* `devDependencies` 包开发时依赖

  `npm install`  默认会安装2中依赖

  `npm install --production`  只安装运行时依赖

  `npm install packagename` 指定包名, 也只安装运行时依赖

  `npm install packagename --dev` 指定包名, 排除开发时依赖, 效果同上


详细规格说明参考[官方package.json说明](https://docs.npmjs.com/files/package.json)

---

### 3. Go

* 一个目录下的第一层所有文件属于一个包, 这些文件需要声明相同的包名
* 一个目录下的子目录是另一个完全没关系的独立包
* 包名和目录名可以不一样
* 可以有重名的包, 只要导入路径不同即可

---

### 4. Lua

TODO: Lua 包定义由Luarocks实现, 用于管理与组织Lua模块.

---
<!--
### Java

* 源文件结构

* jar包结构

-->
