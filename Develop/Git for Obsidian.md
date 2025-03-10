[Getting Started - Git Documentation - Obsidian Publish](https://publish.obsidian.md/git-doc/Getting+Started)

好像就是嵌入Obsidian的Git界面，用起来跟VSCode差不多，配置仓库相关还是需要借鉴[[Git & GitHub]]。

在Obsidian自带的命令行中，也可以直接运行Git相关命令，实在用不惯直接在VSCode内操作即可。

# Desktop

> For existing remote repository.

为了从远端服务器上克隆仓库，跟其他代码仓库一样，必须要先有一个远程远程URL。可以是 `https` 或 `ssh`，当然这依赖于认证 `Authentication` 方法，例子：

- `https`: `https://github.com/<username>/<repo>.git`
- `ssh`: `git@github.com:<username>/<repo>.git`

1. 先安装，需要VPN代理，在Obsidian插件市场中安装。跟随 `Installation` 指示，根据自己的操作系统。
2. 设置 `Authentication`
3. `Git` 只能将一个远程 `repo` 克隆到一个新的文件夹中。因此，可以有两个选择：
	- 使用命令 `Clone an exising remote repository` 来将自己的远程仓库克隆到本地笔记仓库(vault)中，作为一个子文件夹。然后进一步，可以有两个选择：
		- 将所有的文件从新文件夹(包括 `.git`！)移动到自己的笔记仓库根目录中。
		- 将新的子文件夹打开，作为一个新的笔记仓库。这里可能需要重新安装插件。
	- 在任何你想要作为笔记仓库的位置，运行 `git clone <your-remote-url>` 命令行中。
4. 学习如何最佳的配置 `.gitignore`。

# Authentication

要安全地、永久地存储用户名和密码，而不必一直重新输入，可以使用 `Git` 的 `Credential Helper`。`libsecret` 可以将密码存储在一个安全的地方。
