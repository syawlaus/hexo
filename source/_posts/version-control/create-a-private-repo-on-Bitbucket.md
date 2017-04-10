title: 在 Bitbucket 创建私人项目仓库
date: 2016-01-28 17:30
categories: 版本控制
---

最近需要把我的一个个人项目放到网上保存，因为 GitHub 免费用户不支持远程私人 repo，所以就放到 Bitbucket 吧。

<!-- more -->

放到 Bitbucket 的步骤跟 GitHub 是一样的，只是创建远程 repo 时设置为 private 而已。

首先，如果我们没有 Bitbucket 帐号，注册一个。

接下来在 Bitbucket 创建一个远程 repo，Repositories --> Create repositoriy，创建时默认就是 private 的。

![](/images/version-control/create-a-private-repo-on-Bitbucket/create-a-new-repository.png)

接下来需要把本地 repo 推送到远程 repo。

首先假设本地 repo 已使用 Git 管理，如果还没有，请参考《[Git 常用命令](http://syawlaus.github.io/blog/version-control/git-commands/)》一文。

然后 `cd` 到本地 repo 的目录，执行

    $ git remote add [remote-repo-shortname] git@bitbucket.org:syawlaus/remote-repo-shortname.git

这命令的作用是，把 `[remote-repo-shortname]` 和 URL `git@bitbucket.org:syawlaus/remote-repo-shortname.git` 映射起来。

然后再执行

    $ git push -u [remote-repo-shortname] --all

把本地 repo 数据推送到远程 repo。其中：

* `-u` 表示对于推送成功的每个分支，都加上**上流（跟踪）引用（upstream (tracking) reference）**，详见[这里](http://stackoverflow.com/questions/5697750/what-exactly-does-the-u-do-git-push-u-origin-master-vs-git-push-origin-ma)。
* `--all` 表示推送全部分支。

如果输出：

    Permission denied (publickey).
    fatal: Could not read from remote repository.
    
    Please make sure you have the correct access rights
    and the repository exists.

说明我们本机的 SSH key 还没通过 Bitbucket 的验证。

首先先在本地生成 SSH key.

---

To be continued.
