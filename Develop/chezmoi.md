[chezmoi.io](https://www.chezmoi.io/)

>Manage your dotfiles across multiple diverse machines, securely.

# Concepts

大致来说，`chezmoi` 会把我们的 `dotfiles` 的期望状态存储在目录：`~/.local/share/chezmoi` 中。当运行 `chezmoi apply` 命令时，chezmoi 会计算我们每一个配置文件所需的内容，然后做出最小的更改，使得我们的 dotfiles 匹配我们所需的状态。

在Windows中，存储目录位于：

- `Users/.local/share/chezmoi`
- `C:\Users\<user>\.local\share\chezmoi`

# Preparation

## Install

[Releases · twpayne/chezmoi (github.com)](https://github.com/twpayne/chezmoi/releases)

在Windows系统上，下载 `amd64` 版本即可。对于很多其他的系统平台，都有对应的版本进行下载。而且，chezmoi也可以通过大部分包管理工具进行安装。

如果没有使用包管理工具进行下载，需要将chezmoi的目录加入到系统环境变量中。

## GitHub Repo

[Create a new repository on GitHub](https://github.com/new) called `dotfiles` and be ready to push our repo.

# Start Using

[Quick start - chezmoi](https://www.chezmoi.io/quick-start/)

在安装完成，添加完系统变量后，直接运行命令

```Shell
chezmoi init
```

进行初始化。该命令会创建一个新的本地 git 仓库，位于 `~/.local/share/chezmoi` ，chezmoi会在该目录中存储它的源数据。默认情况下，chezmoi只会修改工作副本中的文件。

与 git 类似，chezmoi可以通过以下命令添加文件：

```Shell
chezmoi add ~/.bashrc
```

该命令会将 `.bashrc` 文件复制到 `~/.local/share/chezmoi/dot_bashrc` 中。

如果需要编辑，可以使用命令 `chezmoi edit [filename]`，打开我们系统中的 `$EDITOR` 并编辑文件。当然，我们可以使用任何编辑器进行编辑。可以使用 `chezmoi diff` 命令来查看chezmoi做出了哪些改动，如果我们需要应用改动的话，使用 `chezmoi -v apply` 命令。

所有的 `chezmoi` 命令都接收 `-v` (verbose) 标记来确切地输出它们会对文件系统做出的改动，`-n` (dry run) 标记用于不作出任何实际的改动。`-n -v` 的组合非常有用，如果我们想要看一下实际的改动具体是什么的话。

```Shell
chezmoi --help
```

上述命令可以查看所有chezmoi相关的、具体命令的名称和内容。

# Commands

[Command overview - chezmoi](https://www.chezmoi.io/user-guide/command-overview/)

首次与仓库进行同步时，在完成 `chezmoi init` 的情况下，可以使用命令组：

```Shell
chezmoi cd # 打开工作chezmoi文件目录
git add .
git commit -m "Initial commit"
```

提交完成后，设置GitHub远程仓库，确定分支，并推送到远程仓库中：

```Shell
git remote add origin git@github.com:$GITHUB_USERNAME/dotfiles.git
git branch -M main
git push -u origin main
```

>[!Hint]
>chezmoi can be configured to automatically add, commit, and push changes to your repo.

完成后，我们可以直接运行 `exit` 退回到之前的工作目录。`chezmoi` 命令会切换到chezmoi模式(?)，exit可以退出该模式，而不退出整个命令行。

具体的chezmoi工作流可以在网页中查看，总的来说，该工作流主要包含四个仓库状态：

- `home directory`：配置文件实际的位置
- `working copy`：实际配置文件的一个副本，可以编辑
- `local repo`：本地(裸)仓库，用于与远端同步
- `remote repo`：实际的远程同步仓库

## Upload

### `add <file>`

`chezmoi add <filename>` 可以将文件从本地文件添加到工作目录 `working copy` 中，该步骤只创建一个副本。`chezmoi edit <file>` 可以用于编辑该工作副本。

### `git add/commit/push`

编辑完成后，使用 `git add` 或 `git commit` 命令将该副本加入到 `local repo` 中。在版本控制信息记录完成后，使用命令 `git push` 将本地仓库中的内容推送到 `remote repo` 。

## Download

### `chezmoi diff`

查看chezmoi会对我们本地实际的配置文件做出什么改动。

### `chezmoi apply -v`

如果我们认可这些改动，则直接应用，该命令会将工作副本实际写回到那些配置文件中。

### `chezmoi edit $FILE`

如果不认可那些改动，可以编辑这些工作副本文件。

### `chezmoi merge $FILE`

或者，我们可以通过 `merge` 命令来合并这些最新状态的不同文件改动。此处的文件指那些配置文件的副本，和实际的文件内容。

### `chezmoi update -v`

在任何机器上，我们都可以通过该命令来拉取(pull)并应用(apply)那些来自于远程仓库(remote repo)中的最新改动。

# Using chezmoi across multiple machines

在另外的、我们需要配置的“新”机器上，我们可以直接用我们的仓库进行初始化：

```Shell
chezmoi init <dotfiles repo url>
```

该命令会检查仓库及其所有子模块，并且选择性地创建一个chezmoi配置文件。接下来，继续使用 `chezmoi diff` 来查看chezmoi会对我们的本地文件做出哪些改动，也就是对比本地工作副本和远程仓库的文件内容。

如果确认的话，我们只需要执行 `chezmoi apply -v` 即可。当然，如果有新的需要，我们也可以对其进行编辑，只要执行：`chezmoi edit $FILE`。
