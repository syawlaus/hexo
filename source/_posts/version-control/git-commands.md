title: Git 常用命令
date: 2016-01-27 16:00
categories: 版本控制
---

# Git 基本工作流程

[Git](https://git-scm.com/) 是一个优秀的分布式版本控制系统，在熟悉 Git 的常用操作前，我们需要先弄清楚 Git 的基本工作流程。

<!-- more -->

[Git 的基本工作流程](https://git-scm.com/book/zh/v2/%E8%B5%B7%E6%AD%A5-Git-%E5%9F%BA%E7%A1%80)是：

1. 在工作目录中修改文件。
2. 暂存文件，将文件的快照放入暂存区域。
3. 提交更新，找到暂存区域的文件，将快照永久性存储到 Git 仓库目录。

其中：

* Git 仓库目录是 Git 用来保存项目的元数据和对象数据库的地方。
* 工作目录是对项目的某个版本独立提取出来的内容。 这些从 Git 仓库的压缩数据库中提取出来的文件，放在磁盘上供你使用或修改。
* 暂存区域是一个文件，保存了下次将提交的文件列表信息，一般在 Git 仓库目录中。 

文件对应的有三种状态，分别是：

* 已修改（Modified），即已修改，但未放入暂存区域的文件。
* 已暂存（Staged），即已放入暂存区域，但未提交到本地仓库的文件（已暂存表示对一个已修改文件的当前版本做了标记，使之包含在下次提交的快照中）。
* 已提交（Committed），即已提交到本地仓库的文件。

![](/images/version-control/introduction-to-version-control/git-file-status.png)

<center>（[图片来源](https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E8%AE%B0%E5%BD%95%E6%AF%8F%E6%AC%A1%E6%9B%B4%E6%96%B0%E5%88%B0%E4%BB%93%E5%BA%93)）</center>

我对暂存区域有个疑问，为什么要设计暂存区域？文件只有“未提交”和“已提交”两种状态不是更方便吗？

---

# Git 安装和配置

见《Pro Git》的 [安装 Git](https://git-scm.com/book/zh/v2/%E8%B5%B7%E6%AD%A5-%E5%AE%89%E8%A3%85-Git) 和 [初次运行 Git 前的配置](https://git-scm.com/book/zh/v2/%E8%B5%B7%E6%AD%A5-%E5%88%9D%E6%AC%A1%E8%BF%90%E8%A1%8C-Git-%E5%89%8D%E7%9A%84%E9%85%8D%E7%BD%AE) 章节。

---

# Git 命令

## 查看帮助

查看 XXX 命令的帮助有三种方式：

    $ git help XXX
    $ git XXX --help
    $ man git-XXX

## 初始化仓库

对某个目录使用 Git 管理，初始化该目录

    $ cd /your-project-directory
    $ git init

该命令将创建一个名为 `.git` 的子目录，这个子目录含有你初始化的 Git 仓库中所有的必须文件，这些文件是 Git 仓库的骨干。 但是在这个时候，我们仅仅是做了一个初始化的操作，项目里的文件还没有被跟踪。参见 [Git 内部原理](https://git-scm.com/book/zh/v2/Git-%E5%86%85%E9%83%A8%E5%8E%9F%E7%90%86-%E5%BA%95%E5%B1%82%E5%91%BD%E4%BB%A4%E5%92%8C%E9%AB%98%E5%B1%82%E5%91%BD%E4%BB%A4) 来了解更多关于到底 `.git` 文件夹中包含了哪些文件的信息。

## 检查文件状态

检查文件的当前状态：

    $ git status

如果目录是干净的，即所有已跟踪文件在上次提交后都未被更改过。执行 `git status` 会输出：

    $ git status
    On branch master
    nothing to commit, working directory clean

上面的输出信息表示，我们当前在项目主分支（master），且当前目录下没有出现任何处于未跟踪状态的新文件，否则 Git 会在这里列出来

如果我们在目录中新建一个 `README` 文件，再执行 `git status` 会输出：

    $ git status
    On branch master
    Untracked files:
      (use "git add <file>..." to include in what will be committed)
    
        README
    
    nothing added to commit but untracked files present (use "git add" to track)

未跟踪的文件意味着 Git 在之前的快照（提交）中没有这些文件。Git 不会自动将这些文件纳入跟踪范围，除非我们明白地告诉 Git “我需要跟踪这些文件”， 这样的处理让你不必担心将生成的二进制文件或其它不想被跟踪的文件包含进来。 

执行命令 `git add README` 之后，把 `README` 文件放到暂存区，这时再查看文件状态，会输出：

    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)
    
        new file:   README

`Changes to be committed` 说明文件已暂存，下次提交会包含该文件。

## 暂存文件

跟踪 XXX 文件，把 XXX 文件放到暂存区域：

    $ git add XXX

如果参数是目录的路径，`add` 命令将递归地跟踪该目录下的所有文件。

如果暂存了 XXX 文件，在提交前又作出了修改，这时查看状态，会输出：

    $ git status
    On branch master
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)
    
        modified:   XXX

文件 XXX 出现在 `Changes not staged for commit` 这行下面，说明已跟踪文件的内容发生了变化，但还没有放到暂存区。要暂存这次更新，需要执行 `git add`。这是个多功能命令：可以用它开始跟踪新文件，或者把已跟踪的文件放到暂存区，还能用于合并时把有冲突的文件标记为已解决状态等。**将这个命令理解为“添加内容到下一次提交中”**而不是“将一个文件添加到项目中”要更加合适。现在执行 `git add` 将 XXX 放到暂存区，然后再看看 `git status` 的输出：

    $ git add XXX
    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)
    
        modified:   XXX

`git status` 命令的输出十分详细，但其用语有些繁琐。如果使用 `git status -s` 或 `git status --short` 命令，将得到一种更为紧凑的格式输出。执行 `git status -s`，状态报告输出如下：

    $ git status -s
    ?? LICENSE.txt          // 新添加的未跟踪文件
    A  lib/git.rb           // 新添加到暂存区中的文件
     M README               // 该文件被修改了但是还没放入暂存区
    M  lib/simplegit.rb     // 该文件被修改了并放入了暂存区
    MM Rakefile             // 在工作区被修改并提交到暂存区后又在工作区中被修改了，所以在暂存区和工作区都有该文件被修改了的记录

## 提交文件

现在的暂存区域已经准备妥当可以提交了。在此之前，请一定要确认还有什么修改过的或新建的文件还没有 `git add` 过，否则提交的时候不会记录这些还没暂存起来的变化。

把暂存区的 XXX 文件提交到本地仓库：

    $ git commit

我们可以在 `git commit` 时加上提交日志：

    $ git commit -m "本次提交的日志记录"

也可以跳过 `git add`，直接暂存并提交已跟踪过的文件：

    $ git commit -a

## 查看提交历史

我们可以通过以下命令查看提交历史

    $ git log

## 克隆现有仓库

获取一份已存在的 Git 仓库的拷贝：

    $ git clone [URL] [本地仓库名]

SVN 类似的命令是 `checkout`，但 SVN `checkout` 命令只是获取 repo 的最新版本文件，而 `git clone` 则是获取远程 Git 仓库中的每一个文件的每一个版本（默认配置下）。

## 远程仓库

### 查看远程仓库

远程仓库是指托管在因特网或其它网络中的我们的项目的版本库。如果想查看我们已经配置的远程仓库服务器，可以运行 `git remote` 命令，它会列出指定的每一个远程服务器的简写。

    $ git remote

如果想查看远程仓库及其 URL，可加上 `-v` 参数：

    $ git remote -v

### 添加远程仓库

我们可以执行以下命令来添加一个新的远程 Git 仓库，并指定仓库的简写：

    $ git remote add <remote-repo-shortname> <URL>

### 从远程仓库获取数据

从远程仓库中获取我们本地还没有的数据，可以执行：

    $ git fetch [remote-repo-shortname]

执行完成后，我们将拥有该远程仓库中全部分支的引用，可以查看或合并。

### 推送数据到远程仓库

如果我们想要推送数据到远程仓库，可以执行：

    $ git push [remote-repo-shortname] [branch-name]

### 移除远程仓库

    $ git remote rename [old remote-repo-shortname] [new remote-repo-shortname]

### 重命名远程仓库

    $ git remote rm [remote-repo-shortname]

---

# 参考资料

* [Pro Git - 2.1 Git 基础 - 获取 Git 仓库](https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E8%8E%B7%E5%8F%96-Git-%E4%BB%93%E5%BA%93)
* [Pro Git - 2.2 Git 基础 - 记录每次更新到仓库](https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E8%AE%B0%E5%BD%95%E6%AF%8F%E6%AC%A1%E6%9B%B4%E6%96%B0%E5%88%B0%E4%BB%93%E5%BA%93)
* [Pro Git - 2.5 Git 基础 - 远程仓库的使用](https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E8%BF%9C%E7%A8%8B%E4%BB%93%E5%BA%93%E7%9A%84%E4%BD%BF%E7%94%A8)
