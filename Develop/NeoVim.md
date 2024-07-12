>Config NeoVim from scratch

# 视频资源

[0 to LSP：Neovim RC From Scratch_bilibili](https://www.bilibili.com/video/BV1HM4m1Z7ex/?spm_id_from=333.880.my_history.page.click&vd_source=5c0fccc3e62acb7264f3cd05395b00c0) 视频中提到的Packer.nvim已经不再维护

[从零开始配置 Neovim(Nvim) - MartinLwx's Blog](https://martinlwx.github.io/zh-cn/config-neovim-from-scratch/) (MacOS)

[glepnir/nvim-lua-guide-zh](https://github.com/glepnir/nvim-lua-guide-zh)

[Neovim从新手到高手系列之轻松安装LazyVim](https://www.bilibili.com/video/BV1uE421u7xE/?spm_id_from=333.880.my_history.page.click&vd_source=5c0fccc3e62acb7264f3cd05395b00c0) (Linux)

[The Only Video You Need to Get Started with Neovim (youtube.com)](https://www.youtube.com/watch?v=m8C0Cq9Uv9o&t=11s)

[NvChad](https://nvchad.com/)


# Intro

NeoVim中支持通过Lua脚本进行配置，在此之前，一般使用VimScript进行脚本配置，这继承自Vim，目前已经逐渐转向Lua。

# Install

在NeoVim的官网：[Home - Neovim](https://neovim.io/)，下载NeoVim。解压缩后，可以得到可运行的NeoVim，它是一个基于Qt的程序。

完成之后，将安装目录下的 `\bin` 文件夹添加至系统环境变量中，便于在任意目录下使用 `nvim` 直接在命令行中打开NeoVim。

通过在NeoVim中运行命令 `:lua require("xxx")` 发现，好像NeoVim并不需要在Lua环境下进行工作。而且其配置文件，在Windows系统中需要位于目录：

```text
C:/Users/xxx/AppData/Local/nvim
C:/Users/xxx/AppData/Local/nvim-data
```

在Linux系统中，配置文件的位置涉及到 `XDG_CONFIG_HOME` 、`XDG_CONFIG_DIRS` 等系统环境变量的具体位置值。

# Config

[在Windows下配置NeoVim](https://www.bilibili.com/video/BV1CY411N7xf/?spm_id_from=333.337.search-card.all.click&vd_source=5c0fccc3e62acb7264f3cd05395b00c0)

配置脚本安装位置，可以在nvim程序中通过命令 `:help init.lua` 查看具体的安装位置。简略描述如下：

```text
Unix: ~/.config/nvim/init.vim (or init.lua)
Windows: ~/AppData/Local/nvim/init.vim (or init.lua)
|$XDG_CONFIG_HOME|: $XDG_CONFIG_HOME/nvim/init.vim	(or init.lua)
```

在对应的目录中，就可以写入我们需要的 .vim 或 .lua 配置文件。(%HOME% = ~/AppData/Local)

`:checkhealth` 或`LazyHealth` 命令让nvim自己检测自身，以及插件等等。

nvim会在启动时，直接找到配置路径下的文件，并执行，例如 `init.lua` 文件。任何Lua中的目录都可以通过 `require()` 关键字进行引用，就如同index.html一般，括号内必须为文件名。

## WezTerm

一个好看的，可以在Windows上使用的命令行工具，具体配置下载等信息可以参考 [[WezTerm]] 文档。

## Python

一个Python环境是可选的，用于在命令行中直接执行Python代码。目前暂时没有涉及通过 Anaconda 的环境运行Python脚本，或者通过 SSH 远程运行脚本。

将Python安装文件夹 `Python` 以及 `Scipts` 添加至系统环境变量Path中。

# Plugins

通过LazyVim来管理所有的插件，即管理.lua/plugins中所有的.lua插件文件即可。

## LazyVim

~~[wbthomason/packer.nvim: A use-package inspired plugin manager for Neovim.](https://github.com/wbthomason/packer.nvim)~~

在0 to LSP的视频中提到，使用 `packer.nvim` (~~[packer.nvim: A use-package inspired plugin manager for Neovim.](https://github.com/wbthomason/packer.nvim)~~)进行nvim的插件管理，但是其已经不再被维护，也有了更好更稳定的替代者：`LazyVim` 。

[🛠️ Installation | LazyVim](https://www.lazyvim.org/installation)

网络问题可能会导致lazy.vim的安装出现问题，表现在启动nvim时，会报错：`No Module named 'lazy'`。也有可能是备份的问题，在删除nvim以及nvim-data文件夹之后，重新进行 `git clone` 能够成功安装LazyVim。

如果初步的lazy.nvim.starter安装成功，那么重新启动nvim时，lazyvim会开始工作。安装lazyvim默认相关的插件时，会自动安装 telescope.nvim，一个用于模糊搜索的插件。


# Keymaps

在主界面中，使用Shift+/可以呼出按键解释菜单。或者可以通过网页：[⌨️ Keymaps | LazyVim](https://www.lazyvim.org/keymaps)查看按键映射。

在LazyVim的默认配置文件中，已经通过 `vim.g.mapleader = " "` 将空格键设置为映射引导按键，这是一个较为常见的、便于后续命令行使用的配置。该配置位于配置Lazyvim后，默认存在的文件：`~/AppData/Local/nvim/lua/config/keymaps.lua` 。

所以，现在 `<space>sh` 命令可以搜索 Help 文档。

同时，这种设置可以通过在nvim中键入命令来执行：`:lua vim.opt.number = false` 可以将nvim中的当前行行号关闭，而转为显示0。

`: Tutor` 命令可以唤出NeoVim Tutorial，该文档被默认关闭，以节省时间。替代方案，可以在网页打开