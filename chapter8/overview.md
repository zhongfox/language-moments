# 8. 概述

包管理是语言生态圈中重要的一环, 包管理的设计的目的, 在于优化代码模块化, 依赖关系调解, 项目集成与部署等等, 在很大程度上会影响语言生态圈的繁荣.

一份代码源文件, 和模块, 包等概念的关系,  在各个语言系统中不完全相同, 也是需要厘清的.

|                  | Ruby                            | Node.js                   | Go                             | Lua                                                            |
|------------------|---------------------------------|---------------------------|--------------------------------|----------------------------------------------------------------|
| 模块             | 源文件可以包含若干module        | 源文件既模块              | 无模块概念                     | 源文件既模块                                                   |
| 包定义           | 由rubygem定义<br>若干源文件组合 | 由npm定义<br>若干模块组合 | 同一目录第一层源文件属于一个包 | 语言本身只有模块的概念, 没有包的概念<br>Luarocks实现了包的定义 |
| 包管理工具       | rubygems                        | npm                       | go 工具链                      | 无                                                             |
| 中央管理库       | <https://rubygems.org/>         | <https://www.npmjs.org/>  | github.com,goolge code等       | Luarocks: <https://luarocks.org/>                              |
| 项目依赖管理工具 | bundler                         | npm                       | glide/dep                      | Luarocks                                                       |

除此以外, 大部分语言还存在其他的管理工具, 比如 Facebook 推出的 Node.js 包管理工具[Yarn](https://yarnpkg.com/), Go语言第三方包管理工具[godep](https://github.com/tools/godep), Lua 的 [LuaDist](http://luadist.org/) 等等.

---

#### 1. Ruby

Ruby语言本身有module的概念, 不过这个module和其他语言的文件模块不是一回事, module是一个语法结构, ruby源文件和module并无一一对应的关系, 一个源文件可能包括多个module, 也可能没有module.

Ruby源文件并没有显示的导出功能, 源文件加载后的数据导出, 依赖语言定义的命名空间和作用域.

Ruby中的包叫做Gem, 是由[RubyGem](https://rubygems.org/)定义和管理的.

[bundler](http://bundler.io/)是Ruby通用的项目依赖管理工具.

---

#### 2. Javascript

Javascript语言本身并没有包的概念, 对比其他高级语言中, Ruby有require, Go, JAVA, Python都有import, 甚至Lua都有require, 而Javascript最大的应用场景浏览器中, `<script>`这种引入方式显得杂乱无章, 语言自身毫无组织和约束能力.

不过一直在不断进化的生态圈中, 出现了CommonJS, AMD等一些优秀的包管理规范. Node.js的NPM机制是CommonJS的一种实现. 本书中的Javascript包管理是主要围绕Node.js NPM.

在Node.js中, 源文件即是模块, 模块通过require进行加载.

在模块之外, NPM定义的包, 则是将模块联系起来的一种机制. NPM既实现了包管理, 也实现了项目的包依赖管理.

---

#### 3. Go

Go 语言没有文件模块概念, 源文件都属于某个包.

关于包管理, Go只是提供了基本的功能(go get, vendor), 并没有官方最佳的管理方案, 也没有统一的中央管理库, 通过使用go get命令从远程代码库(github.com, goolge code 等)拉取, 直接跳过中央版本库的约束, 让代码的拉取直接基于源代码版本控制库, 开发者间的协同直接依赖于源代码的版本控制. 直接去除了库版本的概念. 没有明显的包版本标识.

对于项目依赖, 官方的建议是把外部依赖的代码全部复制到自己可控的源代码库中, 进行统一管理. 从而做到对依赖包的可控管理.

---

#### 4. Lua

在Lua中, 源文件和模块(module)可以一一对应, require 模块时, 会将该模块所在的文件内容包装到一个function中, 文件最后的return 作为模块的返回值.

Lua包定义由Luarocks实现, 用于管理与组织Lua模块.

