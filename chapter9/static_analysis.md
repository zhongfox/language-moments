# 9.2 静态分析

### Ruby

#### [Rubycritic](https://github.com/whitesmith/rubycritic)

主要特性:

* 使用第三方静态分析器:  [Reek](https://github.com/troessner/reek), [Flay](https://github.com/seattlerb/flay) 和 [Flog](https://github.com/seattlerb/flog)
* 同时支持命令行和web图表化展示分析结果
* 可以总览你的项目,并且可以对代码打分(百分制)
* 根据各自的坏味道数量建立文件索引
* 可以查看具体的类文件中的代码质量问题

主要分析[维度](https://github.com/whitesmith/rubycritic/blob/master/docs/core-metrics.md):

* Score: 代表所有分析文件的总分, 0~100分区间
* Churn: 文件修改次数, 基于版本控制系统, 如git的commit次数
* Complexity: 文件复杂度, 目前计算的维度主要基于`赋值`,`分支`和`方法调用`
* Rating: A~F的6个等级, A最好, 通常讲, A,B是比较好的, C可以视为警告, D和F是需要修复的  
  Rating是基于Cost计算的, 所谓Cost是和文件的smell和Complexity相关的  
  (RubyCritic's rating system was inspired by Code Climate's)
* Duplication: 代码重复
* Smells: 代码的坏味道, 主要基于Reek, Flay, Flog


#### [rubocop](https://github.com/bbatsov/rubocop)

官方文档: [rubocop](http://rubocop.readthedocs.io/en/latest/), 主要的风格指导是基于社区流行的[ruby-style-guide](https://github.com/bbatsov/ruby-style-guide)

如果对于rubocop的规则不太熟悉, rubocop执行代码时的报错信息, 不太容易和相关style对应起来, 一个技巧是在[rubydoc Rubocop](http://www.rubydoc.info/gems/rubocop/)上搜索报错信息, 比如在google上利用site搜索:

> Missing top-level class documentation comment site:www.rubydoc.info

相关使用技巧:

* 自动修复(Auto-correct offenses): `-a, --auto-correct`

* 默认配置文件: <https://github.com/bbatsov/rubocop/blob/master/config/default.yml>

* 自定义配置:

  本项目下如果`.rubocop.yml` 存在, 将作为自定义配置文件

  也可以通过参数指定配置文件: `-c, --config FILE`

---

### Javascript

#### [eslint](http://eslint.org/)

主要特性:

* 开源的 JavaScript 代码检查工具
* 所有规则都被设计成可插入的, ESLint 内置了一些规则，用户可以在使用过程中自定义规则
* ESLint 并不推荐任何编码风格，规则是自由的
* 每条规则各自独立, 可以开启或关闭, 可以将结果设置成警告或者错误
* 部分规则支持格式化自动修复

安装:

* 通过NPM全局安装: `npm install -g eslint`

配置加载:

* 配置文件格式可以使JSON, JS或者YMAL, 会从当前目录一直向上需找
* 配置也可以内置到package.json中, 配置顶层key是`eslintConfig`
* 在配置文件中还可以指定外部配置文件, 这个机制可以用于共享配置文件: `extends: './public-eslintrc.js'`
* 另一个共享的方案是使用ESLint 配置的NPM包, 这些配置的模块名一般以eslint-config-为前缀
* 最后配置还可以通过eslint命令行参数指定

以JS配置文件为例:

```javascript
module.exports = {
  extends: 'eslint:recommended', //指定规则分类
  env: { //指定运行环境
    node: true,
    es6: true
  },
  rules: { //对规则进行定制
    'no-console': 'off',
  },
};
```

* 每条规则有 3 个等级：off、warn和error。off表示禁用这条规则，warn表示仅给出警告，并不会导致检查不通过，而error则会导致检查不通过
* 规则的详细说明文档可以参考这里：[Rules - 规则](http://eslint.cn/docs/rules/)

代码格式化:

* 在ESLint 规则列表页面，我们发现有些规则的旁边会带有一个橙色扳手图标，表示在执行eslint命令时指定`--fix`参数可以自动修复该问题

更多细节, 请参考:

* [ESLint 官网](http://eslint.org/) 官网容易被墙, 可以参考下面的中文文档
* [ESLint 中文](http://eslint.cn/)
* [利用 ESLint 检查代码质量](http://morning.work/page/maintainable-nodejs/getting-started-with-eslint.html)

---

### Lua

---

### Java

---

### Go

---

### VIM 语法检查插件Syntastic

[Syntastic](https://github.com/vim-syntastic/syntastic) 使用各语言的第三方语法检查器, 对文件进行语法检查, 并展示错误.

检测操作可以通过执行命令触发, 也可以是在保存时自动执行. 避免用户在编译或者执行时才发现错误.

Syntastic并不实现具体的语法检测功能, 而是通过集成第三方检测插件

#### 推荐配置

```bash
set statusline+=%#warningmsg#
set statusline+=%{SyntasticStatuslineFlag()}
set statusline+=%*

let g:syntastic_always_populate_loc_list = 1
let g:syntastic_auto_loc_list = 1
let g:syntastic_check_on_open = 1
let g:syntastic_check_on_wq = 0
```

具体配置含义可以在vim中查阅: `:help syntastic`

传递参数给指定checker: `syntastic_<filetype>_<checker>_args`, 比如需要给checker指定配置文件

如 `let g:syntastic_javascript_eslint_args = "-c ~/.eslintrc.js"`

#### 常用命令

* 查看所有语言支持的checker说明:

  >:help syntastic-checkers

* 查看当前语言可用的检测器(Available checkers)和开启的检测器(Currently enabled)

  > :SyntasticInfo

* 主动调用检测

  > :SyntasticCheck

  以上命令会使用配置好的checker进行检测, 也可以指定其他可用的checker进行检测, 如在ruby文件指定mri检测器进行检测:

  > SyntasticCheck mri

* 切换主动/被动检测模式

  > SyntasticToggleMode

  * passive mode: 被动检测模式, 不会根据open/save等自动检测, 需要调用`SyntasticCheck` 触发检测
  * active mode: 主动检测模式

#### 常见问题

1. checker 没有任何错误输出

   可能问题:

   1. checker没有安装, checker是需要从第三方进行安装的

   2. checker安装了但是没有启用, 可以通过`:SyntasticInfo`检查

   3. syntastic使用的`$PATH` 不是你的login shell 使用的`$PATH`

      比如ruby场景下使用rubocop, 如果本机使用rvm/rbenv, 而本机系统还有自带ruby, 可能会出现syntastic使用了系统ruby, 导致无法正确调用rvm/rbenv下安装的checker. 简单的解决办法是卸载系统自带的ruby

2. `:help syntastic-checkers` 命令无效

   需要先执行`:helptags ~/.vim/bundle/syntastic/doc`, 参见[help syntastic-checkers error](https://groups.google.com/forum/#!topic/vim-syntastic/axAbKWRVN4Q)
