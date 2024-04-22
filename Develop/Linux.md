> 随手记录一些常用的指令或技巧

[Linux 命令大全 | 菜鸟教程 (runoob.com)](https://www.runoob.com/linux/linux-command-manual.html)

[Linux 常用命令集合 | 菜鸟教程 (runoob.com)](https://www.runoob.com/w3cnote/linux-common-command.html)

[Linux 常用命令全拼 | 菜鸟教程 (runoob.com)](https://www.runoob.com/w3cnote/linux-command-full-fight.html)

任意命令 + `--help` 显示更完整的、更详细的选项和使用方法。

# 磁盘管理

## `pwd`

print work directory 命令用于显示工作目录。执行 `pwd` 指令可立刻得知目前所在的工作目录的绝对路径名称。

## `ls`

list directory contents 命令用于显示指定工作目录下之内容（列出目前工作目录所含的文件及子目录)。

```shell
ls -l                # 以长格式显示当前目录中的文件和目录
ls -a                # 显示当前目录中的所有文件和目录，包括隐藏文件
ls -lh               # 以人类可读的方式显示当前目录中的文件和目录大小
ls -t                # 按照修改时间排序显示当前目录中的文件和目录
ls -R                # 递归显示当前目录中的所有文件和子目录
ls -l /etc/passwd    # 显示/etc/passwd文件的详细信息
```

当文件名包含空格、特殊字符或者开始字符为破折号时，可以使用反斜杠 `\` 进行转义，或者使用引号将文件名括起来。

ls 命令还可以使用通配符进行模式匹配，例如 `*` 表示匹配任意字符，`?` 表示匹配一个字符，`[...]` 表示匹配指定范围内的字符。例如：

```shell
ls *.txt         # 列出所有扩展名为.txt的文件
ls file?.txt     # 列出文件名为file?.txt的文件，其中?表示任意一个字符
ls [abc]*.txt    # 列出以a、b或c开头、扩展名为.txt的文件
```

在使用 `ls -l` 命令时，第一列的字符表示文件或目录的类型和权限。其中第一个字符表示文件类型；第一列的其余 9 个字符表示文件或目录的访问权限，分别对应三个字符一组的 rwx 权限：r 表示读取权限、w 表示写入权限、x 表示执行权限、- 表示没有对应权限。
## `cd`

change directory 命令用于改变当前工作目录的命令，切换到指定的路径。若目录名称省略，则变换至使用者的 home 目录 (也就是刚 login 时所在的目录)。例子：

```shell
cd [dirName]  # [dirName] 要切换的目标目录，可以是相对路径或绝对路径
cd /path/to/dircetory  # 切换到绝对路径 /path/...
cd relative/path/to/directory  # 切换到相对路径
cd $VAR_NAME  # 切换到环境变量指定的目录
```

另外，`~` 表示为 home 目录的意思； `.` 则是表示目前所在的目录； `..` 则表示目前目录位置的上一层目录； `cd -` 切换到上次使用的目录。

## `mkdir`

make directory 命令用于创建目录。

```shell
mkdir [-p] dirName  # [-p]确保目录名称存在，不存在的就建一个
```

## `du`

disk usage 命令用于显示目录或文件的大小。`du` 会显示指定的目录或文件所占用的磁盘空间。

```shell
du  # 显示目录或者文件所占的空间 只显示当前目录下面的子目录的目录大小和当前目录的总的大小，最后一行为当前目录的总大小
du -h  # 方便阅读的格式显示目录所占空间情况
```

## `df`

disk free 命令用于显示目前在 Linux 系统上的文件系统磁盘使用情况统计。

```shell
df
Filesystem     1K-blocks    Used     Available Use% Mounted on 
/dev/sda6       29640780 4320704     23814388  16%     / 
udev             1536756       4     1536752    1%     /dev 
tmpfs             617620     888     616732     1%     /run 
none                5120       0     5120       0%     /run/lock 
none             1544044     156     1543888    1%     /run/shm 
```

第一列指定文件系统的名称，第二列指定一个特定的文件系统1K-块，1K是1024字节为单位的总内存。用和可用列正在使用中，分别指定的内存量。使用列指定使用的内存的百分比，而最后一栏"安装在"指定的文件系统的挂载点。

`df` 也可以显示磁盘使用的文件系统信息：

```shell
df test 
Filesystem     1K-blocks    Used      Available Use% Mounted on 
/dev/sda6       29640780    4320600   23814492  16%       / 
```

其他例子：

```shell
df -i  # 显示inode信息而非块使用量
df --total  # 显示所有信息
df -h  # 产生可读的格式df命令的输出
```

## `tree`

tree 命令用于以树状图列出目录的内容。执行 `tree` 指令，它会列出指定目录下的所有文件，包括子目录里的文件。

# 文件管理

## `find`

find 命令用于在指定目录下查找文件和目录。它可以使用不同的选项来过滤和限制查找的结果。

```shell
find [路径] [匹配条件] [动作]
find . -name file.txt  # 查找当前目录下名为 file.txt 的文件
find . -name "*.c"  # 将当前目录及其子目录下所有文件后缀为 .c 的文件列出来
find /home -size +1M  # 查找 /home 目录下大于 1MB 的文件
```

## `rm`

remove 命令用于删除一个文件或者目录。

参数：
- `-i` 删除前逐一询问确认。
- `-f` 即使原档案属性设为唯读，亦直接删除，无需逐一确认。
- `-r` 将目录及以下之档案亦逐一删除。

删除文件可以直接使用rm命令，若删除目录则必须配合选项"-r"。

```shell
rm -r *  # 删除当前目录下的所有文件及目录
```

>文件一旦通过rm命令删除，则无法恢复，所以必须**格外小心**地使用该命令。

## `cat`

concatenate 命令用于连接文件并打印到标准输出设备上。

```shell
cat -n textfile1 > textfile2  # 把 textfile1 的文档内容加上行号后输入 textfile2 这个文档里
cat /dev/null > /etc/test.txt  # 清空 /etc/test.txt 文档内容

-n 或 --number - 由 1 开始对所有输出的行数编号
-b 或 --number-nonblank - 和 -n 相似，只不过对于空白行不编号
-s 或 --squeeze-blank - 当遇到有连续两行以上的空白行，就代换为一行的空白行
-E 或 --show-ends - 在每行结束处显示 $
```
## `touch`

touch 命令用于修改文件或者目录的时间属性，包括存取时间和更改时间。若文件不存在，系统会建立一个新的文件。`ls -l` 可以显示档案的时间记录。

# 系统管理

## `top`

top 命令是一个在 Linux 和其他类 Unix 系统上常用的实时系统监控工具。它提供了一个动态的、交互式的实时视图，显示系统的整体性能信息以及正在运行的进程的相关信息。使用权限：所有使用者。

```shell
top -i  # 不显示闲置(idle)或无用的进程
top -d 3  # 设置信息更新间隔3秒
top -p 139  # 显示指定的进程信息
top -c  # 显示完整信息而不截断
top -b  # 以批处理模式显示程序信息
top -S  # 以累积模式显示程序信息
top -n 2  # 设置信息更新次数，达到次数后终止更新显示
```

`top` 命令的功能和交互操作：

- 按键命令：在 top 运行时可以使用一些按键命令进行操作，如按下 "k" 可以终止一个进程，按下 "h" 可以显示帮助信息等。
- 排序：可以按照 CPU 使用率、内存使用率、进程 ID 等对进程进行排序。
- 刷新频率：可以设置 top 的刷新频率，以便动态查看系统信息。

## `free`

free 命令用于显示内存状态。free指令会显示内存的使用情况，包括实体内存，虚拟的交换文件内存，共享内存区段，以及系统核心使用的缓冲区等。

```shell
free  # 显示内存使用信息
free -t  # 以总和的形式查询内存的使用信息
free -s 10  # 每10s执行一次命令
```

## `ps`

process status 命令用于显示当前进程的状态，类似于 windows 的任务管理器。

```shell
ps -ef | grep <进程关键字>
ps -ef | grep php  # 显示php的进程
ps -ef  # 显示所有进程信息，包括命令行
```

## `kill`

kill 命令用于删除执行中的程序或工作。`kill` 可将指定的信息送至程序。预设的信息为 SIGTERM(15)，可将指定程序终止。若仍无法终止该程序，可使用 SIGKILL(9) 信息尝试强制删除程序。程序或工作的编号可利用 `ps` 指令或 `jobs` 指令查看。

常用的信号：1(HUP)：重新加载进程；9(KILL)：杀死一个进程；15(TERM)：正常停止一个进程。

```shell
kill 12345  # 杀死进程12345
kill -KILL 123456  # 强制杀死进程123456
kill -HUP pid  # 对进程发送SIGHUP信号
kill -9 123456  # 彻底杀死进程123456
```

## `sudo`

sudo命令以系统管理者的身份执行指令，也就是说，经由 sudo 所执行的指令就好像是 root 亲自执行。使用权限：在 /etc/sudoers 中有出现的使用者。

```shell
sudo -l  # 显示出自己(执行sudo的使用者)的权限
```

## `apt`

Advanced Packaging Tool 命令是一个在 Debian 和 Ubuntu 中的 Shell 前端软件包管理器。apt 命令提供了查找、安装、升级、删除某一个、一组甚至全部软件包的命令，而且命令简洁而又好记。apt 命令执行需要超级管理员权限(root)。

```shell
sudo apt update  # 查看可更新的包
sudo apt upgrade  # 升级安装包
sudo apt update && sudo apt upgrade -y  # 将以上两个命令组合起来
sudo apt remove <package_name>=<version_number>  # 指定安装版本
sudo apt list  # 列出可更新的包
sudo apt autoremove  # 清理不再使用的依赖和库

sudo apt install < package_name> --no-upgrade  # 只想升级而不要安装
--only-upgrade  # 只升级 若包不存在则不安装
```

## `reboot`

reboot 命令用于用来重新启动计算机。若系统的 runlevel 为 0 或 6 ，则重新开机，否则以 shutdown 指令（加上 -r 参数）来取代。

## `shutdown`

命令可以用来进行关机程序，并且在关机以前传送讯息给所有使用者正在执行的程序，shutdown 也可以用来重开机。使用权限：系统管理者。

```shell
shutdown -r now  # 重新启动计算机
shutdown -h now  # 立即关机，且关机后停机
shutdown -h 10   # 指定10分钟后关机

-c  # 取消目前已经进行中的关机动作
-k  # 并不会真的关机，只是将警告讯息传送给所有使用者
```

## `finger`

finger 命令可以让使用者查询一些其他使用者的资料。例如：Login Name; User Name; Home directory; ......

# 系统信息

## `lsb_release`

`lsb` 是 Linux Standard Base（Linux标准库）的缩写， **lsb_release命令** 用来与具体Linux发行版相关的Linux标准库信息。

注：LSB的译法有Linux标准库，Linux标准规范；CentOS最小化安装时默认没有这个命令，需要安装 `lsb_release` 使用命令。

```shell
lsb_release -a  # 显示全部信息，包括LSB、版本号、代号、版本描述信息
```

## `nvidia-smi`

命令用于查看nvidia显卡相关的信息：显存使用情况、显卡驱动版本、CUDA版本等等。

## `sensors`

`sensors` 命令可以用于查看系统CPU的温度，需要通过 `sudo apt install` 命令下载。然后，再通过 `watch -n 1 sensors` 来每间隔1秒监看CPU的温度状态。
