# Vim

## Toggle line wrapping

```text
:set nowrap
:set wrap
```

## Jump to column

Be on the line you want to jump to, then use the `|` command.

```text
30|
```

## Format JSON file

```text
:%!python -m json.tool
```

## Set tabs to 4 spaces in .vimrc

```text
set tabstop=4      " \t size is 4.
set shiftwidth=4   " Indent size is 4.
set softtabstop=4  " Tab or backspace size is 4.
set expandtab      " Replace \t with whitespaces.
set smarttab       " Tab inserts 'shiftwidth' at the beginning of line, 'tabstop' and 'softtabstop' elsewhere.
```
