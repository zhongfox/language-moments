# 8.8 标准库

一门语言的发布, 除了语言执行器外, 往往还会提供一些常见的功能的具体实现, 比如net, http等等. 语言提供的标准库是生态圈中重要的一环, 不过在各语言中叫法并不相同:

|      | Ruby             | Node.js      | Go               | Lua              | Java                |
|------|------------------|--------------|------------------|------------------|---------------------|
| 名称 | Standard Library | Core Modules | Standard library | Standard Library | Runtime Environment |

---

### 1. Ruby

* `$LOAD_PATH` 中的某个目录

  如本机: `/Users/zhonghua/.rvm/rubies/ruby-2.1.1/lib/ruby/2.1.0`

---

### 2. Node.js

Node.js中的「标准库」其实也是模块, 不过区别于用户定义的文件模块, 「标准库」模块叫做核心模块.

---

### 3. Go

* 静态包: `$GOROOT/pkg/具体平台/`
* 源码: `$GOROOT/src/`

---

### 4. Lua

源于Lua的理念, Lua 的标准库非常小巧, 如果从其他语言转到Lua, 会感觉Lua提供的类库近似吝啬, 不过这也使得Lua非常适合嵌入式系统或者和其他语言系统集成.


---

### 5. Java

`$JAVA_HOME/jre/lib/rt.jar` rt表示runtime
