# 8.6 依赖管理

|                  | Ruby         | Node.js                               | Go         | Lua  |
|------------------|--------------|---------------------------------------|------------|------|
| 项目依赖管理工具 | bundler      | npm                                   | glide      | -    |
| 依赖规格文件     | Gemfile      | package.json                          | glide.yaml | -    |
| 依赖规格lock文件 | Gemfile.lock | npm-shrinkwrap.json/package-lock.json | glide.lock | TODO |

---

### Ruby Bundler 和 Node.js NPM 关于 版本锁定的差异

Bundler 和 NPM 在使用上最大的差异, 在于前者使用了lock文件`Gemfile.lock`锁定版本, 而后者默认是不会使用lock文件的, 虽然NPM提供了`npm-shrinkwrap.json`作为lock文件, 但是并没有bundler完善, 社区接收度也不高.

任何依赖管理的工具, 终极的目的都是「**保证项目的直接依赖的包接口和行为都是稳定的**」, 而如Bundler、Glide「**锁定项目的每个直接或间接依赖的包的版本**」只是达成这个目的的其中一种实现. 而NMP并不是这样实现的, 这与社区个性(哲学)相关, 也和他们的模块加载机制相关.

#### 模块加载差异与包管理的结构

Ruby中的一个文件, 无需显示的export, Ruby require一个文件后, 会将文件中的module全部加载到全局命名空间. 因此在一个 Ruby 项目的所有包, 不管是直接还是间接依赖, 都只允许出现一次, 并且还不能有模块重名(这也是为什么每个包都会有命名空间封装一下). 这样就允许Ruby将依赖包存储为扁平化的结构和版本锁定.

而Node.js中的模块都是局部可用的, 模块需要显示的export, 导入的内容只存在于局部变量中, 这让每个模块有自己独立的依赖成为可能, 只要它们从不同的地方去 require 就行了. 基于此 npm 的逻辑就是每个包都应该有它自己的依赖, 这可以让使用者只关心项目的直接依赖, 忽略间接依赖. 在达到这个目标的前提下, 能共用一些包就共用, 不能用就算了. 这样依赖, NPM运行不同版本的包共存, 因此依赖包的存储就是一个树状的, 同时也无法方便的去lock版本.

#### 问题

Bundler 和 NPM 都不是完美的方案, 它们的不同实现会产生不同的问题:

##### Bundler 依赖升级困难:

你的项目本身和安装的一个 gem A 都依赖 gem B 。Gemfile 大概像这样：

```ruby
gem 'A'    # has dependency B
gem 'B'
```

有天 B 升级了并提供了一些新特性，你想在项目中使用新版的 B 。这时你就得确保 A 也能够使用新版的 B ，否则就不能升级，Bundler 会提示你 A 依赖的 B 跟你的项目依赖的 B 版本有冲突。你只能使用 A 能够允许的最高版本的 B ，然后也许你就被迫升级 A 去了。如果恰好 A 那时已经没人维护了。你就有更多的事情做了，要么 fork 一个 A 自己维护，要么把你需要的功能抽出来然后 kill your dependency

##### NPM 树状结构占空间, 没有lock有风险

NPM 的树状存储结构会占用很多空间, NPM 3 进行了一些优化, 可以共享一些相同版本的包, 不过理念是一样的.

```
underscore
`--- dependencies
A
`--- underscore
     `--- dependencies
```

NPM的包版本声明使用semver机制, 但是semver无法完全保证「项目的直接依赖的包接口和行为都是稳定的」, 原因可能是包的维护者没有遵守semver机制(minor version 需要向下兼容), 当然也有可能是因为出现了bug.

无论如何Node.js的依赖管理容易出现这样的场景, 在`package.json`中声明版本`^1.2.0`, 在不同的机器下, minor version可能是任何版本.

##### npm 5 package-lock.json

npm 5 增加了 package-lock.json, 主要特性:

* package-lock.json 文件中已经记录了整个 node_modules 文件夹的树状结构，甚至连模块的下载地址都记录了, 基于lock文件重装速度会很快.

* `npm install xxx`

  使用npm install xxx命令安装模块时，不再需要--save选项，会自动将模块依赖信息保存到 package.json 文件 (但此文件需要先手动创建)

  安装模块操作（改变 node_modules 文件夹内容）会生成或更新 package-lock.json 文件

* `npm install`

  如果不存在 package-lock.json 文件，它会根据安装模块后的 node_modules 目录结构来创建

  `npm install`时 如果已经存在 package-lock.json 文件，则它只会根据 package-lock.json 文件指定的结构来下载模块，并不会理会 package.json 文件

  如果手动修改了 package.json 文件中已有模块的版本，直接执行npm install不会安装新指定的版本，只能通过npm install xxx@yy更新

* 参数`--no-save` TODO

---

### Go

对于项目依赖, 官方的建议是把外部依赖的代码全部复制到自己可控的源代码库中, 进行统一管理.

如果希望依赖和项目本身分离, 可以考虑使用[glide](https://glide.sh/)

#### Glide

glide是Go的第三方包管理工具. 支持语义化版本, 支持Git、Svn等, 支持Go工具链, 支持vendor目录, 支持从Godep、GB、GPM、Gom导入. 支持私有的Repos和Forks.

glide管理的工程目录结构如下:

```
$GOPATH/src/myProject (Your project)
|-- glide.yaml
|-- glide.lock
|-- main.go (Your main go code can live here)
|-- mySubpackage (You can create your own subpackages, too)
|    |-- foo.go
|-- vendor
     |-- github.com
          |-- zhongfox
                |-- ... etc.
```

#### 规格文件: `glide.yaml`

重要的规格说明:

* package: 当前包在`GOPATH`下的位置 `This is used for things such as making sure an import isn't also importing the top level package.`

* import: 需要导入的依赖包, 每个条目可以包含以下若干属性:

  * package(必要): 包名, 模式遵循`go tool`的要求
  * version: 语义(semantic)版本, 分支, 分支tag或者提交号, 详见[versioning documentation](https://glide.readthedocs.io/en/latest/versions/)
  * repo: 如果package 和代码地址不是对应的, 可以使用repo设置代码地址.
  * subpackages <!--一直没搞懂用途-->
  * os
  * arch

---

### Lua

TODO

---

参考资料:

* [npm 没有 Gemfile.lock 这样的机制，怎么想的？](https://ruby-china.org/topics/29777#reply10)
