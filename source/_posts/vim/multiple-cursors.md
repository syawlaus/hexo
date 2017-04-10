title: Vim 插件之 multiple-cursors
date: 2015-12-20 15:42
categories: Vim
toc: true
tags:
---

# 关于 multiple-cursors

[multiple-cursors](https://github.com/terryma/vim-multiple-cursors) 是类似 Sublime Text 的多光标选择功能，能够选择多个光标的定位，一次修改所有地方，非常方便。

<!-- more -->

![vim-multiple-cursors-1](/images/vim/multiple-cursors/1.gif)
![vim-multiple-cursors-2](/images/vim/multiple-cursors/2.gif)
![vim-multiple-cursors-3](/images/vim/multiple-cursors/3.gif)
![vim-multiple-cursors-4](/images/vim/multiple-cursors/4.gif)

---

# 安装 multiple-cursors

见 [Vundle](http://syawlaus.github.io/blog/vim/vundle/) 一文。

---

# 使用 multiple-cursors

* 在 `Normal模式` 按下 `Ctrl-N`，高亮当前光标的单词，再按下 `Ctrl-N`，高亮这个单词的其它 occurrence
  * 按下 `v` 退出 `Visual模式`，返回 `Normal 模式`，再按下 `i/a` 等进入 `Insert 模式` 插入字符
  * 按下 `c` 删除当前单词，并进入 `Insert 模式`
* 选择多光标时，`Ctrl-P` 删除当前单词光标，光标返回上一个 occurrence（记忆：previous）
* 选择多光标时，`Ctrl-X` 光标跳过当前单词，跳到下一个 occurrence（记忆：skip）
* 使用正则表达式定位光标
