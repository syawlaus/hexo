title: Vim 插件之 EasyMotion
date: 2015-12-20 14:37
categories: Vim
toc: true
tags:
---

#关于 EasyMotion

[EasyMotion](https://github.com/Lokaltog/vim-easymotion) 跟 [Vimperator](https://addons.mozilla.org/zh-cn/firefox/addon/vimperator/) / [Vimium](https://chrome.google.com/webstore/detail/vimium/dbepggeogbaibhgnhhndojpepiihcmeb) 类似，能快速标记文本里满足条件的位置，通过键入位置的记号快速精确定位光标，能大大提高 Vim 使用效率。

<!-- more -->

---

#安装 EasyMotion

见 [Vundle](http://syawlaus.github.io/blog/vim/vundle) 一文。

---

#使用 EasyMotion

启用 EasyMotion 的默认命令是 `<leader><leader>ope`（`ope` 即 `w, e, b, f` 等光标定位命令，另外作者为了避免 EasyMotion 与 Vim 其它快捷键冲突，故意用了 `<leader><leader>`，自定义 `<leader>` 键后，只要 `<leader>ope` 即可启动 EasyMotion），但 `<leader>` 的默认键位 ``\`` 不太顺手，所以定义为`,`（在 `~/.vimrc` 里加上 `let g:EasyMotion_leader_key = ','`），这样只要键入 `,fa`，就启动 EasyMotion 并高亮标记所有字符 `a` 。键入 `,w`，就高亮标记所有 word 首字符，如下图：

![](/images/vim/easymotion/normal.jpeg)
![](/images/vim/easymotion/leader-w.jpeg)

只要再键入记号，就能把光标定位到该位置，非常方便！:)
