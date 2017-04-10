title: Vim 插件之 MiniBufExpl
date: 2015-12-20 15:48
categories: Vim
toc: true
tags:
---

# 关于 MiniBufExpl

[MiniBufExpl](https://github.com/fholgado/minibufexpl.vim) 是 Vim 里快捷浏览 buffer 的插件，在窗口顶部列出所有打开的 buffer ，通过快捷键很方便地切换 buffer，在同时编辑多文件时非常有用。具体效果见下图：

<!-- more -->

![](/images/vim/minibufexplr/buffer-states.jpeg)
![](/images/vim/minibufexplr/current-buffer-highlighting.gif)

---

# 安装 MiniBufExpl

见 [Vundle](http://syawlaus.github.io/blog/vim/vundle/) 一文。
 
---

# 使用 MiniBufExpl

光标定位到 buffer-bar，在 Vim 命令行输入：

    :MBEFocus

窗口顶部 buffer-bar 里，不同的颜色表示不同的 buffer 状态：

![](/images/vim/minibufexplr/highlight-purple.jpeg )

表示当前焦点并激活的 buffer（未编辑过）

![](/images/vim/minibufexplr/highlight-red.jpeg)

表示当前焦点并激活的 buffer（编辑过）

![](/images/vim/minibufexplr/highlight-yellow.jpeg)

表示已打开的 buffer 

![](/images/vim/minibufexplr/highlight-cyan.jpeg)

表示已关闭的文件（但 buffer 仍存在）
