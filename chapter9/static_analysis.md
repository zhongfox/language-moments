# 9.2 静态分析

### Ruby

[Rubycritic](https://github.com/whitesmith/rubycritic)

[rubocop](https://github.com/bbatsov/rubocop)


### Javascript

### Lua

### Java

### Go


---

### VIM 语法检查插件Syntastic

[Syntastic](https://github.com/vim-syntastic/syntastic) 使用各语言的第三方语法检查器, 对文件进行语法检查, 并展示错误.

检测操作可以通过执行命令触发, 也可以是在保存时自动执行. 避免用户在编译或者执行时才发现错误.

Syntastic并不实现具体的语法检测功能, 而是通过集成第三方检测插件


> :SyntasticInfo

查看当前语言可用的检测器(Available checkers)和开启的检测器(Currently enabled)

> SyntasticCheck

主动调用检测

-------
临时笔记:

https://groups.google.com/forum/#!topic/vim-syntastic/axAbKWRVN4Q
:helptags ~/.vim/bundle/syntastic/doc

:help syntastic-checkers

