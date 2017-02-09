# 8.6 依赖管理

|                  | Ruby         | Node.js             | Go         | Lua |
|------------------|--------------|---------------------|------------|-----|
| 项目依赖管理工具 | bundler      | npm                 | glide      | -   |
| 依赖规格文件     | Gemfile      | package.json        | glide.yaml | -   |
| 依赖规格lock文件 | Gemfile.lock | npm-shrinkwrap.json | glide.lock | -   |


### 1. Ruby

TODO 未完成

---

### 2. Node.js

TODO 未完成

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

### 4. Lua
