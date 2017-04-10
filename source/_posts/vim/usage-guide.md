title: Vim 使用指南
date: 2015-12-14 12:00
categories: Vim
toc: true
tags:
---

#原则

**Vim tutor：切记您要在使用中学习，而不是在记忆中学习。**

<!-- more -->

---

#光标移动

光标上下左右移动

        K
    H     L
      J

光标移动到行首

    0（数字 0）

光标移动到行首首个非空白字符

    ^

光标移动到行尾

    $

光标移到文件第一行

    gg

光标移到文件最后一行

    G

光标移到某一行

    行号 + gg / 行号 + G

查看当前行的信息

    Ctrl-G

单词间的光标移动

    w       移到下个单词首，记忆：next word
    W       移到下个以 blank 分隔的单词首
     
    b       移到单词首，记忆：back to head
    B       移到以 blank 分隔的单词首
     
    e       移到单词尾，记忆：end of word
    E       移到以 blank 分隔的单词尾
     
      cursor  ew         E W
         |    ||         | |
    X = (name_1,vision_3); # this is a comment

光标移到当前行上/下一个字符 `x`

    Fx / fx

匹配光标所在括号，移到其匹配的括号

    %

匹配光标所在单词，移到上/下一个单词

    # / *

向上一页

    Ctrl-B  -- 记忆：backward page

向上半页

    Ctrl-U

向下一页

    Ctrl-F  -- 记忆：fordward page

向下半页

    Ctrl-D

光标移到当前屏幕的第一行

    H -- 记忆：highest line

光标移到当前屏幕的中间行

    M -- 记忆：middle line

光标移到当前屏幕的末行

    L -- 记忆：lowest line

光标移到跳转列表的上一个位置（old <- new）

    Ctrl-O

光标移到跳转列表的下一个位置（old -> new）

    Ctrl-I

---

#Insert 模式

切换为 `Insert 模式`

    i  -- 在光标前输入，记忆：insert
    I  -- 在当前行的首个非空白字符前输入（即 ^i）
     
    a  -- 在光标后输入，记忆：append
    A  -- 在当前行的行尾输入
     
    o  -- 在光标下一行行首输入
    O  -- 在光标上一行行首输入

退出 `Insert 模式`，返回到 `Normal 模式`

    <Esc>

删除并切换为 `Insert 模式`

    C / c$  -- 删除当前行光标后面的字符，并切换为 Insert 模式（即 d$a），记忆：change
    c^      -- 删除光标到行首首个非空白字符，并切换为 Insert 模式（即 d^i）
    cc      -- 删除当前行，并切换为 Insert 模式（即 ddO）
    cfx     -- 删除光标到下一个 x 字符之间的内容，并切换为 Insert 模式（即 dfxi）

上一条补全

    Ctrl-P  -- 记忆：previous completion

下一条补全

    Ctrl-N  -- 记忆：next completion

---

#编辑（Normal 模式）

删除光标所在字条

    x

undo

    u

undo 整行的修改 

    U

redo

    Ctrl-R

---

#查找/替换

在当前文件中查找（自动 wrapscan，除非 wrapscan 被复位）

    /key   -- 往下查找
    ?key   -- 往上查找

仅在本次查找忽略大小写

    /key\c

查看下一个查找结果

    n   -- 正向查看（与 / 或 ? 查找方向一致），记忆：next
    N   -- 反向查看（与 / 或 ? 查找方向相反）

查找结果移除高亮显示

    :nohls  -- 记忆：no highlight search

替换当前行第一个 old 为 new

    :s/old/new  -- 记忆：search

替换当前行全部 old 为 new

    :s/old/new/g  -- 记忆：global

替换两行之间全部 old 为 new

    :#,#s/old/new/g  -- #指行号，:3,10s/old/new/g 即替换第 3~10 行之间全部 old 为 new

替换整个文件全部 old 为 new

    :%s/old/new/g

替换整个文件全部 old 为 new，且替换前会询问用户（y/n）

    :%s/old/new/gc  -- 记忆：global confirm

替换光标所在字符

    r -- 按下 r 后输入新字符，记忆：replace

进入/退出替换模式

    R / <Esc>

---

#复制/剪切/粘贴

删除当前行

    dd

删除 N 行（向下删除）

    Ndd

从光标处删除至当前单词尾

    de  -- 记忆：delete to word end

删除光标所在单词

    daw  -- 记忆：delete a word

删除 () 块

    dab  -- 记忆：delete a block

删除 () 块里面的内容

    dib  -- 记忆：delete inner block

删除 {} 块

    daB  -- 记忆：delete a Block

删除 {} 块里面的内容

    diB  -- 记忆：delete inner Block

复制

    y  -- 先按 v 进入 Visual 模式，HJKL 选择文字区域后按 y 复制，记忆：copy

复制当前行

    yy

粘贴

    p  -- Normal 模式下使用，记忆：paste

注意：`d` 命令都可以用 `c` 命令代替，而且执行后还切换到 `Insert 模式`

---

#Visual 模式

切换至 `Visual 模式`

    v

退出 `Visual 模式`

    <Esc>

可视行（`Visual line`）

    V

可视块（`Visual block`）

    Ctrl-V

---

#存档/退出

写入文件 / 强制写入文件

    :w / :w! -- 记忆：write

退出 Vim 或 `help` / 强制退出 Vim 或 `help`（且不写入）

    :q / :q! -- 记忆：quit

写入文件并退出 Vim

    :wq
    或
    :x

---

#分屏

上下分屏

    Ctrl-W S        -- 上下分屏，记忆：split
    或
    :sp [fileName]  -- 不加 fileName 是当前文件分屏，加 fileName 是不同文件分屏

左右分屏

    Ctrl-W V         -- 左右分屏，记忆：vertical split
    或
    :vsp [fileName]  -- 同 :sp [fileName]）

切换分屏

    Ctrl-W HJKL  -- 光标移到左下右上的分屏

关闭分屏

    Ctrl-W C  -- 关闭光标所在分屏，记忆：close
    Ctrl-W Q  -- 关闭光标所在分屏，如果只剩最后一个分屏，则退出 Vim

调整分屏尺寸

    Ctrl-W =  -- 让所有分屏高度一样
    Ctrl-W +  -- 增加高度
    Ctrl-W -  -- 减小高度

互换分屏位置

    Ctrl-W L
    Ctrl-W R

---

#宏

录制一个宏 a 并保存到寄存器中

    qa

停止录制宏
    
    q

播放宏 a

    @a
    
---

#其它操作

进入 `help`

    :help

查看某命令的 `help`

    :help cmd

补全命令

    输入:命令时，Ctrl-D 可查看可能的补全结果

返回 `Normal 模式` / 在 `Normal 模式` 下取消当前未输入完的命令

    <Esc>

Vim 所有删除操作，删除后都复制到剪贴板（即剪切）

`y` 是操作符，可 `number + ope` 结合使用，如 `y2aw` `2yaw` 就是复制两个单词

    operator [number] motion
    [number] operator motion

执行上一条命令

    .

---

#教程图

![](/images/vim/usage-guide/lession 1 - basic editing.gif)
![](/images/vim/usage-guide/lession 2 - operators & repetition.gif)
![](/images/vim/usage-guide/lession 3 - yank & paste.gif)
![](/images/vim/usage-guide/lession 4 - searching.gif)
![](/images/vim/usage-guide/lession 5 - marks & macros.gif)
![](/images/vim/usage-guide/lession 6 - various motions.gif)
![](/images/vim/usage-guide/lession 7 - various commands.gif)
![](/images/vim/usage-guide/Vim-Cheat-Sheet-for-Programmers.png)
