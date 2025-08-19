# VIM

## Vim cheat sheet
[This](https://vim.rtorr.com/) should have most common shortcuts.

## Toggle line wrapping
```vim
:set nowrap
:set wrap
```

## Jump to column
Be on the line you want to jump to, then use the `|` command.
```vim
30|
```

## Copy line and paste
```vim
yy
p
```

## If you forgot to open a file with sudo
```vim
:w !sudo tee %
```

## Format JSON file
```vim
:%!python -m json.tool
```

## Set tabs to 4 spaces in .vimrc
```vim
set tabstop=4      " \t size is 4.
set shiftwidth=4   " Indent size is 4.
set softtabstop=4  " Tab or backspace size is 4.
set expandtab      " Replace \t with whitespaces.
set smarttab       " Tab inserts 'shiftwidth' at the beginning of line, 'tabstop' and 'softtabstop' elsewhere.
```
