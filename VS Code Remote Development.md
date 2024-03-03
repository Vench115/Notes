# Source

[Visual Studio Code Remote Development](https://code.visualstudio.com/docs/remote/remote-overview)

# VS Code extension

1. 在VSCode里下载扩展(包)。

The **Remote Development extension pack** includes four extensions. See the following articles to get started with each of them:

- Remote-SSH - Connect to any location by opening folders on a remote machine/VM using SSH.
- Dev Containers - Work with a separate toolchain or container-based application inside (or mounted into) a container.
- WSL - Get a Linux-powered development experience in the Windows Subsystem for Linux.
- Remote-Tunnels - Connect to a remote machine via a secure tunnel, without configuring SSH.

While most VS Code extensions should work unmodified in a remote environment, extension authors can learn more at [Supporting Remote Development](https://code.visualstudio.com/api/advanced-topics/remote-extensions).

## Remote tutorials

The tutorials below will walk you through running Visual Studio Code with the Remote Development extensions.

| Tutorial | Description |
| ---- | ---- |
| [Remote via SSH](https://code.visualstudio.com/docs/remote/ssh-tutorial) | Connect to remote and virtual machines with Visual Studio Code via SSH. |
| [Work in WSL](https://code.visualstudio.com/docs/remote/wsl-tutorial) | Run Visual Studio Code in Windows Subsystem for Linux. |
| [Develop in Containers](https://code.visualstudio.com/docs/devcontainers/tutorial) | Run Visual Studio Code in a Docker Container. |
| [GitHub Codespaces](https://docs.github.com/github/developing-online-with-codespaces/using-codespaces-in-visual-studio-code) | Connect to a codespace with Visual Studio Code. |

# Via SSH

[Connect over SSH with Visual Studio Code](https://code.visualstudio.com/docs/remote/ssh-tutorial)

This tutorial walks you through creating and connecting to a virtual machine (VM) on Azure using the Visual Studio Code [Remote - SSH](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh) extension. You'll create a Node.js Express web app to show how you can edit and debug on a remote machine with VS Code just like you could if the source code was local.

> **Note**: Your Linux VM can be hosted anywhere - on your local host, on premise, in Azure, or in any other cloud, as long as the chosen Linux distribution meets these [prerequisites](https://code.visualstudio.com/docs/remote/linux#_local-linux-prerequisites).

2. 下载SSH扩展，下载完成后，VS Code左下角会出现新的状态条。

这个新的状态条可以立马告诉你，现在VS Code工作在什么环境中 (local or remote)。点击这个状态条，可以呼出Remote-SSH的命令操作。

此时，我们需要有一个虚拟机（Virtual Machine)，未来可能需要创建（购买）新的虚拟机，并获取其端口、地址等等。该教程中使用的是Azure，但是我们自己的VM可以部署在任何环境。

## Set up SSH

指在虚拟机(VM)上配置SSH，可以使用多种方法，例如：SSH公/私钥对，或者用户名和密码。VS Code推荐使用基于密钥的授权方式，因为如果使用用户名和密码的话，SSH扩展会多次要求输入密码。

让系统生成密钥的方式在[[Git & GitHub#远程仓库使用协议 ssh]]中有完整提及。注意生成不同种密钥时，对于密钥的使用存在不同。

## Add SSH key to your VM

将先前生成的SSH密钥加入VM，可以在第一次登录以后的登录中，不用繁琐地再次输入密码。

> Take the public key and paste it into your VM setup, by copying the entire contents of the `id_ed25519.pub` in the **SSH public key**.

在运用SSH连接VM之后，可以在文件 `/.ssh/authorized_keys` 中加入自己的 `id_ed25519.pub` 公钥内容，以省去多次输入密码的麻烦。

## Connect using SSH

生成本机的密钥之后，我们可以使用SSH来连接到VM。

Now that you've created an SSH host, let's connect to it!

You'll have noticed an indicator on the bottom-left corner of the Status bar. This indicator tells you in which context VS Code is running (local or remote). Click on the indicator to bring up a list of Remote extension commands.

3. 选择 `Connect to Host ... (Remote-SSH)` 命令，通过输入VM的信息来连接到远程主机。

此处通过配置用户根目录中 `C:/Users/username/.ssh/config` ，可以方便地利用状态条进行快捷选择以及连接到远程主机。

配置的内容如下：

```
Host hostname
  HostName host_IP
  User username
  Port port_id
```

4. 配置完成后，重新使用左下角的SSH状态条呼出命令，并进行连接。

注意此时的网络环境，可能需要使用OpenVPN进行连接。

## Ending your SSH connection

You can end your session over SSH and go back to running VS Code locally with **File** > **Close Remote Connection**

# Environments setting

安装配置环境时，尽量不要使用 `conda install` ，可能会有莫名的报错。

注意CUDA版本号和torch版本的匹配问题。

`nvidia-smi` 可以查看CUDA版本号。
