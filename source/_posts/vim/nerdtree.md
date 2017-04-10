title: Vim 插件之 NERDTree
date: 2015-12-20 15:27
categories: Vim
toc: true
tags:
---

# 关于 NERDTree

[NERDTree](https://github.com/scrooloose/nerdtree) 是 Vim 下能够显示/打开文件目录结构的插件，如图：

<!-- more -->

![nerdtree](/images/vim/nerdtree/nerdtree.jpeg)

---

# 安装 NERDTree

见 [Vundle](http://syawlaus.github.io/blog/vim/vundle) 一文。

---

# 使用 NERDTree

NERDTree 默认不显示，想要显示只需输入命令 `:NERDTree`。想关闭 NERDTree，只需要把光标移动到其分屏，再输入 `:q`。

上面的打开/关闭命令实在太繁，我自定义快捷键为 `F2`，在 `~/.vimrc` 里加上：

    map <F2> :NERDTreeToggle<CR>

如果想在关闭 Vim 时，除了 NERDTree 窗口没有其它文件窗口时能自动关闭 Vim，在 `~/.vimrc` 里加上：

    autocmd bufenter * if (winnr("$") == 1 && exists("b:NERDTreeType") && b:NERDTreeType == "primary") | q | endif

常用操作如下：

    o  展开/收起目录，或打开文件
    O  展开当前节点的所有子节点

    x  收起当前节点的父节点
    X  收起当前节点的所有子节点

    r  递归更新当前节点
    R  递归更新当前节点的根节点

    p  光标跳到当前节点的父节点
    P  光标跳到当前节点的根节点

    i  sp 新分屏打开光标所在文件，并使光标跳到该分屏
       （我不喜欢使用 i，自定义为 s，在 ~/.vimrc 里加上 let NERDTreeMapOpenSplit='s'，再重启 Vim 即可）
    
    s  vsp 新分屏打开光标所在文件，并使光标跳到该分屏
       （我不喜欢使用 s，自定义为 v，在 ~/.vimrc 里加上 let NERDTreeMapOpenVSplit='v'，再重启 Vim 即可）
    
    I  显示隐藏文件/文件夹
    
    未解决：NERDTree 打开时恢复上次关闭的状态

---

# 参考资料

* [VIM 插件 NERDTree 使用笔记](http://www.cppblog.com/summericeyl/archive/2014/01/03/205138.html)
* [NERDTree插件（vim笔记三）](http://jianshu.io/p/eXMxGx)
* [How to remap a NERDTree shortcut in VIM](http://superuser.com/questions/127431/how-to-remap-a-nerdtree-shortcut-in-vim)
