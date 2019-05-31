Vim
===

Mode 模式
---

* Normal Mode/Command Mode: 按 `ESC` 从其他模式退回Normal Mode
* Insert Mode: 按 `i` 进入编辑模式,开始编辑文件
* Command-Line Mode: 按 `:` 进入,执行一些特殊\高级命令

常用命令:

command|meaning
-|-
vim|进入vim
vim filename|打开或新建一个文件
:q!|强制退出
:wq|保存并退出
ZZ|保存并退出

Normal Mode Commands
---

navigate:

commands|meaning
-|-
h|move left
j|move down
k|move up
l|move right
-|-
Crtl-f|page down
Ctrl-b|page up
-|-
w|move forward by word
W|move forward by word using white space as word boundaries
b|move backward by word
B|move backword by word using white space as word boundaries
-|-
z<ENTER>|move your view up
0|go to the beginning of line
^|go to the beginning of character
$|go to the end of line
-|-
1gg|go to the very beginning of the file
gg|go to the very beginning of the file
G|go to the very end of the file
$G|go to the very end of the file
<LINE_NUMBER>gg or <LINE_NUMBER>G|go to a specific line
-|-
Ctrl-g|to view how many lines in the file
g-Gtrl-g|get more infomation about where is the cursor
.|repeat the previous command

delete:

command|meaning
-|-
x|delete the character at the cursor
X|delete the character before the cursor
d-w|to delete a word at the cursor
d-W|to delete words ignore punctuate
d-$|delete words from cursor to the end of the line
D|delete words from cursor to the end of the line
dd|delete the whole line
5dw|number-the count, how many times to repeat the command
3w|repeat word motion 3 times
d3w|delete the 3w motion
2d3w|delete the 3w motion 2 times

command = [count]operation[count]{motion}


Line Mode
---

commands|meaning
-|-
:32<ENTER>|go to line 32
:$<ENTER>|go to the last line
-|-
:set ruler|open the ruler
:set noruler|close the ruler
:set ruler!|open or close the ruler
:w<ENTER>|save file and return to Normal mode
