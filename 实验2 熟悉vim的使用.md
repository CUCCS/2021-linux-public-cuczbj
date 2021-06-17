### 实验2 熟悉vim的使用

##### 实验目的 使用vim作为文本编辑器，并使用asc录制屏幕

##### 实验环境Linux

###### 1.打开vim和asc

打开vim：登录ssh，输入vimtutor就可以打开了，按esc，输入：wq退出

录制：输入asciinema rec开始录制，输入exit停止录制

###### 2.基本操作

1. 1`hjkl`移动光标方向，`x`键删除字符，`i`键转换到插入字符状态，`a`键转换到在文本后附加字符的状态。 

https://asciinema.org/a/CONCjyWp2ALMSEn4pm5nggxFl

   1.2 ：q退出编辑器，不做任何更改

   1.3   按x键删除光标下的字符

   1.4按i键插入文本。

   1.5按A键附加文本。

   2.1键入dw删除单词。

   2.2 键入d$以删除到行尾。

1.2---2.2

https://asciinema.org/a/XXMGEATiwRlfG2oBL7wpd6cBL





2.3

d是delete操作符。

w 直到下一个单词的开头，不包括第一个字符。

e 到当前单词的结尾，包括最后一个字符。

$ 到行尾，包括最后一个字符。

2.4

键入2w将光标向前移动两个字。

键入3e将光标向前移动到第三个单词的末尾。

键入0（零）移动到行的开头。

2.5

键入d2w删除两个大写单词。

2.6

键入dd删除整行。

2.3----2.6https://asciinema.org/a/Yqh25AVoG0z3sW2pERe5PRmuV

2.7按u键撤消最后的命令

3.1键入p将以前删除的文本放在光标后面。

3.2键入rx以将光标处的字符替换为x

3.3要更改到单词结尾，请键入ce

3.4“更改”操作符用于与“删除”相同的运动。

4.1键入CTRL-G以显示您在文件中的位置和文件状态。

键入G以移动到文件中的某一行。

4.2 键入/后跟短语以搜索短语。

4.3键入%以查找匹配的、]、或}。

4.4键入：s/old/new/g以“new”替换“old”。

2.7---4.4https://asciinema.org/a/EQJonHZeeov2gdVa7jrQxS71q

5.1类型：！然后是执行该命令的外部命令。

5.2要保存对文本所做的更改，请键入：w FILENAME

5.3要保存部分文件，请键入v motion:w FILENAME

5.4要插入文件的内容，请键入：r FILENAME

6.1键入o以在光标下方打开一行，并将您置于插入模式。

6.2键入a以在光标后插入文本。

6.3 键入大写R以替换多个字符。

6.4使用y运算符复制文本，使用p运算符粘贴文本

键入“：set xxx”可设置选项“xxx”。一些选项包括：

“ic”“ignorecase”“搜索时忽略大小写”

“is”“incsearch”“显示搜索短语的部分匹配项。”

“hls”“hlsearch”突出显示所有匹配的短语

您可以使用long或short选项名。

5. 1-----6.4https://asciinema.org/a/3sUW54GWbQU3SmCKLdCM0pKRh

7.1按住F1查看帮助

