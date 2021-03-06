参考:
> 《vimtutor》

# 方向

h(左移) j(下移) k(右移) l(右移)，也可以使用方向键。

# 进入跟退出

进入: 输入 vim 文件名 <回车>。

退出:
1. 保存并退出:输入 :wq! <回车>。
2. 退出但不保存: 输入 :q! <回车>。

# 删除单个字符

在正常模式下在光标所在位置按x可以删除光标所在位置单个字符。

# 插入/添加

1. 光标前插入: 输入 i。
2. 当前行最后插入: 输入 A。
3. 在当前光标后插入: 输入 a。

# 计数指定动作

比如:
1. 输入 2w 光标往后移动两个单词。
2. 输入 3e 光标往后移动到第三个单词的词尾。
3. 输入 0 移动光标到行首。


# 删除相关命令(与d有关)

## 基本操作

许多改变文本的命令都由一个操作符和一个动作构成。使用删除操作符 d 的删除命令的格式如下:

   d  motion

其中d是删除操作符，motion是操作符的操作对象,比如:
1. 删除从当前光标到下一个单词的起始处: 输入 dw。
2. 删除光标到单词末尾，包括最后一个字符: 输入 de。
3. 删除从当前光标到行末的所有字符: 输入 d$。

## 计数删除

在删除操作符跟动作的组合中，在动作前加上一个数字可以删除更多:

d number(数字) motion

比如输入 d2w 可以删除两个单词。

## 删除整行

1. 输入 dd 可以删除当前行。
2. 输入 2dd 可以删除两行。

# 撤销类命令

1. 按下u可以恢复上一次操作。
2. 按下U可以恢复光标所在行的初始状态。
3. 按CTRL+R可以重做被撤销的命令，也就是撤销掉撤销命令。

# 置入类命令

p可以把最后一次删除(复制)的内容置入光标之后。

比如： 把一行移动到指定位置，可以先用 dd 命令删除该行，这样会将该行保存到vim的寄存器中，然后在光标所在行按下p，会在光标后所在行的下一行粘贴置入。

# 替换类命令

1. 在光标所在位置输入 r 和要替换的字符，就会把当前光标的内容替换成你要替换的内容。
2. 在光标所在位置输入 R 可以连续替换字符。

# 更改类命令

在光标所在位置，输入 cw 不仅可以删掉一个单词，同时可以进入插入模式。

更改类操作命令如下:

c [number] motion

# 定位以及文件状态

1. 按下 CTRL+G 可以显示光标所在行的位置以及文件状态信息。
2. 输入 G 可以跳到文件的最后一行，输入 gg 可以跳到文件的第一行。

# 搜索类命令

1. 输入 / 跟 要查找的字符串并按下 <回车>，比如: /errroor <回车>，可以在文件中找到你要找的字符串的位置。
2. 此时按n可以往下查找，按下N可以往上查找。
3. 如果是第一次逆向查找，使用? 代替 /即可。
4. ctrl+o 可以回退到之前查找的位置，ctrl+i可以跳到较新位置。

# 配对括号查找

%可以跳转到匹配当前行离光标最近的括号({[或者)}] 对应匹配的)}]或者({[的位置。

# 替换命令

1. 在光标所在行，把头一个old替换成new，输入 :s/old/new。
2. 在光标所在行，把所有old替换成new，输入 :s/old/new/g。
3. 在光标所在处的两行内把所有的old替换成new，输入 :#,#/old/new/g。
4. 把文件内所有的old替换成new，输入 :%s/old/new/gc。

# 执行外部命令

输入:!紧跟着外部命令就会执行该外部命令，比如:

输入 :!ls 可以列举目录内容。

# 保存改动到文件

输入 :w 文件名就可以把当前文件另存为该文件名的文件。

此时，如果想删除文件，可以使用如下命令:
1. MS-DOC下，输入 :!del 文件名。
2. Unix下，输入 :!rm 文件名。

# 保存指定文本到文件

按下v可以进入视图模式，然后移动光标选中文本会高亮，此时，输入 : 屏幕底部会出现:'<,'> 接着输入 w 文件名 会看到 :'<,'>w 文件名 ,按下<回车>后，将会把选中的文本保存当对应的文件下。

# 提取并合并文件

当光标所在位置，输入 :r 文件名 会把对应文件名的内容提取到当前位置并置入。

# 打开类命令

在光标处，输入 o 会在光标下方新建一行并进入插入模式，输入 O 则会往光标上方新建一行并进入插入模式。

# 复制粘贴文本

输入 y 可以复制对应文本，输入 p 可以把复制的文本粘贴到光标后的位置。

# 设置类命令

输入 :set xxx 可以设置xxx选项。

1. 输入 :set ic,表示查找时忽略字母大小写(ignorecase)
2. 输入 :set is,表示查找短语时显示部分匹配(incsearch)
3. 输入 :set hls,表示高亮显示所有匹配短语(hlsearch)
4. 在选项前输入no可以关闭选项，比如 :set noic 可以取消忽略大小写查找
