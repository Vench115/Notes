# 配置 Lua 环境

## 相关网页链接

Lua官网：[Lua: download](https://www.lua.org/download.html)

~~教程：[Lua 环境安装 | 菜鸟教程 (runoob.com)](https://www.runoob.com/lua/lua-environment.html)~~

由于菜鸟教程中提到的GitHub中的Lua for Windows版本较为老旧，故在Lua官网中下载最新的版本进行环境配置。

## Building

此处描述的流程基于Windows系统，其他系统，例如Linux可以借鉴别的教程。

1. 在官网中下载最新的Lua release ([Lua: download area](https://www.lua.org/ftp/))
2. 解压之前下载的压缩包 (lua-5.x.x.tar.gz)
3. 在类Unix系统中，使用make进行安装，具体的安装指引可以在Lua文件夹中的readme查看
	1. 利用 `MinGW` 打开Lua的顶级目录
	2. 运行 `make` 命令，此时可能会报错
	3. 若报错，使用命令 `make help` 查看运行的平台是否被包含
	4. 运行指定的平台，此处使用的是 `make mingw`
4. 完成上述命令，使用命令 `make test` 检查是否成功

若成功，命令行输出为：

```shell
$ make test
make[1]: Entering directory '/c/Users/lua-5.4.7/src'
./lua -v
Lua 5.4.7  Copyright (C) 1994-2024 Lua.org, PUC-Rio
make[1]: Leaving directory '/c/Users/lua-5.4.7/src'
```

## Installing

完成Build Lua之后，我们需要将其安装在一个公开的位置上。使用命令 `make install INSTALL_TOP=xxx` 来完成安装（以及Build），其中 `xxx` 是我们选择的安装目录。

若不指定安装位置，那么Lua会被安装到 Linux 中默认的位置：`/usr/local/xxx` 。该位置在Windows系统中不存在(应该)，所以会导致安装失败。同时，也要注意安装是基于 `src` 和 `doc` 文件夹的位置，所以 `INSTALL_TOP` 需要为绝对路径。

安装完成后，在我们所选的安装目录下，Lua 会呈现如下的结构：

```text
bin:
	lua luac
include:
	lua.h luaconf.h lualib.h lauxlib.h lua.hpp
lib:
	liblua.a
man/man1:
	lua.1 luac.1
```

上述的文件目录结构即为开发所需的所有目录，如果我们想要运行Lua程序，那么只需要 `bin` 和 `man` 文件夹。`include` 和 `lib` 文件夹中的内容，当我们需要在 `C/C++` 程序中嵌入Lua时，才会用到。

最后，添加 `lua/bin` 路径到系统变量中，完成Lua的配置工作。
