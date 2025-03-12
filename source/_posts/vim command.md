---
title: vim command
date: 2015-08-16 12:01:30
tags: vim
categories: linux
---

# config

`~/.vimrc`

```
" Tab and Indentation Settings
set tabstop=4        " Number of spaces a tab counts for
set shiftwidth=4     " Number of spaces to use for each step of auto indent
set expandtab        " Use spaces instead of tabs
set smarttab         " Insert tabs on the start of a line according to shiftwidth
set autoindent       " Copy indent from current line when starting a new line
set smartindent      " Smart autoindenting when starting a new line


" General Settings
set number           " Show line numbers
set relativenumber   " Show relative line numbers
set ruler            " Show cursor position
set encoding=utf-8   " Set UTF-8 encoding
set scrolloff=5      " Keep 5 lines visible above/below cursor

" Search Settings
set ignorecase       " Ignore case when searching
set smartcase        " Override ignorecase if search contains uppercase
set hlsearch         " Highlight search results
set incsearch        " Show search matches as you type

" Syntax and Appearance
syntax enable        " Enable syntax highlighting
set background=dark  " Dark background mode
colorscheme default  " Choose a color scheme

" File Handling
set autoread         " Automatically read files changed outside of Vim
set nobackup         " Don't create backup files
set noswapfile       " Don't use swap files
```

- format script

  1.	Move to the top of the file using `gg`.
  2.	Re-indent the file:  `gg=G`


  ```vim
  :set filetype=sh  # optional
  :set autoindent
  :set tabstop=4       " Number of spaces per tab
  :set shiftwidth=4    " Number of spaces for indentation
  :set expandtab       " Use spaces instead of tabs
  ```

  

- format json

  ```shell
  :%!jq .
  ```

- formate xml

  ```
  :%!xmllint --format %
  ```

- 显示不可见字符

  ```shell
  :set list
  ```

- 统计字符出现次数

  ```
  :%s/字符串//ng
  ```


- 大小写转换

  ```
  选择文本之后，执行 g~~ 进行大小写的转换
  ```

- 全文选择

- 多行操作

  ```
  按下 Ctrl + v 进入可视块模式。
  使用上下箭头键或 j 和 k 键选择要编辑的行。
  按下 Shift + i 进入插入模式。
  输入要插入的文本。
  按下 Esc 键退出插入模式，所有选定的行都将被修改。
  ```

  


# command

## type `^M`

> equal `\r`

```shell
/Ctrl-V Ctrl-M
```

