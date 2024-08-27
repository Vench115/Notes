# 学习资源

[【GitHour】Git教程](https://www.bilibili.com/video/BV1HM411377j/?spm_id_from=333.337.search-card.all.click)

强烈推荐GitHub官方的文档，可以适时查阅使用：[GitHub Docs](https://docs.github.com/zh)

[Git Tutorials and Training | Atlassian Git Tutorial](https://www.atlassian.com/git/tutorials)

>记录和梳理一下本地仓库与远程仓库（GitHub）的交互流程，以备不时之需。

# 快速建库

此处可以参考GitHub中的配置仓库部分(QcccccQ)

## 存在本地仓库

1. `git init`建立本地仓库，即创建.git文件
	1. `echo "# This is a Readme file" >> README.md`
	2. `git add README.md`
	3. `git commit -m "first commit"`
3. `git branch -M main`设置主分支名称
4. `git remote add origin <远程仓库url地址>`添加远程仓库
5. `git push -u origin main`完成创建后，将本地仓库推送至远程仓库

## 从远程仓库clone

首先，创建一个远程库，或者找到一个已经存在的库，并获取该库的`url`。

其次，找到一个存储该仓库的位置，不需要新建一个文件夹，因为`git clone`操作会将远程仓库的整个仓库，以文件夹的方式`clone`下来。

可以通过`Git Bash`命令行，输入命令：

```shell
cd <仓库存储位置根目录>
```

随后，可以直接初始化仓库，然后通过命令：

```shell
git clone <仓库url>
```

将远程仓库内容直接同步到本地仓库中即可，这里可能会遇到身份验证的问题，例如使用SSH加密方法。

---

# 本地仓库初始化

```shell
git init
```

在Git Bash中直接键入该命令，本地仓库会完成初始化，并创建一个名为：`.git`的隐藏文件夹，可以使用命令：`ls -a` 查看到。

```shell
git init <仓库名称>
```

使用该命令可以在当前目录下，创建一个以输入的仓库名称为名字的git管理的仓库。

**配置用户名**：

```shell
git config --global user.name "USER_NAME"
```

此时，如果不加参数`--global`，那么则表示您想要在当前目录中配置与 Git 提交关联的名称的本地仓库。

**设置提交电子邮件地址**：

```shell
git config --global user.email "USER_EMAIL"
```

同样的，此时不加参数`--global`，那么则表示您想要在当前目录中配置与 Git 提交关联的名称的本地仓库。

---

# 从远程仓库 clone

```shell
git clone <仓库地址>
```

使用该命令会在当前目录中，直接克隆一个远程仓库。

> 后续有更详细的讲解。

---

# 创建远程仓库 remote

在GitHub上Create一个Repository，然后将该远程仓库的**网址**复制下来。

随后，在Git Bash里，键入命令：

```shell
git remote add <远程仓库名 默认origin> <远程仓库url>
```

将远程仓库链接到本地仓库中，再使用命令：

```shell
git remote
```

查看远程仓库是否链接完成，以及远程仓库的命名。

此时，也可以使用命令：

```shell
git remote rename <原名> <新名>
```

来重新命名远程仓库**在本地仓库中**显示的名字。

```shell
git remote -v
```

上述命令可以查看远程分支的命名，以及其具体的`url`地址。

```shell
git remote set-url --add <远程分支名> <仓库url>
```

该命令可以新增远程仓库的地址，也可以用参数`--delete`删除，做到更改具体仓库地址的操作。

---

# 远程仓库使用协议 ssh

[通过 SSH 连接到 GitHub - GitHub 文档](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh)

远程仓库的网址采用两种方式`HTTPS`和`SSH`：
- HTTPS在我们将本地代码push到远程仓库时，需要验证用户名和密码
- SSH不需要验证用户名和密码，但是需要再GitHub上添加SSH公钥的配置

更推荐SSH的方式，更安全也更加方便，当然，使用SSH方式必须配置SSH的密钥。

[检查现有 SSH 密钥 - GitHub 文档](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/checking-for-existing-ssh-keys)

打开Git Bash，输入指令 `ls -al ~/.ssh` 以查看是否存在现有的 SSH 密钥。

```shell
ls -al ~/.ssh
# Lists the files in your .ssh directory, if they exist
```

可以查看是否存在现有的SSH密钥。

[生成新的 SSH 密钥并将其添加到 ssh-agent - GitHub 文档](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

进入用户根目录中的`.ssh`目录中，使用命令：

```shell
ssh-keygen -t rsa -b 4096
# or
ssh-keygen -t ed25519 -C "your_email@example.com"
# ed25519 recommended
```

来生成我们的SSH密钥。`-t`指定协议为RSA / ed25519，`-b`指定大小为4096，`-C`指定用户。

回车以后，会提示我们需要输入密钥的文件名称，如果我们是第一次使用这个命令，那么直接回车即可。它会在我们用户根目录中的`.ssh`目录中生成一个`id_rsa`的密钥文件。

如果之前配置过SSH密钥，那么此时最好不要直接回车，因为之前已经生成过`id_rsa`这个密钥文件，如果再次回车，它会覆盖掉我们之前的密钥文件，该操作是不可逆的。所以此时，我们需要输入一个新的文件名。

刚刚的操作会生成两个新文件：没有任何扩展名的`<密钥文件名>`即私钥文件，有扩展名的`<公钥文件名.pub>`的就是公钥文件。

我们需要打开公钥文件，然后复制公钥文件的内容，回到GitHub页面，点击右上角我们的头像，在`settings`中找到`SSH and GPG keys`，即密钥配置选项。

在SSH Keys中点击`New SSH key`按钮，然后将刚刚生成的公钥内容粘贴到下面的输入框中，在`title`中任意输入一个名称即可。

如果到这里我们是第一次创建SSH密钥，那么就完成了。如果刚刚我们指定了一个新的文件名，那么还需要增加一步配置，我们需要创建一个`config`文件，并且将下面的内容添加入文件中。

```text
# github
Host github.com
HostName github.com
PreferredAuthentications publickey
IdentifyFile ~/.ssh/test
```

以上内容的意思是：当我们访问GitHub时，指定使用`.ssh`下的`test`这个密钥。

---

# 在其他环境与仓库同步

在其他环境指的是，我们需要在**全新的环境**中进行配置同步，此时我们需要配置SSH key，感觉如果采用了该方法，则需要先生成一个本地的SSH key，并将其添加到GitHub上才可以使用。

同时，不要忘记配置我们的**用户名**、**邮箱**和**密码**。

```shell
git config --global user.name "USER_NAME"
git config --global user.email "USER_EMAIL"
```

可以通过在终端 `Git Bash` 中输入 `ssh -T git@github.com` 来测试本地密钥是否正常工作：

```shell
$ ssh -T git@github.com
# Attempt to SSH in to github
> Hi USERNAME! You've successfully authenticated, but GitHub does not provide
> shell access.
```

如果没有可用的本地密钥，那么则需要：[新增 SSH 密钥到 GitHub 帐户 - GitHub 文档](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)

为 GitHub.com 上的帐户添加新 SSH 身份验证密钥后，可以重新配置任何本地存储库以使用 SSH。 有关详细信息，请参阅“[管理远程仓库](https://docs.github.com/zh/get-started/getting-started-with-git/managing-remote-repositories#switching-remote-urls-from-https-to-ssh)”。

**将远程 URL 从 HTTPS 切换到 SSH：**

1. 打开 `Git Bash`
2. 将当前工作目录更改为我们的本地仓库
3. 列出现有远程仓库以获取要更改的远程仓库的名称

```shell
$ git remote -v
> origin  https://github.com/OWNER/REPOSITORY.git (fetch)
> origin  https://github.com/OWNER/REPOSITORY.git (push)
```

4. 使用 `git remote set-url` 命令将远程 URL 从 HTTPS 更改为 SSH。

```shell
git remote set-url origin git@github.com:OWNER/REPOSITORY.git
```

5. 验证远程 URL 是否已更改。

```shell
$ git remote -v
# Verify new remote URL
> origin  git@github.com:OWNER/REPOSITORY.git (fetch)
> origin  git@github.com:OWNER/REPOSITORY.git (push)
```

使用 `git remote rm` 命令从存储库中删除远程 URL。`git remote rm` 命令采用一个参数：远程名称（例如 `destination`）。

从存储库中删除远程 URL 只会取消本地和远程存储库的链接，它不会删除远程存储库。

完成以后，下次将 `git fetch`、`git pull` 或 `git push` 执行到远程存储库时，系统将要求你提供 GitHub 用户名和密码。

当 Git 提示你输入密码时，请输入你的personal access token，[[Git & GitHub#个人访问令牌]] 。 或者，可以使用 [Git 凭据管理器](https://github.com/GitCredentialManager/git-credential-manager/blob/main/README.md)等凭据帮助程序。

通过上述内容创建完细粒度个人访问令牌后，就可以在命令行上使用了。

我们可以在通过 HTTPS 执行 Git 操作时输入它，而不是密码。

例如，若要在命令行上克隆存储库，请输入以下 `git clone` 命令。 然后，系统会提示你输入用户名和密码。 当系统提示输入密码时，请输入 personal access token 而不是密码。

```shell
$ git clone https://github.com/USERNAME/REPO.git
Username: YOUR_USERNAME
Password: YOUR_PERSONAL_ACCESS_TOKEN
```

Personal access token 只能用于 HTTPS Git 操作。 如果存储库使用 SSH 远程 URL，则需要[将远程 URL 从 SSH 切换到 HTTPS](https://docs.github.com/zh/get-started/getting-started-with-git/managing-remote-repositories#switching-remote-urls-from-ssh-to-https)。

如果没有提示您输入用户名和密码，说明您的凭据可能已缓存在计算机上。 可[在密钥链中更新凭据](https://docs.github.com/zh/get-started/getting-started-with-git/updating-credentials-from-the-macos-keychain)，从而用令牌替换旧密码。

可以使用 Git 客户端缓存 personal access token 而不是为每个 HTTPS Git 操作手动输入 personal access token。 Git 会将您的凭据临时存储在内存中，直到过期为止。 您还可以将令牌存储在 Git 可以在每个请求之前读取的纯文本文件中。 有关详细信息，请参阅“[在 Git 中缓存 GitHub 凭据](https://docs.github.com/zh/get-started/getting-started-with-git/caching-your-github-credentials-in-git)”。

---

# 将文件添加到仓库 add

## 查看仓库状态 status

```shell
git status
```

可以查看现在处于哪个分支，有哪些文件以及这些文件都处于什么文件状态。

## 暂存文件 add

```shell
git add
```

将文件添加到暂存区，等待后续的提交操作。文件显示绿色，表示已经加入暂存区，等待被提交；文件显示红色，表示未加入版本控制或者尚未加入暂存区。

此处可以使用**通配符**，例如：
- \*.txt (任何以.txt结尾的文件)
- . (所有文件)
- 文件夹名

## 提交文件 commit

```shell
git commit -m "提交附带的注释信息"
```

文件只有被提交到仓库中，才可以算是真正被保管起来。该命令只会提交**暂存区**中的文件，而不会提交工作区中的其他文件。

这个命令在提交时，需要使用`-m`参数来指定提交的信息(message)，这个信息会被记录到仓库中。如果此时不指定`-m`这个参数，那么commit命令会进入一个交互式的界面，默认会使用vim来编辑提交信息，在我们的设置中，会使用VSCode。

小技巧，在该命令中加入参数`-a`：

```shell
git commit -a -m "提交附带的注释信息"
```

或者更加简洁的方式：

```shell
git commit -am "提交附带的注释信息"
```

可以在一个命令中完成添加暂存和提交两个动作，该技巧只对已经添加过的文件生效，如果是新文件就不能使用`-a`的参数来完成添加暂存。

## 查看提交记录 log

```shell
git log
```

使用该命令查看提交情况，其中包含：
- 每一次提交都有一个唯一的提交ID，就是commit后的16进制字符串
- 每次提交的作者和邮箱
- 提交的时间
- 提交时编写的注释信息
- ……

```shell
git log --oneline
```

可以查看更简洁的提交记录，只显示每次提交的ID和提交信息。

```shell
git reflog
```

在Git中，几乎所有的操作都是可以回溯的，以上命令就可以用于查看所有操作的历史记录，可以找到误操作之前的版本号，然后再使用`git reset`命令来回退到这个版本就可以了。

但是，这里要声明的是，只有**提交的内容**才可以恢复，本次修改还没有提交的内容会丢失。

## 对远程仓库操作

[[Git & GitHub#创建远程仓库]]

执行完上述步骤后，可以使用命令：

```shell
git remote -v
```

查看我们当前仓库所对应的远程仓库的别名和地址。

并指定分支名称，使用命令：

```shell
git branch -M <主分支名称>
```

## 推送到远程仓库 push

关联本地仓库与远程仓库，使用命令：

```shell
git push -u <远程分支名称> <本地分支名称>
```

更完整版的命令为：

```shell
git push --set-upstream <远程分支名称> <本地分支>:<远程仓库分支>
```

如果本地分支名称与远程分支的名称相同，可以省略`:<远程仓库分支>`。

## 拉取自远程仓库 pull

GitHub上的文件是可以在线编辑的，也就是远程仓库的修改，此时，我们需要在本地仓库上同步远程仓库的修改。

```shell
git pull <远程仓库名称> <远程分支名>:<本地分支名>
```

如果省略了仓库名称和分支名称，那么就是默认拉取origin仓库的main分支，即：

```shell
git pull origin main
```

作用就是拉取远程仓库的指定分支到本地，再进行合并，这个合并操作是Git自动执行的。

如果本地内容和远程内容没有冲突的话，那么合并操作就会成功，否则，合并操作就会因为冲突而失败。此时，我们需要手动来解决冲突。

## 获取远程仓库修改 fetch

```shell
git fetch <远程仓库名称> <远程分支名>:<本地分支名>
```

该命令只是获取远程仓库的修改，而并不会自动合并到本地仓库中，我们需要手动合并这些修改。

---

# 查看文件差异 diff

```shell
git diff
```

该命令可以用来查看：
- 文件在工作区、暂存区、版本仓库之间的差异
- 两个文件在**特定版本之间**的差异
- 文件在**两个分支**之间的差异

平时在开发过程中，我们一般用图形化界面GUI来查看，但是如果遇到没有图形化工具，比如服务器上来使用Git时，就有必要了解一下diff命令。

不加任何参数的`git diff`默认比较的是工作区和暂存区之间的差异内容，它会显示发生更改的文件以及更改的详细信息。

第一行显示发生变更的文件，第二行40位hash值表示文件，最后六位表示文件的权限。红色表示删除内容，绿色表示新增内容。

查看**工作区**与**版本库**之间差异的命令：

```shell
git diff HEAD
```

查看**暂存区**与**版本库**之间差异的命令：

```shell
git diff --cached
```

比较文件在两个版本之间差异的命令：

```shell
git diff <比较提交版本ID 1> <比较提交版本ID 2>
```

可以用上一个提交版本的ID或者`HEAD~`，与当前版本的最新提交`HEAD`来比较。此时如果用`HEAD~3`即表示`HEAD`之前的第三个版本。

单独查看某一个文件在两个版本之间差异的命令：

```shell
git diff <比较提交版本ID 1> <比较提交版本ID 2> <文件名称>
```

查看两个分支之间的差异：

```shell
git diff <比较的分支名1> <比较的分支名2>
```

---

# 分支操作 branch

## 查看与创建

通过命令：

```shell
git branch
```

来查看当前仓库的所有分支，在各类可视化GUI工具中，也可以看到仓库的分支情况。在分支名字前，有星号`*`的表示当前所处的分支。分支的命名一般是有一些含义的，比如`dev`表示开发版本，`feat`表示开发新功能的分支等等。

```shell
git branch <分支名>
```

通过上述命令，我们可以创建一个新的分支，或者使用可视化界面中的`branch`按钮，创建新的分支。

## 切换分支 checkout

此时，我们可以发现，`git branch`只是创建了分支，而没有切换到分支上。那么，我们可以通过命令：

```shell
git checkout <分支名称>
```

来切换到不同的分支上。

## 只切换分支 switch

上述使用`checkout`切换分支时，可能会遇到一些潜在的问题，因为`git checkout`除了可以切换分支和状态之外，`git checkout`还可以用来恢复文件或目录到之前的某一个状态。

例如，我们意外地修改了某个文件，此时我们就可以使用`git checkout`命令来恢复到我们修改之前的状态。此时，如果分支名和文件名相同，就会出现歧义，`git checkout`会**默认切换分支**，而不是恢复文件。

为了避免这种歧义，Git在2.23版本开始，为我们提供了一个新的命令：

```shell
git switch <分支名称>
```

它是专门用来切换分支的，语义更加明确。

在切换分支时，文件也会相应发生变化，因为不同分支上的改动还没有合并`merge`。在实际开发中，这种分支并行的情况更加常见，因为我们的分支开发就是并行的。

```shell
git checkout -b <回退分支名> <提交ID>
```

以上命令可以恢复到该分支在`提交ID`时间点的状态，提交ID可以在可视化界面中，或者使用`git log --oneline --graph --decorate --all`命令来查看。

## 合并分支 merge

```shell
git merge <将要被合并的分支名>
```

通过`git merge`命令可以将不同分支合并到**当前分支**中，当前分支也是合并后的目标分支。

执行完上述命令后，Git会自动为我们产生一次提交，此时我们就需要输入提交的注释消息，随后也可以在分支可视化工具上看到合并的效果。

我们也可以通过命令：

```shell
git log --graph --oneline --decorate --all
```

来查看分支的结构图，虽然没有可视化工具上那样美观，但是该分支图也可以清楚地展现出分支的创建、开发、合并的过程。

再使用`git branch`命令查看分支情况，可以看到被合并的分支依然存在，如果我们真真正正地不需要一个分支，那么我们可以使用命令：

```shell
git branch -d <要删除的分支名>
```

来删除这个分支，`-d`表示删除已经完成合并的分支，也就是说，如果一个分支已经被合并到其他分支中了，那么我们就可以使用该命令来删除这个分支。但是，如果分支没有完成合并，我们是不可以用`-d`这个参数来删除的。

```shell
git branch -D <要强制删除的分支名>
```

大写的`-D`参数可以强制删除某个分支。

---

# 解决合并冲突

如果两个分支的修改部分没有任何重复的话，那么合并分支就非常简单，Git会为我们自动完成合并。但是，如果两个分支修改了同一个文件的同一行代码，Git就不知道应该保留哪个分支的修改内容了，这也就产生了冲突`conflict`。

使用命令`git merge`后，Git就会提示我们产生了冲突：

```shell
CONFLICT (content): Merge conflict in [发生冲突的文件名]
Automatic merge failed; fix conflicts and then commit the result.
```

此时，我们可以用命令`git status`来查看冲突文件的列表，或者，我们可以用`git diff`命令来查看冲突的具体内容，Git会将两个分支的修改内容全部显示出来，Git会使用左箭头`<<<`、等号`===`、右箭头`>>>`分别来表示两个分支的修改内容。

那么现在我们就需要做的就是手工编辑这个文件，留下我们想要的内容后，再重新提交。可以去掉左箭头、等号、右箭头，并且选择留下自己想要的内容（在可视化工具中同理），这样我们就把两个冲突分支的修改内容合在了一起。

最后，保存退出，再添加到暂存区，并用附带注释信息提交后，就自动完成了我们的合并过程。

在**提交之前**，如果我们想中断这次合并，我们也可以使用命令：

```shell
git merge --abort
```

来终止此次合并。

---

# 回退版本 reset

```shell
git reset <回归方式> <要回归到的版本ID>
```

用于回退版本，可以退回到之前的某一个提交的状态

```shell
git reset --soft <要回退到的版本ID>
```

表示回退到某一个版本，以”软“的方式，即**保留**工作区和暂存区的**所有**修改内容。

```shell
git reset --hard <要回退到的版本ID>
```

表示回退到某一个版本，以”硬“的方式，但是**丢弃**工作区和暂存区的**所有**修改内容。

```shell
git reset --mixed <要回退到的版本ID>
```

表示回退到某一个版本，以”适中“的方式，但只**保留工作区**的修改内容，而**丢弃暂存区**的修改内容。`--mixed`也是该命令的默认参数，我们需要根据不同场景使用不同参数。

上一个版本可以用`HEAD^`来表示，`HEAD`指向当前分支的**最新提交**。

---

# 变基 rebase

我们可以在**任意分支**上执行`rebase`操作，也就是可以在`main`分支执行，也可以在其他开发的分支上执行。

例如：
- 在`dev`开发分支上进行`rebase`操作，那么`dev`上的提交记录就都会变基到`main`分支的末尾；
- 在`main`分支上执行`rebase`操作，那么`main`分支上的提交记录都会变基到`dev`分支的末尾。

执行`rebase`之后，最后的结果都是一条直线，但是提交记录的顺序会有些不同，这是由于`rebase`的机制导致的。

在Git中，每个分支都有一个指针，指向**当前分支**的**最新提交记录**。

在执行`rebase`操作时，Git会找到当前分支和目标分支的**共同祖先**，再把当前分支上，从共同祖先到最新提交记录的所有提交都移动到目标分支的最新提交之后。简单来说，这个过程就像嫁接移植一样，从分叉点把整个分支都移动到目标分支的最新提交记录后面。

首先，我们需要切换到被变基的分支上：

```shell
git rebase <被变基的分支名>
```

执行上述命令后，再执行命令：

```shell
git rebase <变基的目标分支名>
```

意为：将当前的分支**变基到**目标的分支上。

**`git merge`**：
- 优点：不破坏原分支的提交历史，方便回溯和查看。
- 缺点：会产生额外的提交记录和两条分支线的合并，使得提交记录变得复杂。

**`git rebase`**：
- 优点：不需要新增额外的提交记录到目标分支，可以形成一个线性的提交历史记录，较为直观和干净。
- 缺点：会改变提交历史，改变当前分支branch out的节点。

我们需要**避免**在一个**共享分支**上进行`rebase`操作，因为这样会对和你一起在这个分支上进行开发的同事造成一些困扰，所以一般不会在公共的分支上执行rebase操作。

两个命令没有绝对的优劣，需要视具体情况而选择使用。

---

# 删除文件 rm

## 直接删除

一般的删除方式略显复杂，可以使用命令：

```Shell
git rm <需要删除的文件名>
```

可以直接将文件从工作区和暂存区中**同时删除**，最后通过`git commit -m "删除文件xxx"`来删除版本库中的该文件。

仅删除暂存区：
```Shell
git rm --cached <file name>
```

以上命令可以实现将文件去除版本控制，可见链接：[git把某个文件去除版本控制_git取消文件的版本控制_CSDN](https://blog.csdn.net/HakuMaster/article/details/127645288?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_utm_term~default-8-127645288-blog-122438868.235^v38^pc_relevant_sort_base1&spm=1001.2101.3001.4242.5&utm_relevant_index=11)。

递归删除某个目录下的**所有**子目录和文件
```Shell
git rm -r *
```

删除后不要忘记提交`commit`。

## 一般删除

直接在文件夹中删除文件，举例使用Linux系统命令：`rm`来删除，此处`rm`并非指Git中的`git rm`。在Windows操作系统中，直接将文件删除到回收站中即可。

删除文件后使用`git status`可以看到Git发现我们删除了文件，显示为红色的<font color=red> deleted </font>。

这里需要声明，为何还需要使用`git add`命令添加到暂存区，因为我们只是在工作区中删除了文件，暂存区中的文件并没有被删除。

查看暂存区中的内容：

```shell
git ls-files
```

通过这个命令，我们可以发现暂存区中并没有删除我们已经在工作区中删除的文件，所以我们可以理解成，需要告诉Git我们删除了这个文件，通过命令：

```shell
git add <已经删除的文件名>
```

将文件从暂存区中删除。

---

>[!callout]
>以下是一些Git相关的概念

# 工作区域

## 工作区 Working Directory

我们实际操作的目录，就是本地电脑上的文件夹。

## 暂存区 Staging Area / Index

临时存储区（中间区域），保存**即将提交**到Git仓库的**修改内容**，是进行版本控制中非常重要的一个区域。

## 本地仓库 Local Repository

通过`git init`命令创建的仓库，包含了完整的项目历史和元数据，是Git存储代码和版本信息的**主要位置**。

## 提交过程

修改工作区的文件→添加`git add`到暂存区→提交`git commit`到本地仓库

过程中，我们可以使用Git命令来查看、比较或者撤销修改。

---

# 文件状态

- **未跟踪 Untracked**
	- 新创建的，尚未被Git管理起来的文件。
- **未修改 Unmodified**
	- 已经被Git管理起来，但是内容没有发生变化的。
- **已修改 Modified**
	- 已经被我们修改过的文件，但是尚未添加到暂存区中。
- **已暂存 Staged**
	- 已经添加到暂存区域内的文件。

---

# 分支 Branch

Git中很重要的内容，我们可以将其看作代码库中的**不同版本**，可以独立存在，并且有自己的提交记录。就像是一棵树的不同枝干，每个枝干都有自己的生长轨迹，所以它被形象地称为分支。

多个开发人员可以在自己的分支上进行开发工作，最后再合并到主线代码库中，我们也可以直接在一个分支上进行新功能的开发，或者建立一个问题修复的分支来处理一些bug和缺陷。

通过这种方法，我们就可以让主线代码仓库处在一个**随时可用**的比较**稳定**的状态，而不会影响到其他功能的开发和测试，保证了项目的正常运行和高效协作。分支的优点就是可以提高团队协作的效率，减少冲突和错误的影响，让团队中的每个人都能够独立开发和测试。

分支管理：在实践中，为了保证分支管理的顺畅和有效，我们需要制定明确的**分支管理流程**和**规范**，同时加强分支的**保护**和**权限控制**，避免常见的分支管理错误。

---

# GitFlow 模型

在该模型中，通常有五种类型的分支，每种类型都有自己的用途。在实际使用中，很多人不会完全遵守GitFlow模型，因为其适用于团队技术水平适中，有一定的开发流程和规范的团队。

## 主线/基线分支 (main)

- 项目的**核心分支**，包含了项目的最新的稳定版本的代码，我们应该随时保证主线分支中的代码是可以发布的。
- 一般来说，主线分支的代码会被部署到生产环境中
- 主线分支中的代码是**不可以直接修改**的，只能通过合并分支的方式来修改。
- 每次合并分支都建议生成一个新的版本号，这样可以方便追踪和回溯，可以通过`git tag`的命令来标记版本号，版本号的规则：[[Git & GitHub#版本号]]

## 问题修复分支 (hotfix)

- 包含了项目某个问题修复的源码，用于修复线上的问题
- 一般来说，问题修复分支是从主线分支中分离出来的，修复完成后会合并到主分支和开发分支中
- 当问题修复分支合并完成之后，一般会将这个问题修复分支删除掉
- 问题修复分支一般也会更新我们的小版本号

## 开发分支 (develop)

- 从主线分支中分离出来，包含了项目的最新开发版本的代码，用于开发和测试
- 项目的另一条**核心分支**，和`main`分支一样是一个长期存在的分支

## 功能分支 (feature)

- 包含了项目某个新功能的代码，用于开发新的功能
- 从开发分支中分离出来
- 当功能分支的代码稳定后，会以某种方式合并回开发分支中
- 最后再按照版本发布的计划执行版本发布的流程

## 版本发布分支 (release)

- 也叫预发布分支
- 包含了项目最新预发布版本的代码，用于发布前的测试和验证
- 一般来说，预发布分支是从开发分支中分离出来的
- 当预发布分支的代码稳定后，会合并到主分支和开发分支中，然后会将预发布分支删除
- 在测试验证的过程中发现的问题，会在预发布分支中进行修改、提交，并且cherry-pick到开发分支中

## 版本号

常见的版本号规则如下，主要构成：

```text
Tag:1(主版本号).2(次版本号).3(修订版本)
```

- 主版本 (Major Version)：主要的功能变化或重大更新
- 次版本 (Minor Version)：一些新的功能、改进和更新，通常不会影响现有功能
- 修订版本 (Patch Version)：修复一些小的问题或缺陷，以及一些安全漏洞的补丁等等，通常不会影响现有的功能和接口。

---

# GitHubFlow 模型

适用于一些技术水平比较高的团队或者开源项目，相对于GitFlow模型，GitHubFlow模型更加简单明了。

只有一个长期存在的主分支，而且主分支上的代码可以直接部署到生产环境中，一般会设置分支保护，禁止团队成员直接在主分支上进行提交。

团队成员可以从主分支中分离出自己的分支进行开发和测试，然后在本地分支提交代码。等到开发完成后，可以发起一个`Pull Request`，简称PR，中文名叫拉请求或合并请求。团队成员可以对代码进行Review评审，如果没有问题，就可以将这个PR发布和合并到主分支中，整个流程就完成了。

好习惯：分支的命名、创建、合并规范等等
- 分支命名：
	- 推荐使用带有意义的描述性名称来命名分支
	- 版本发布应该根据版本号命名，例如：v1.0.1
	- 功能性分支应该根据其功能和任务命名，例如：feature-login-page
	- 修补补丁和错误应该根据其问题编号命名，例如：hotfix-#issueid-desc
- 分支管理：
	- 定期合并成功验证的分支，及时删除已经合并的分支
	- 保持合适的分支数量，确保及时将功能性分支合并回主分支
	- 为分支设置合适的管理权限

---

# 换行符相关

[git 处理和修改行结束符（CRLF和LF）](https://blog.csdn.net/ccfxue/article/details/52625806?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169494981316800197087016%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=169494981316800197087016&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-52625806-null-null.142^v94^insert_down1&utm_term=git%20crlf&spm=1018.2226.3001.4187)

如果我们在Windows平台上写程序，或者我们在与其他人协作时，我们处在不同的系统上进行开发，那么我们可能会遇到行尾结束符的问题。

这是因为Windows使用`回车`和`换行`两个字符来结束一行，而Mac和Linux只使用`换行`这一个字符。虽然这看着是一个小问题，但是它会极大地绕轮平台协作。

为解决这个问题，Git可以在你提交时，自动地把行结束符`CRLF`转换成`LF`，而在签出代码时把`LF`转换成`CRLF`。用`core.autocrlf`来打开此项功能，如果是在Windows系统上，把它设置成 true，这样当签出代码时，LF 会被转换成 CRLF。

```shell
git config --global core.autocrlf true
```

Linux 或 Mac 系统使用`LF`作为行结束符，因此你不想 Git 在签出文件时进行自动的转换；当一个以 CRLF 为行结束符的文件不小心被引入时，你肯定想进行修正，那么就把`core.autocrlf`设置成`input`来告诉 Git 在提交时把 CRLF 转换成 LF，签出时不转换。 

```shell
git config --global core.autocrlf input
```

参考上面的配置方法，你就可以在 Windows 系统上，签出文件时保留 CRLF，而在 Mac 和 Linux 系统上，包括仓库中，保留 LF 。  

如果你是 Windows 程序员，且正在开发仅运行在 Windows 上的项目，可以设置`false`取消此功能，把回车符记录在库中。

```shell
git config --global core.autocrlf false
```

---

# 个人访问令牌

**Personal Access Token**

Git 的基于密码的身份验证已被删除，以支持更安全的身份验证方法。有关详细信息，请参阅“[管理个人访问令牌](https://docs.github.com/zh/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)”。

可以[使用凭据帮助程序](https://docs.github.com/zh/get-started/getting-started-with-git/caching-your-github-credentials-in-git)，以便 Git 每次与 GitHub 通信时都会记住你的 GitHub 用户名和 personal access token。

使用 [GitHub API](https://docs.github.com/zh/rest/overview/authenticating-to-the-rest-api) 或[命令行](https://docs.github.com/zh/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#using-a-personal-access-token-on-the-command-line)时，可使用 Personal access token 替代密码向 GitHub 进行身份验证。

## 令牌的类型

GitHub 目前支持两种类型的 personal access token：fine-grained personal access token 和 personal access tokens (classic)。GitHub 建议尽可能使用 fine-grained personal access token 而不是 personal access tokens (classic)。

组织所有者可以设置策略来限制 personal access tokens (classic) 对其组织的访问。

如果选择使用 personal access token (classic)，请记住它将授予对有权访问的组织内的所有存储库以及个人帐户中的所有个人存储库的访问权限。

作为安全预防措施，GitHub 会自动**删除**一年内未使用过的 personal access token。 为了提供进一步的安全性，强烈建议将过期时间添加到 personal access token。

## 确保 personal access token 安全

Personal access token 类似于密码，它们具有相同的固有安全风险。 创建新的 personal access token 之前，请考虑是否有更安全的身份验证方法可供使用：

- 若要从命令行访问 GitHub，可以使用 [GitHub CLI](https://docs.github.com/zh/github-cli/github-cli/about-github-cli) 或 [Git 凭据管理器](https://github.com/GitCredentialManager/git-credential-manager/blob/main/README.md)，而不是创建 personal access token。
- 在 GitHub Actions 工作流中使用 personal access token 时，请考虑是否可以改用内置 `GITHUB_TOKEN`。 有关详细信息，请参阅“[自动令牌身份验证](https://docs.github.com/zh/actions/security-guides/automatic-token-authentication)”。

如果无法使用这些选项，但必须创建 personal access token，请考虑使用其他 CLI 服务安全地存储令牌。

在脚本中使用 personal access token 时，可以将令牌存储为机密，并通过 GitHub Actions 运行脚本。 有关详细信息，请参阅“[在 GitHub Actions 中使用机密](https://docs.github.com/zh/actions/security-guides/encrypted-secrets)”。还可以将令牌存储为 Codespaces 机密，并在 Codespaces 中运行脚本。 有关详细信息，请参阅“[管理 codespace 的机密](https://docs.github.com/zh/codespaces/managing-your-codespaces/managing-encrypted-secrets-for-your-codespaces)”。