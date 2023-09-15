> 记录一下本地仓库与远程仓库（GitHub）的交互流程。

# 本地仓库初始化

使用命令

```git
git init
```

即可，本地仓库中会创建一个名为：.git的隐藏文件夹。

# 创建远程仓库

在GitHub上new一个repository，然后将该远程仓库的**网址**复制下来。

随后，在Git Bash里，键入命令：

```git
git remote add <远程仓库名 默认origin> <远程仓库url>
```

将远程仓库链接到本地仓库中，再使用命令：

```git
git remote
```

查看远程仓库是否链接完成，以及远程仓库的命名。

此时，也可以使用命令：

```git
git remote rename <原名> <新名>
```

来重新命名远程仓库**在本地仓库中**显示的名字。

