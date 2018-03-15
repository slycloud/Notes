## Motion
### Common Motion
command | description | example
--- | --- | ---
`0` | move to the start of the line |
`$` |  move to the end of the line | 
`{`, `}` | move by paragraph: `{` ->  backward; `}` ->  forward | 
`gg` | move to the start of the file | 
`G` | move to the end of the file | 
[number] + `G` | move to line number | 
Ctrl + `G` | show the location in the file and the file status | 
`dd` | delete a whole line | 
`2dd` | delete 2 lines |
`o`(lower case) | open a line below the cursor and place you in Insert mode. |
`O`(capital case) | open up a line ABOVE the cursor. |
`a` | insert text AFTER the cursor |
`e` | move to the end of word, or of the next word. |
`R` | replace more than one charactor |
`p` | put previously deleted(yanked?) text after the cursor. |
`r<x>` | replace the character at the cursor with character "x". |
`ce` | change until the end of a word.(delete automatically and you can retype). `c`: correct. `e`: end (length of words that are to be corrected) |
`cw` | delete the current word (in fact, delete from cursor to end of the current word), and place you in Insert mode. |
`:1,$d` | delete whole file |

### Edit in Normal Mode
`d + <optional count> + <motion>`

#### Where
d - is the delete operator
motion - is what the operator will operate on(list below).

#### List of Motions
w - until the start of the next word, EXCLUDING its first character.
e - to the end of the current word, INCLUDING the last character.
$ - to the end of the line, INCLUDING the last character.

#### Example
command | description
--- | ---
`d2w` | delete 2 words (add 2 toexecute `dw` 2 times)
`d$` | delete to the end of the line
`de` | delete from the cursor to the end of the word
`dw` | delete a word

### Undo
command | description | example
--- | --- | 
`u` | undo previous action |
`U` | undo all the changes on a line |
CTRL + `R` | undo the undo's |
`:later 30s`, `:earlier 10m` | relatively come back or go forward to a time point（相对地）回到某个时间点的编辑状态; time unit: second, hour, day, etc. <br> link: http://vimdoc.sourceforge.net/htmldoc/undo.html |

## Command
Command that is started with `:`and input below the terminal.

### Yank

