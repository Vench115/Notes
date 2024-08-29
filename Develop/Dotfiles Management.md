# Intro

Dotfiles 代指 Linux/Mac 中各种配置文件。我们希望有一个方法可以集中管理它们，便于在切换不同机器时，能快速恢复熟悉的工作环境。目前比较流行的做法是 [使用 git 来管理](https://dotfiles.github.io/)，借助 orphan branch 还可以给不同系统/机器建立不同的分支，分开管理。

[How to Store Dotfiles - A Bare Git Repository | Atlassian Git Tutorial](https://www.atlassian.com/git/tutorials/dotfiles)

Perfect technique to mange config dotfiles, learned from [[NeoVim]]. And the only pre-requisite is install Git.

In his words the technique below requires:

>No extra tooling, no symlinks, files are tracked on a version control system, you can use different branches for different computers, you can replicate you configuration easily on new installation.

这个配置文件管理的技巧，包含存储一个Git裸仓库，在一个辅助(side)文件夹中，例如 `$HOME/.cfg` 或 `$HOME/.myconfig` ，只需要使用一个精心编写的 `alias` ，就可以让指令在仓库上运行，而不是在常规的 `.git` 文件夹上运行，那样会干扰周围的其他任何Git仓库。

# 学习资源

[Dotfiles 管理-使用 git 裸仓库 - 晨鹤部落格](https://chenhe.me/post/dotfiles-guan-li-shi-yong-git-luo-cang-ku)，汉化版，同样是在Linux中的实现方法。

利用 git 裸仓库。它可以在**原地**跟踪Dotfiles，无需移动，自然也无需软链接。此方案来源 (YouTube)：[Git Bare Repository - A Better Way To Manage Dotfiles](https://www.youtube.com/watch?v=tBoLDpTWVOM)，讲解清晰。

GNU Stow (YouTube - typecraft)：[NEVER lose dotfiles again with GNU Stow (youtube.com)](https://www.youtube.com/watch?v=NoFiYOqnC4o&t=69s)

不过，在 `Windows` 系统中，没有 `alias` 命令，需要寻找替代方案。Maybe，在多种平台上进行配置文件管理的软件：[chezmoi - Quick Start](https://www.chezmoi.io/quick-start/)

# Git Bare Repository

>Git裸仓库

在Git中，仓库可以分为两种类型：工作目录（working directory）和裸仓库（bare repository）。裸仓库是一个**没有工作目录**的Git仓库，它只包含版本历史信息和索引，没有文件副本。它只是一个包含Git版本控制所需的元数据的文件夹。

裸仓库通常用于共享代码的中央仓库，在团队协作中扮演着重要的角色。由于裸仓库没有工作目录，因此不能在上面进行实际的编辑和修改文件的操作，仅仅是用来存储并共享版本控制的历史记录。

# Start from Scratch

在Windows系统中，没有默认配置Linux中的 `$HOME` 系统变量，用于指明一个工作目录，需要手动配置 `.cfg` 文件夹所处的位置。

使用下述命令组，进行准备工作

```Shell
git init --bare $HOME/.cfg`
```

创建一个文件夹 `~/.cfg` ，这是第一个git bare repository，用于追踪我们的文件。

```Shell
alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'
``` 

创建一个 `alias` 的 `config` (命令)取代我们往常与配置文件仓库交互时所使用的 `git` 。其中，`config` 依然会使用 `git` ，不过 `config` 会指定交互仓库的工作目录为：`$HOME/.cfg/` ，也就是我们用于同步的裸仓库位置。而整个工作目录则是 `$HOME`，也就是说，我们可以同步工作目录中，所有我们需要的 `dotfiles` 。

```Shell
config config --local status.showUntrackedFiles no
```

设置一个对于该仓库来说，本地的flag，来隐藏那些我们尚未进行追踪的文件。这是为了之后当我们输入 `config status` 或者其他命令时，我们不关心的文件不会显示为 `untracked` 。

```Shell
echo "alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'" >> $HOME/.bashrc
```

同样地，我们也可以在我们的 `.bashrc` 中手动加入alias定义，或者为了方便使用上述的命令行。

在执行了上述设置之后，`$HOME` 中的任意文件都可以通过一般指令进行版本控制，通过我们新创建的 `config` 命令来替换 `git` 命令，例如：

```Shell
config status
config add .vimrc
config commit -m "Add vimrc"
config add .bashrc
config commit -m "Add bashrc" config push
...
```

# Install our dotfiles

如果我们已经在某个Git仓库上存储了我们自己的配置文件，那么接下来的步骤可以让我们在一台新的系统上安装该配置文件。

在安装之前，确保我们已经向自己的 `.bashrc` 或 `zsh` 中commit了 `alias` ，使用命令：

```Shell
alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'
```

而且，为了使我们的源仓库 (source repository) 忽略我们即将要克隆到它的文件夹中的文件，所以我们可以运行下列命令行，以便于我们不会引发奇怪的递归问题

```Shell
echo ".cfg" >> .gitignore
```

现在，我们可以clone我们的dotfiles，到一个git裸仓库中，也就是我们的 `$HOME` 中的 "dot" 文件：

```Shell
git clone --bare <git-repo-url> $HOME/.cfg
```

然后在最近的Shell范围中确定alias：

```Shell
alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'
```

完成上述内容后，切换到 (checkout) 确定裸仓库中的实际内容，

```Shell
config checkout
```

上述步骤有可能会失败，错误内容为：

```Shell
error: The following untracked working tree files would be overwritten by checkout:
.bashrc
.gitignore Please move or remove them before you can switch branches. Aborting
```

这是因为我们标记的 `$HOME` 文件夹中已经有一些库存配置文件，而这些文件会被Git覆写。解决方法也很简单：如果你真的需要这些文件，直接备份它们即可，如果不需要的话，把它们删掉也可以。下述命令可以自动地把这些冲突的文件，移动到一个备份文件夹中：

```Shell
mkdir -p .config-backup && \
config checkout 2>&1 | egrep "\s+\." | awk {'print $1'} | \
xargs -I{} mv {} .config-backup/{}
```

如果有问题的话，重新运行前述的命令：

```Shell
config checkout
config config --local status.showUntrackedFiles no
```

至此，前导内容配置完毕，现在我们可以键入 `config` 命令来增加或者更新我们的dotfiles：

```Shell
config status
config add .vimrc
config commit -m "Add vimrc"
config add .bashrc
config commit -m "Add bashrc"
config push
```

同样地，作为一个快捷方式，我们不必在任何想要设置的新机器上记住所有这些步骤。我们可以创建一个简单的脚本，像作者一样将其存储为Bitbucket snippet，并且为它创建一个简短的url，并像这样调用它：

```Shell
curl -Lks http://bit.do/cfg-install | /bin/bash
```

为了让该教程更加全面，下面的代码块是作者的脚本，其已经在作者新配置的Alpine Linux上测试验证过了。

```Shell
git clone --bare https://bitbucket.org/durdn/cfg.git $HOME/.cfg

function config {
    /usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME $@
}

mkdir -p .config-backup

config checkout

if [ $? = 0 ]; then
    echo "Checked out config.";
else
    echo "Backing up pre-existing dot files.";     config checkout 2>&1 | egrep "\s+\." | awk {'print $1'} | xargs -I{} mv {} .config-backup/{} fi;

config checkout
config config status.showUntrackedFiles no
```
