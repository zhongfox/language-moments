# 9.3 Vim 静态分析插件

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

---

### VIM 异步语法检查插件[ALE](https://github.com/w0rp/ale)

* 需要升级vim8, 或者使用NeoVim.
* ale 利用vim8支持的`job_control`进行异步语法检测
* 可以配置的检测时机: 内容动态修改时, 文件打开时, 文件保存时

* 自动读取各语言检测器用户配置文件(项目目录或者用户home目录):

  `~/.eslintrc.js`

  `~/.rubocop.yml`

  `~/.luacheckrc`

#### 关键配置参数

* 通过`help ale-options` 查看所有支持的配置说明

* 修改时机配置

  ```vim
  "以下是默认值
  let g:ale_lint_on_save = 0
  let g:ale_lint_on_text_changed = 1
  let g:ale_lint_on_enter = 1
  ```

* 查看当前语言可用的检测器(Available Linters)和开启的检测器(Enabled Linters)

  > :ALEInfo

* `g:ale_linters` 对各语言要使用的linter进行限制

  比如限制js只是用eslint: `let g:ale_linters = {'javascript': ['eslint']}`

  或者禁止js使用: `let g:ale_linters = {'javascript': []}`

* 在错误提示之间跳转:

  ```vim
  nmap <silent> <C-k> <Plug>(ale_previous_wrap)
  nmap <silent> <C-j> <Plug>(ale_next_wrap)
  ```

