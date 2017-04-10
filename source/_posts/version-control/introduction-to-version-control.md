title: 版本控制简介
date: 2015-04-29 10:00
categories: 版本控制
---

#为什么需要版本控制？

也许我们每个人都曾经有过这样的苦恼：不小心误删文件，或错误修改文件后无法恢复到以前的版本，或修改频繁每个版本都另存为一个新文件导致管理起来很麻烦，就像下图一样。

<!-- more -->

![](/images/version-control/introduction-to-version-control/Final.doc.jpg)

如果有那么一个软件，可以帮助我们管理文件的各个修改过的历史版本，显示每次修改过的内容，误删后可以找回，误改后可以恢复到某个历史版本，那该是多么的神奇，简直就像多啦A梦的时光机一样！

![](/images/version-control/introduction-to-version-control/doraemon-time-machine.jpg)

幸运的是，计算机业界的前辈们已经发明了这个文件时光机，称为**版本控制系统（Version Control System）**。 关于版本控制系统的发展历史，可阅读《[版本控制工具历史的10个里程碑](http://blog.jobbole.com/14489/)》、《[版本管理器的发展史](http://coolshell.cn/articles/3288.html)》，看看这个工具怎么一步步演变至今天的模样。

![](/images/version-control/introduction-to-version-control/scm-history.png)

一个版本控制系统应要做到：

* 让多个用户能同时并行工作。
* 不会覆盖各个用户的变更。
* 维护全部文件的全部版本历史。

---

#SVN

**Apache Subversion（简称 SVN）**是其中一款比较有名的版本控制系统。为了更好地使用 SVN，我们最好先了解 SVN 的设计思路。[SVN 维基百科条目](http://zh.wikipedia.org/wiki/Subversion)的描述是：

> CollabNet 于2000年创建 SVN 项目，意在写出一款近似 CVS 操作方式的版本控制系统。只不过，这款产品针对 CVS 的 bug 和一些缺失的功能，进行了修正和补充。
>
> 最初设计团队设定了几个简单的目标。他们并不想在版本控制方法论上有新突破。他们只想修补 CVS。他们决定 SVN 应该与 CVS 相似，保留相同的开发模型，但不复制 CVS 最明显的缺点。虽然它不一定是 CVS 的完全的替代品，但它应该和 CVS 相似，从而任何 CVS 用户可以不费什么力气的转换过来。

那么我们先要了解 CVS 的设计思路，见 [CVS 维基百科条目](http://zh.wikipedia.org/zh-cn/%E5%8D%94%E4%BD%9C%E7%89%88%E6%9C%AC%E7%B3%BB%E7%B5%B1)。简单来说，CVS 使用 Client-Server 架构，即服务器维护一个中央库，中央库里保存全部文件的全部版本历史，用户连接到中央库并完整复制一份到本地工作副本，对本地工作副本文件进行增删改后，把变更同步到中央库。

为了不让某个用户的工作内容被其它用户的覆盖，CVS 中央库只接受来自基于最新版本文件的变更，所以用户都需要保持自己的本地工作副本最新。变更提交成功后，中央库里全部提交变更的文件的版本号都会 +1。

CVS 的限制是：

* CVS 不支持文件复制和重命名。
* 不支持原子提交（Atomic commit）。
* CVS 只支持文字文件。

原子提交的意思是，对于本次变更的文件，要么全部提交成功，要么全部提交失败。所以 CVS 不支持原子提交是指，如果在提交过程中，CVS 客户端与中央库断开连接，断开前提交的文件成功，断开后提交的文件失败（[What does it mean by atomic commit for a versioning system?](http://programmers.stackexchange.com/questions/163593/what-does-it-mean-by-atomic-commit-for-a-versioning-system)）。这会引起很多问题，比如提交前的程序代码在本地能够正常运行，提交时中断，那么中央库里的代码，有些是最新的，有些是旧的，很可能导致程序无法正常运行。更严重的是，随着开发团队同步中央库的代码到本地，这些新旧代码的差异会迅速扩散，导致每个人本地的代码有新有旧，严重影响团队成员的工作效率和质量，所以 SVN 需要要解决 CVS 这些问题。

那么我们总结一下 SVN 的设计思路：

* 保留与 CVS 相同的模型，即 Client-Server 架构，分层库的设计（[layered library design](http://en.wikipedia.org/wiki/Apache_Subversion#Layers)）。
* 统一版本号，CVS 对每个文件使用各自版本号，而 SVN 对全部文件使用同一个版本号。
* 支持原子提交，每次提交成功后，全部文件（包括没有变更的文件）的版本号 +1。
* 重命名、复制、删除文件等操作都记录在版本历史中。
* 目录也有版本历史，整个目录树可以被移动或复制，而且能够记录全部版本记录。

了解设计思路后，可以开始使用 SVN 了，我们先了解一些概念和术语。

**仓库（Repository）**
　　服务器中央文件库，简称 repo。repo 不仅保存全部文件，还保存全部历史记录，即 Server 端。

**主干（Trunk）**
　　repo 开发主线。

**分支（Branch）**
　　repo 开发支线，当用户想开发过程有多条不同支线，或有多个开发方向时，分支会很有用。

**工作副本（Working copy）**
　　用户不能直接操作 repo，需要检出一份到本地作为工作副本，即 Client 端。

**检出（Checkout）**
　　从 repo 创建一个本地工作副本。

**提交（Commit）**
　　把本地文件变更同步到 repo。SVN 采用原子提交，变更文件要么全部提交成功，要么全部提交失败。

**更新（Update）**
　　把 repo 文件的最新版本同步到本地工作副本。

**合并（Merge）**
　　把分支与主干的文件合并，或分支与另一分支的文件合并。

**冲突（Conflict）**
　　简单来说，就是 SVN 不知道对于同一文件的同一处修改，应该使用用户修改的内容，还是使用 repo 最新版本的内容。例如，本地某个 a 文件不是最新，且用户作出了修改。这时更新中央库文件，恰好有 a 文件的更新记录。那么 SVN 对于本地工作副本和 repo 的 a 文件同一处修改地方，不知道该使用用户修改的内容，还是使用 repo 最新的内容，就会给用户报告冲突。用户需要自行手动处理冲突。

**标签（Tag）**
　　给某个时间点的 repo 版本加上标注，使得在将来容易找回这个版本。例如，标签 `LAST_STABLE_CODE_BEFORE_EMAIL_SUPPORT` 比 Repository UUID `7ceef8cb-3799-40dd-a067-c216ec2e5247` 更容易阅读和理解。

---

#Git

SVN 有一个非常突出的缺点，就是服务器 repo 的[单点故障](https://zh.wikipedia.org/wiki/%E5%8D%95%E7%82%B9%E6%95%85%E9%9A%9C)。当 repo 出现网络问题，导致客户端连接不上时，客户端本地的修改就无法及时同步到 repo，也无法从 repo 检出最新版本的文件。更严重的是，如果 repo 出现磁盘损坏，又没有做好备份工作时，项目的数据将会丢失，只剩下各个客户端本地保存的快照。

为了解决诸如 SVN 等“集中化版本控制系统（Centralized Version Control Systems，简称 CVCS）“的单点故障问题。[Linus Torvalds](https://zh.wikipedia.org/wiki/%E6%9E%97%E7%BA%B3%E6%96%AF%C2%B7%E6%89%98%E7%93%A6%E5%85%B9) 发明了 [Git](https://zh.wikipedia.org/wiki/Git) 这个“分布式版本控制系统（Distributed Version Control System，简称 DVCS）”。Git 的原理是，客户端保存的数据，不仅是 repo 最新文件的快照，而且是整个 repo 完整镜像。见[下图](https://git-scm.com/book/zh/v2/%E8%B5%B7%E6%AD%A5-%E5%85%B3%E4%BA%8E%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6)：

![](/images/version-control/introduction-to-version-control/dvcs.png)

这样，即使 repo 服务器出现故障，也只需要用某个客户端的镜像就能恢复整个项目的历史信息。有关 Git 更详细的设计原理，请见《Pro Git》的 [Git 基础](https://git-scm.com/book/zh/v2/%E8%B5%B7%E6%AD%A5-Git-%E5%9F%BA%E7%A1%80) 章节。

[Git 的目标是](https://git-scm.com/book/zh/v2/%E8%B5%B7%E6%AD%A5-Git-%E7%AE%80%E5%8F%B2)：

* 速度。
* 简单的设计。
* 对非线性开发模式的强力支持（允许成千上万个并行开发的分支）。
* 完全分布式。
* 有能力高效管理类似 Linux 内核一样的超大规模项目（速度和数据量）。

关于 Git 的具体使用，请见《[Git 常用命令](http://syawlaus.github.io/blog/version-control/git-commands/)》一文。

---

# 参考资料

* [版本控制入门插图教程 - 阮一峰的网络日志](http://www.ruanyifeng.com/blog/2008/12/a_visual_guide_to_version_control.html)
* [Subversion](http://zh.wikipedia.org/wiki/Subversion)
* [SVN Tutorial - TutorialsPoint](http://www.tutorialspoint.com/svn/)
* [Pro Git](https://git-scm.com/book/zh/v2)