#### 1-direction yank
command | description | example
--- | --- | ---
(1) `ggyG` <br> (2) `:%y+` | (1) yank the whole file (won't copy to clipboard) <br> (2) yank the whole file (will copy to clipboard) |
`yy` or `Y` | copy a whole line |
`yw` | copy to end of the word |
`y}` | copy to end of the paragraph |
`y{` | copy to start of the paragraph |
`y$` | copy to end of the line |
`y/<search command>` | copy from the cursor to the first match of the search command. | 注意：默认，"the first match"应该也会被复制，但是经过测试，使用spf13-vim的配置，"the first match"本身不会被包含！<br> 例如：I have a |dream that I want to fly.红色"|"表示光标，测试命令为“y/fly”。默认应该复制“dream that I want to fly”，但实际复制的结果是“dream that I want to ”

#### 2-direction yank
以下命令，从光标处开始，往前后两个方向复制，即可以复制一个完整单词/段落等；将以下“a”替换为“i”，复制时会忽略两头的空格。
command | description | example
--- | --- | ---
`yaw` | copy a whole word <br> | 例如：I have a dr|eam. "yaw"将会复制"dream"
`yaW` | copy a whole word, but use "whitespace" as separator of words. |
`yas` | copy a whole sentence |
`yap` | copy a whole paragraph |

#### copy a block of code
command | description | example
--- | --- | ---
`yab` or `ya{` | copy entire {…} block |
`ya[` | copy entire […]-delimited text the cursor is in |
`ya(` | copy entire (…)-delimited text the cursor is in |
`ya<` | copy entire <…>-delimited text the cursor is in |
`ya"` | copy entire “…”-delimited text the cursor is in |
`ya‘` | copy entire ‘...’-delimited text the cursor is in |
``ya` `` | copy entire `…`-delimited text the cursor is in |

### Search
command | description | example
--- | --- | ---
`n` | browse next match | Note:  when the search reaches the end of the file it will continue at the start, unless the 'wrapscan' option has been reset.
`N` | browse previous match | 
`:?<search pattern>` | followed by a phrase, searches BACKWARD for the phrase | 
`:/<search pattern>` | followed by a phrase, searches FORWARD for the phrase | 

### Substitution
commadn | description | example
--- | --- | ---
`:s/old/new/g` | to substitute 'new' for 'old' <br> g - means to subsitute globally in the line, change all occurrences of 'old' in the line. |
`:#,#s/old/new/g` | where #,# are the line numbers of the range of lines where the substitution is to be done.
`:<some character>` | press CTRL+D, Vim will show a list of commands that start with 'e' <br> press <TAB>, Vim will complete the command name to ':edit' or others. <br> Note: Completion works for many commands. Just try pressing CTRL+D adn <TAB>. | e.g. ":e"
`:w FILENAME` | save the changes made to the text |
`:rm FILENAME` | remove the file named FILENAME |
`:r FILENAME` | insert the contents of a file | Note1: the file you retrieve is placed below the cursor line.<br> Note2:you can also read the output of an external command. For example :r !ls reads the output of the ls command and puts it below the cursor.
type "v", then ":w FILENAME" | save part of the file <br> Note: Pressing v starts Visual selection. You can move the cursor around to make the selection bigger or smaller. Then you can use an operator to do something with the text.

### Multi-window
command | description | example
`vim -On file1 file2 ...` <br> `vim -on file1 file2 ...` | 在shell里面打开多个文件且垂直分屏 <br> 在shell里面打开多个文件且水平分屏 |
`Ctrl-w + Ctrl-=` | make split windows' size equally |
`Ctrl-w` | switch between all windows |
`Ctrl-w-j/k/h/l` | switch in down / up / left / right |
`:sp` <br> `:sp <filename>` | split a window, opening the same file in current window <br> split a window, opening the specified file |
`:new` | 创建空白分屏 | 
`:vsp flilename` | 以垂直分屏方式打开文件，vsp=vsplit |
`:sp filename` | 以水平分屏方式打开文件，sp=split |
`ctrl+w+s` | 以水平分屏方式打开当前文件 |
`ctrl+w+v` | 以垂直分屏方式打开当前文件 |
`ctrl+w+c` | 关闭当前分屏，c=close |
`ctrl+w+o` <br> :only | 取消其它分屏，只保留当前分屏 |
`ctrl+w+q` | 只剩最后一个分屏时，退出 |
ctrl+w+w` <br> ctrl+w+h <br> ctrl+w+j <br> ctrl+w+k <br> ctrl+w+l | 任意切屏，只有两个分屏时，比较方便 <br> 向左切屏 <br> 向下切屏 <br> 向上切屏 <br> 向右切屏

### Fold
所有fold的指令都以z开头.

command | description | example
--- | --- | ---
`zf<motion>` | 手动折叠一段文本 | “zfip”, “zf/<pattern>"
`NzF` | 折叠N行 | ”5zF" |
`zo` | open up a folded line |
`zc` | close a fold again |
`zn` | unfold everything |
`zi` | toggle all folds. Folded -> unfolded or unfolded -> folded | 
`zd` | delete a fold |
`ze` | eliminate all folds(not work?) | 

对于nested folder，每次打开或关闭一个fold，都只适用于一层fold。要同时打开／关闭多个层次，可以使用以下命令：

command | description | example
--- | --- | ---
`zm` | Increase fold level (one level more foldy) |
`zM` | Maximize the foldedness (折叠所有层次) |
`zr` | Reduce foldedness (one level less foldy) |
`zR` | Minimize all foldedness (打开所有层次的折叠) |

#### Automatic fold
注意：只有当没有手动设置fold时，自动fold才会生效.

command | description | example
--- | --- | ---
`:set foldmethod=indent` | 根据indent来fold |
`:syntax enable` <br> `:set foldmethod=syntax` | 根据syntax来fold |
`:set foldexpr=strlen(matchstr(get line(v:lnumber), ‘^-*'))` | 自定义fold的规则 | 根据’-’的个数来决定fold的深度 |
`:set foldopen=all` <br> `:set foldclose=all` | 当光标移动到fold上，自动打开；光标移开时，自动关闭 |
`:set foldcolumn=6` | 在左侧显示目前打开的fold层次结构；此处设置为最多显示6层次（？） |
`:set foldcolumn&` | 关闭显示 |
