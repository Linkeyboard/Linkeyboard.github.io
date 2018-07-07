---
title: vim指令
date: 2018-06-30 21:50:51
tags: vim
categories: software
---

# vim配置了解一下

---
<!-- more -->
- 移动
	* jklh
	* $(本行末尾) 0(本行开始) ^(本行非空格的第一个字符)
	* G(文件最后一行) gg(文件第一行)
	* w e
	* 相同单词移动 *
	
---
- 插入
	* i 插入
	* o 当前行下一行插入 O当前行下一行插入
	* A 行尾插入 I 行首插入
	* J 下一行连接到当前行，中间多一个空格(x删除)

---
-  其他
 	* mx(mark)  `x 回到标记地方 

---
- 替换
    * r 替换一个字
    * R 一直替换直到按ESC
    * cc替换整行
    * C替换到行尾
    * c0替换到行首
---
  - 删除
  	* x删除光标字符 X删除光标前字符
  	* dw删除一个单词 
  	* d$删除到行尾 d0删除到行首
  	
---
  - 复制粘贴
  	* yy
  	* p在当前航下一行粘贴 P在当前行上一行粘贴
---
vim配置文件
```bash
set nu
syntax on
set nobackup
set mouse =a
set tabstop=4
set shiftwidth=4
set expandtab
set smarttab
set autoread
set cindent
set autoindent
set smartindent
set hlsearch
set showcmd
set enc=utf-8
set expandtab
set autowrite
set completeopt=preview,menu
filetype plugin on

map <F5> :call CompileRunGcc()<CR>
func! CompileRunGcc()
    exec "w"
    if &filetype == 'c'
        exec "!g++ % -o %<"
        exec "! ./%<"
    elseif &filetype == 'cpp'
        exec "!g++ % -o %<"
        exec "! ./%<"
    elseif &filetype == 'java'
        exec "!javac %"
        exec "!java %<"
    elseif &filetype == 'sh'
        :!./%
    endif
endfunc

:inoremap ( ()<ESC>i
:inoremap ) <c-r>=ClosePair(')')<CR>
:inoremap { {<CR>}<ESC>O
:inoremap } <c-r>=ClosePair('}')<CR>
:inoremap [ []<ESC>i
:inoremap ] <c-r>=ClosePair(']')<CR>
:inoremap " ""<ESC>i
:inoremap ' ''<ESC>i
function! ClosePair(char)
    if getline('.')[col('.') - 1] == a:char
        return "\<Right>"
    else
        return a:char
    endif
endfunction
filetype plugin indent on

map <F8> :call Rungdb()<CR>
func! Rungdb()
    exec "w"
    exec "!g++ % -g -o %<"
    exec "!gdb ./%<"
endfunc

inoremap ( ()<Esc>i
inoremap [ []<Esc>i
inoremap { {<CR>}<Esc>O
autocmd Syntax html,vim inoremap < <lt>><Esc>i| inoremap > <c-r>=ClosePair('>')<CR>
inoremap ) <c-r>=ClosePair(')')<CR>
inoremap ] <c-r>=ClosePair(']')<CR>
inoremap } <c-r>=CloseBracket()<CR>
inoremap " <c-r>=QuoteDelim('"')<CR>
inoremap ' <c-r>=QuoteDelim("'")<CR>

function CloseBracket()
 if match(getline(line('.') + 1), '\s*}') < 0
 return "\<CR>}"
 else
 return "\<Esc>j0f}a"
 endif
endf

function QuoteDelim(char)
 let line = getline('.')
 let col = col('.')
 if line[col - 2] == "\\"
 "Inserting a quoted quotation mark into the string
 return a:char
 elseif line[col - 1] == a:char
 "Escaping out of the string
 return "\<Right>"
 else
 "Starting a string
 return a:char.a:char."\<Esc>i"
 endif
endf
```
