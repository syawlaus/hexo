title: Vim 插件之 Vundle
date: 2015-12-20 15:22
categories: Vim
toc: true
tags:
---

# 关于 Vundle

[Vundle](https://github.com/gmarik/Vundle.vim) 是 Vim 插件管理器，简化 Vim 包装插件的过程。

<!-- more -->

---

# 安装 Vundle

Vundle 安装方法见 [gmarik/Vundle.vim](https://github.com/gmarik/Vundle.vim)。

---

# 使用 Vundle

下面用 [NERDTree](https://github.com/scrooloose/nerdtree) 来演示使用 Vundle 安装 Vim 插件的过程：

1. `~/.vimrc` 里加上`Bundle 'scrooloose/nerdtree'`
2. 保存 `~/.vimrc`，重启 Vim
3. 输入命令 `:PluginInstall`，Done！

查看当前 Vim 已安装的插件，在 Vim 命令行输入：

    :PluginList
