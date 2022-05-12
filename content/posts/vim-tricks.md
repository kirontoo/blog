+++
title = "Useful Vim Tricks"
date = "2020-11-21T19:33:51-08:00"
author = "Amy Dang"
authorTwitter = "" #do not include @
cover = ""
tags = ["vim", "tips"]
keywords = ["", ""]
description = "A few Vim tricks that I use to make my life easier."
showFullContent = false
readingTime = true
hideComments = false
toc = true
+++

## Append Output Of A External Command To Your Buffer

The `:read` command can insert a file or the output of an external program into the current buffer.
To run a program just prefix the shell command with `!`.

Example:
``` vim
:read !echo "hello" 
```

You can also specify the line you want to output to. Just prefix `read` with a number and it will output to that line.
Example:
```vim
:12read !echo "hello"
```

To output to a certain number of lines before your current line, prefix with `-` number of lines.
`-` for before. 

Example:
```vim
:-12read !echo "hello"
```

## Create A New File In The Same Directory As The Current Buffer

`%:h` will autocomplete the folder path for you. Press '<TAB>' to autofill the 
full path

```vim
:e %:h/
```
## Custom Settings For Different Filetypes / Languages: ftplugin
By using the `ftplugin` directory, you can apply different settings or keymaps
for different languages and filetypes.

For example: In the `$HOME/.config/nvim/ftplugin/javascript.vim` file
`nnoremap ,get     :read $VIMPATH/snippets/js/get.js<CR>`

when you run this keymap, it will pipe the contents of the `.js` file into
your current buffer

## Sorting
Highlight the lines you want to sort, then run the command: `:sort`

**Example**:
```javascript
// before
banana = 'banana'
durian = 'durian'
cantalope = 'cantalope'
apple = 'apple'

// after
apple = 'apple'
banana = 'banana'
cantalope = 'cantalope'
durian = 'durian'
```

## Columns
Highlight (using shift+v) the lines you want, then run the command `:!column --table`.
This will organize the spacing for you.

**Example**:
```javascript
// before
apple = 'apple'
banana = 'banana'
cantalope = 'cantalope'
durian = 'durian'

// after
apple      =  'apple'
banana     =  'banana'
cantalope  =  'cantalope'
durian     =  'durian'
```

## Multiple Commands In One
You can execute multiple commands in one command by separating them with `|`.
Example:
```vim
:%s/hello/world | %s/foo/bar | %s/hello/goodbye
```

### Use Case: Generating A File
Sometimes you just need to quickly create a file with some boilerplate in it,
to do this you can make vim open and read file, then pipe it into your current buffer.

You can create a snippets folder that holds all of your snippets or boilerplate.
Using these files, I can generate the files I want by combining commands in VIM.

Example: I have a snippet file called `test.js.` In this file, I have some boilerplate for a
test file for a nodejs project. In any given project, I can generate a new test file in the same directory as
my current buffer is in using:

```vim
:e %:h/mytest.test.js | read /path/to/snippet/test.js
```

## Windows

### Hiding Windows
```vim
noremap <leader>hh <C-w>h:hide<CR>	" hide the left window
noremap <leader>hj <C-w>j:hide<CR>	" hide the bottom window
noremap <leader>hk <C-w>k:hide<CR>	" hide the top window
noremap <leader>hl <C-w>l:hide<CR>	" hide the right window
```

