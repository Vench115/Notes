[chezmoi.io](https://www.chezmoi.io/)

>Manage your dotfiles across multiple diverse machines, securely

# Concepts

大致来说，`chezmoi` 会把我们的 `dotfiles` 的期望状态存储在目录：`~/.local/share/chezmoi` 中。当运行 `chezmoi apply` 命令时，chezmoi 会计算我们每一个配置文件所需的内容，然后做出最小的更改，使得我们的 dotfiles 匹配我们所需的状态。

在Windows中，存储目录位于：`Users/.local/share/chezmoi`

# Pre
## Install

[Releases · twpayne/chezmoi (github.com)](https://github.com/twpayne/chezmoi/releases)

在Windows系统上，下载 `amd64` 版本即可。对于很多其他的系统平台，都有对应的版本进行下载。而且，chezmoi也可以通过大部分包管理工具进行安装。

如果没有使用包管理工具进行下载，需要将chezmoi的目录加入到系统环境变量中。

## GitHub Repo

[Create a new repository on GitHub](https://github.com/new) called `dotfiles` and be ready to push our repo.

# Start Using

在安装完成，添加完系统变量后，直接运行命令 `chezmoi init` 进行初始化。该命令会创建一个新的本地 git 仓库，位于 `~/.local/share/chezmoi` ，在这里chezmoi会存储它的源数据。默认情况下，chezmoi只会修改工作副本中的文件。

与 git 类似，chezmoi可以通过以下命令添加文件：

```Shell
chezmoi add ~/.bashrc
```

该命令会将 `.bashrc` 文件复制到 `~/.local/share/chezmoi/dot_bashrc` 中。

如果需要编辑，可以使用命令 `chezmoi edit [filename]`，打开我们系统中的 `$EDITOR` 并编辑文件。当然，我们可以使用任何编辑器进行编辑。可以使用 `chezmoi diff` 命令来查看chezmoi做出了哪些改动，如果我们需要应用改动的话，使用 `chezmoi -v apply` 命令。

所有的 `chezmoi` 命令都接收 `-v` (verbose) 标记来确切地输出它们会对文件系统做出的改动，`-n` (dry run) 标记用于不作出任何实际的改动。`-n -v` 的组合非常有用，如果我们想要看一下实际的改动具体是什么的话。

```Shell
chezmoi
```

直接在命令行中输入，可以查看所有相关的、具体命令的名称和内容。

# Commands

```Shell
chezmoi cd # 打开工作chezmoi文件目录
git add .
git commit -m "Initial commit"
```

提交完成后，在github仓库中进行同步。

```Shell
git remote add origin git@github.com:$GITHUB_USERNAME/dotfiles.git
git branch -M main
git push -u origin main
```

>[!Hint]
>chezmoi can be configured to automatically add, commit, and push changes to your repo.

完成后，我们可以直接 `exit` 退回到之前的工作目录。

# Using chezmoi across multiple machines

在另外的、我们需要配置的“新”机器上，我们可以直接用我们的仓库进行初始化：

```Shell
chezmoi init [dotfiles repo url]
```

该命令会检查仓库及其所有子模块，并且选择性地创建一个chezmoi配置文件。接下来，继续使用 `chezmoi diff` 来查看chezmoi会对我们的本地文件做出哪些改动，也就是对比本地文件和远程仓库的内容。

如果确认的话，我们只需要执行 `chezmoi apply -v` 即可。当然，如果不行，我们也可以进行编辑，只要执行：`chezmoi edit $FILE`。
