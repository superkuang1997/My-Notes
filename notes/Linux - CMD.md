# Linux🦄

## 参考文献

👉  [Linux 命令手册](http://linux.51yip.com/)



## 环境搭建

虚拟机镜像安装后事项：

```shell
# 如果无ifconfig命令则安装net-tools
apt install net-tools 

# 如果只有 sshd 说明只安装了 ssh-service
ps -e|grep ssh 
# 安装ssh-server
apt install openssh-server 
# 查看是否有ssh-agent进程
ps -e|grep ssh  

# 启动ssh-agent
/etc/init.d/ssh start  # way1
service ssh start      # way2
```



# Linux文件系统🦄

## 权限管理

### 权限范围

- u：User，所有者，默认情况创建者就是所有者，只能有一个

- g：Group，所属组，定义一组相同类型的用户，有使用权但没有所有权

- o：Other，以上两者之外的用户
- a：All，即全部的用户

<img src="http://store.secretcamp.cn/uPic/image-20210516093115044202105160931151621128675rvTLiirvTLii.png" alt="image-20210516093115044" style="zoom:50%;" />

```
文件类型及权限 -- 引用计数 -- 所有者 -- 所属组 -- 大小（字节） -- 最后一次修改时间 -- 文件名
```



### 文件类型

- 二进制文件：`-`
- 目录：`d`
- 软链接文件：`l`



### 权限代号

- r：读取权限，数字代号为 4；
- w：写入权限，数字代号为 2；
- x：执行或切换权限，数字代号为 1；
- -：不具任何权限，数字代号为 0；

```shell
-rw-------  二进制文件 所有者有读写权
-rw-r--r--  二进制文件 所有者有读写权，所属组和其他人有读权
-rwxrwxrwx  二进制文件 所有用户都有读写操作权
```





## 系统目录结构

![image-20220401153821636](http://store.secretcamp.cn/uPic/image-20220401153821636202204011538221648798702n76RaLn76RaL.png)





```
- Linux
/bin：Binary 的缩写，面向所有用户，存放着最经常使用的命令

/sbin：存放Super User（系统管理员）使用的系统管理命令（程序）

/usr：Unix System Resource ，可以理解为Windows下的 C:/Windows/ 目录

/etc：存放所有的系统管理所需要的配置文件和子目录。

/var : 就是在系统运作后才会渐渐占用硬盘容量的目录，比如一些日志、MySQL等软件的数据文件

/tmp ： 临时存放文件，重要的文件请不要放在这个目录里

/root : 超级管理员的家目录

/home ： 用户的家目录

/boot ： 引导程序文件存放目录（启动Linux时使用的一些核心文件）

/dev ： Device（设备）的缩写，设备文件目录

/lib ： Library，存放着系统最基本的动态连接共享库

/media ： 可移除媒体（如CD-ROM）的挂载点

/mnt ： 用于临时挂载设备的目录

/opt ： optional，额外安装软件的地方（可选应用软件包存放目录），可以理解为Windows下的 D：/Software

/proc ： 虚拟文件系统

/run ： 代替/var/run 目录

/srv ： 是一些服务启动之后，这些服务所需要取用的资料目录，比如www服务的网页文件可以放在这里

/sys ： Linux2.6内核的一个很大的变化，该目录下安装了2.6内核中新出现的一个文件系统sysfs
			 sysfs文件系统集成了下面3种文件系统的信息： 
			 1. 针对进程信息的 proc 文件系统
			 2. 针对设备的 devfs 文件系统
			 3. 针对伪终端的 devpts 文件系统。

/proc ： Processes，/proc 是一种伪文件系统（也即虚拟文件系统），存储的是当前内核运行状态的一系列特殊文件，这个目录是一个虚拟的目录，它是系统内存的映射，我们可以通过直接访问这个目录来获取系统信息。这个目录的内容不在硬盘上而是在内存里，我们也可以直接修改里面的某些文件
```



### /usr

```
/usr/bin：系统用户使用的应用程序
/usr/sbin：超级用户使用的比较高级的管理程序和系统守护程序。
/usr/src：内核源代码默认的放置目录
/usr/local：用户级的程序目录，可以理解为Windows下的 C:/Progrem Files/ 目录
```



### /etc

```
/etc/passwd           使用者帐号资讯，可以查看用户信息
/etc/shadow   			  使用者帐号资讯加密
/etc/group   				  群组资讯
/etc/default/useradd  定义资讯
/etc/login.defs       系统广义设定
/etc/skel             内含定义档的目录
/etc/issue            版本当前操作系统发行信息 
/etc/centos-release   版本当前操作系统发行信息
/etc/cpuinfo          cpu相关信息，包括型号、主频、内核信息等
```



### /proc

`/proc` 文件系统是一个虚拟的文件系统，不占用磁盘空间，它反映了当前操作系统在内存中的运行情况

```sh
# 系统版本
/proc/version  

# 平均负载
/proc/loadavg

# 系统内存情况
/proc/meminfo

# CPU利用率
/proc/stat

# 网络流量
/proc/net/dev
```



#### 平均负载

系统平均负载被定义为在特定时间间隔内运行队列中的平均进程树，uptime、w、top等命令都会有系统平均负载的输出。

如果一个进程满足以下条件则其就会位于运行队列中：

1. 它没有在等待 I/O 操作的结果
2. 它没有主动进入等待状态（调用wait)
3. 没有被停止(例如：等待终止)



以下是 uptime 命令的输出：

命令输出的最后内容表示在过去的 1、5、15 分钟内运行队列中的平均进程数量。

假设系统有两个CPU，那么在过去一分钟每个 CPU 的当前任务数为：8.13/2=4.065

一般小于 3 表示性能良好，大于 5 表示性能较差，所以以上的 4.065 在可接受范围内。

```
7:51pm up 2 days, 5:43, 2 users, load average: 8.13, 5.90, 4.94
```



Load Average 算法：

每隔5秒钟检查一次活跃的进程数



## 网络设置

lo: loop back 回环网卡 用于本机通信测试 

eth0：虚拟机网卡

桥接：通过真实网卡通信，可以与局域网内其他计算及通信

NAT模式:通过VMnet8虚拟网卡与主机通信，如果主机能访问互联网，那么虚拟机也可以。

Host-only模式：通过VMnet1虚拟网卡与主机通信，且仅能与主机通信。



## 设备文件

https://blog.csdn.net/mountzf/article/details/51735691

### 基本概念

- tty：tty一词源于 teletypes，指代终端

- pty：虚拟终端（pseudo-tty）

- pst：pseudo-terminal slave 是 pty 的实现方法，和 ptmx（pseudo-terminal master）配合使用实现 pty







# Linux命令🦄

## Mac OS 命令🐦 

### open

```

```



### launchctl

launchctl是 Mac OS 下的一个统一的服务管理框架，可以启动、停止和管理进程、应用程序、脚本等。

```markdown
~/Library/LaunchAgents         由用户自己定义的任务项
/Library/LaunchAgents          由管理员为用户定义的任务项
/Library/LaunchDaemons         由管理员定义的守护进程任务项
/System/Library/LaunchAgents   由 Mac OS 为用户定义的任务项
/System/Library/LaunchDaemons  由 Mac OS 定义的守护进程任务项
```



```shell
launchctl list  # 查看任务列表
```



### brew

👉 安装 homebrew

```shell
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```



👉  brew 文件目录

`brew install` 安装的软件，会下载到 `/usr/local/Cellar`

`brew cask install` 安装的软件，会下载到 `/usr/local/Caskroom`

安装后的软件中bin目录下的可执行文件，会以软链接的形式存放在 `/usr/local/bin`

安装后的软件会以硬链接的方式存放在 `/usr/local/opt` 





👉 brew search

```sh
# 搜索
brew search wget

# 被 // 包围则表示正则比导师
brew search /wge*/
```



👉 `brew autoremove`

卸载仅作为其他 formula 依赖的 formula ，且这些 formula 不被任何其他 formula 依赖。

```sh
brew autoremove
```





👉 `brew services`

Manage background services with macOS launchctl daemon manager.

If sudo is passed, operate on /Library/LaunchDaemons (started at boot).
Otherwise, operate on ~/Library/LaunchAgents (started at login).

```sh
# List all managed services for the current user (or root).
brew services [list]:
    
# Run the service formula without registering to launch at login (or boot).
brew services run (formula|--all):
    
# Start the service formula immediately and register it to launch at login (or boot).
brew services start (formula|--all):
    

# Stop the service formula immediately and unregister it from launching at login (or boot).
brew services stop (formula|--all):

# Stop (if necessary) and start the service formula immediately and register it to launch at login (or boot).
brew services restart (formula|--all):

```



👉 brew tap

如果你在核心仓库没有找到你需要的软件,那么你就需要安装第三方的仓库去安装你需要的软件

`tap` 命令的仓库源默认来至于`Github`，但是这个命令也不限制于这一个地方

```sh
# brew tap 可以为 brew 的软件的跟踪更新，安装添加更多的 tap formulae

# 没有参数会自动更新已经存在的 tap 并列出当前已经 tapped 的仓库
$ brew tag  

  homebrew/cask
  homebrew/core
  homebrew/services
  iina/mpv-iina
  pivotal/tap
```



👉 简单命令

```sh
brew list             # List all installed formulae and casks

brew update           # Fetch the newest version of Homebrew and all formulae from GitHub

brew help [COMMAND]   # Display help

brew home thefuck     # open a formula or cask's homepage in a browser

brew info thefuck     # Display brief statistics for your Homebrew installation

brew desc thefuck     # Display formula's name and one-line description

brew upgrade thefuck  # Upgrade outdated casks and formulae

brew doctor           # Check your system for potential problems

brew outdated   # List installed casks and formulae that have an updated version available

brew cleanup    # Remove stale lock files and outdated downloads for all formulae and casks, and remove old versions of installed formulae

brew update-reset    # Fetch and reset Homebrew and all tap repositories (or any specified repository) using `git` to their latest `origin/HEAD`
```



👉 常用参数

搭配 COMMAND

```
-d, --debug                      Display any debugging information.
-q, --quiet                      Make some output more quiet.
-v, --verbose                    Make some output more verbose.
-h, --help                       Show this message.
```

不搭配任何 COMMAND

```
--cache                   -- Display Homebrew's download cache
--caskroom                -- Display Homebrew's Caskroom path
--cellar                  -- Display Homebrew's Cellar path
--env                     -- Summarise Homebrew's build environment as a plain list
--prefix                  -- Display Homebrew's install path
--repository              -- Display where Homebrew's git repository is located
--version                 -- Print the version numbers of Homebrew, Homebrew/homebrew-core and Homebrew/homebrew-cask (if tapped) to standard outp
```



👉 镜像设置

https://mirror.tuna.tsinghua.edu.cn/help/homebrew/





## 处理命令🐦 

### ls

命令名称：ls

命令英文原意：list

功能描述：显示目录文件

```shell
ls [-ald] [文件或目录]
    -a # 显示所有文件，包括隐藏文件
    -l # 详细信息显示
    -d # 查看目录属性
    -h # human 人性化显示
    -i # 显示标识号
    -t # 用文件和目录的更改时间排序
```





![](http://store.secretcamp.cn/uPic/la202011222037271606048647URVTwSURVTwSimage/png.png)







### mkdir

英文原意：make directories

```shell
mkdir [目录]     // 创建目录
mkdir -p [目录]  // 递归创建目录，即使上级目录不存在也可以创建
```

### cd

英文原意：change director

功能描述：切换目录

```shell
cd [目录]
. 当前目录
.. 上一级目录
```

### pwd

英文原意：print working directory

功能描述：显示当前目录

```shell
pwd
/home/ubuntu
```

### rmdir

英文原意：remove empty directories

功能描述：删除空目录

```shell
rmdir [目录] // 删除一个空的目录
```

### rm

英文原意：remove

```shell
rm -rf [文件或目录]
    -r 删除目录
    -f 强制执行

rm -rf /*  // 立刻飞升
```

### mv

英文原意：move

```shell
mv [原文件或目录] [目标目录]
```

### cp

英文原意：copy

功能描述：复制文件或目录

```shell
cp -rp [原文件或目录] [目标目录]  
	-r 复制目录
	-p 保留文件属性  
```



### touch

功能描述：创建空文件

```shell
touch [文件名] 
```



### cat /tac

功能描述：显示文件内容

```shell
cat [文件名]  // 显示文件内容
	  -n 显示行号
cat -n /etc/profile
```



### head/tail

```
head [文件名]  // 显示文件前面几行
    -n 指定行数
    -f 动态显示文件末尾内容（tail）
```



### more/less

less 与 more 类似，但使用 less 可以随意浏览文件，而 more 仅能向前移动，却不能向后移动，而且 less 在查看之前不会加载整个文件。



### ln

英文原意：link

```zsh
ln [原文件] [目标文件]  # 生成硬链接
ln -s [原文件] [目标文件]  # 生成软链接
```

- 软链接类似于windows中快捷方式；

- 硬链接类似于拷贝，与原文件i节点相同（ls -i查看），能够同时更新，如果如果原文件丢失，硬链接依然可以访问。
- 硬链接不能跨分区，不能指向目录，软连接可以跨分区，可以指向目录。





### sort

排序命令

```sh
sort [选项] filename

	-f  # 忽略大小写
	-n  # 以数值型进行排序，默认使用字符串型排序
	-r  # 反向排序
	-t  # 指定分隔符，默认是分隔符是制表符
	-k n[,m]  # 按照指定的字段范围排序，从第n字段开始，m字段结束（默认到行尾）
```



### wc

统计命令

```sh
wc [选项] filename

	-l  # 只统计字数
	-w  # 只统计单词数
	-m  # 只统计字符数
```



### update-alternatives



## 文本处理命令🐦

### grep

英文原意：global search regular expression and print out the line

```shell
grep [选项] [指定字串][文件]  // 在文件中搜寻字串匹配的行并输出
      -i 不区分大小写 
      -v 反向选择，排除指定字串
      -r 递归查询目录下所有文件  --recursive
      -n 输出行号
      -w 匹配全词
      -m 限制返回数
      -c 统计匹配到的行数
    
grep -r PATH /etc/profile  # 在文件中搜索内容
```

> 参考文献：https://www.cnblogs.com/sparkdev/p/11294517.html



```sh
grep "search_pattern" path/to/file
grep "first_pattern" path/to/file | grep "second_pattern"
```





```
--after-context        -A           -- specify lines of trailing context
--basic-regexp         -G           -- use basic regular expression
--before-context       -B           -- specify lines of leading context
--binary-files                      -- specify type to assume for binary files
--byte-offset          -b           -- print the byte offset with output lines
--bz2decompress        -J           -- decompress bzip2'ed input before searching
--context              -C           -- specify lines of context
--count                -c           -- only print a count of matching lines
--decompress           -Z           -- decompress gzip'ed input before searching
--devices              -D           -- specify handling of devices, FIFOs and sockets
--directories          -d           -- specify handling of directories
--exclude                           -- skip files matching specified pattern
--exclude-dir                       -- skip directories matching specified pattern
--extended-regexp      -E           -- use extended regular expression
--file                 -f           -- specify pattern file
--files-with-matches   -l           -- output matching files' names only
--files-without-match  -L           -- output non-matching files' names only
--fixed-strings        -F           -- use literal strings
--help                              -- display help information
--ignore-case          -y       -i  -- case-insensitive
--include                           -- examine files matching specified pattern
--include-dir                       -- only search directories matching specified pattern
--invert-match         -v           -- select non-matching lines
--label                             -- provide filename to print for stdin
--line-buffered                     -- flush output on every line
--line-number          -n           -- prefix output with line numbers
--line-regexp          -x           -- force pattern to match only whole lines
--max-count            -m           -- stop after specified no of matches in each file
--no-filename          -h           -- suppress printing of filenames
--no-messages          -s           -- suppress messages about unreadable
--null                              -- print 0 byte after each filename
--only-matching        -o           -- show only matching part of line
--recursive            -R           -- recurse subdirectories
--regexp               -e           -- specify pattern
--silent               --quiet  -q  -- suppress normal output
--text                 -a           -- process binary file as if it were text
--version              -V           -- display version info
--with-filename        -H           -- print filename with each match
--word-regexp          -w           -- force pattern to match only whole words
-I                                  -- process binary files as if non-matching
-O                                  -- follow symlinks on the command line
-S                                  -- follow all symlinks
-p                                  -- do not follow symlinks
```





### wc

Desc：word, line, character, and byte count

```sh
# 统计文件的行数
wc -l file
```



## 搜索命令🐦

### locate

功能描述：在 mlocate 数据库中查找文件

```shell
locate [文件名]  // 在文件资料库中查找文件
	-i 不区分大小写
```







updatedb 命令可以手动更新mlocate 数据库

mac os 下要先创建locate数据库（/var/db/locate.database）

```shell
sudo launchctl load -w /System/Library/LaunchDaemons/com.apple.locate.plist
```

数据库经过一段时间后才会创建





### which

功能描述：在 PATH 变量指定的路径中，搜索某个系统命令的位置，并且返回第一个搜索结果。

which命令 可以看到某个系统命令是否存在，以及**执行的到底是哪一个位置的命令**。

```shell
which python  # python命令所在目录 
```



### where

```
where python
```



### whereis

功能描述：搜索**命令**所在目录及帮助文档路径，只能用于程序名的搜索

搜索二进制文件：-b

man说明文件参数：-m

源代码文件参数：-s

如果省略参数，则返回所有信息。

```shell
whereis [命令名称]  # 搜索命令所在目录及帮助文档路径
whereis ls
```



### find

功能描述：文件搜索

```shell
find [paths] [expression] [actions]

paths：所要搜索的目录及其所有子目录。默认为当前目录。
expression：所要搜索的文件的特征。
actions：对搜索结果进行特定的处理。
```



```sh
args:
	-name 选项可以根据文件名称进行检索，区分大小写
      ? 可以表示任意一个单一的符号
      * 可以表示任意数量（包括 0）的未知符号
      ! 反义匹配
	-iname 同 -name ，不区分大小写
	-type 根据类型搜索
      f 文件
      d 目录
      l 符号链接
  -ls 显示检索到的文件信息
	-empty 检索为空的文件或目录
	-user 检索归属于特定用户的文件或目录
	-size 根据文件大小检索
      用 + 和 - 表示大于或小于当前条件
      c -> 字节  k -> kb  M -> Mb  G —> Gb
  -maxdepth 限制遍历的层数
  -delete 可以用来删除搜索到的文件和目录
  -exec 可以对搜索到的结果执行特定的命令
  		{}  作为检索到的文件的占位符 
  		';' 作为命令结束的标志
  -mtime  修改时间（Modification time）：最后一次文件内容有过更改的时间点
  -atime  访问时间（Access time）：最后一次文件有被读取过的时间点
  -ctime  变更时间（Change time）：最后一次文件有被变更过的时间点
  		用 + 和 - 限制时间范围， 2 表示两天，-2 表示两天之内， +2 表示两天以外
```



```sh
find ./ -name 'steam*'  # 查询当前目录下所有以steam开头的文件
find ~ -type f -name '*.mp3' -exec cp {} /root/tmp ';'   # 将检索到的后缀为.mp3的文件拷贝到指定目录
```



### tree

以树状图列出目录的内容

```
-a 显示所有文件和目录。
-A 使用ASNI绘图字符显示树状图而非以ASCII字符组合。
-C 在文件和目录清单加上色彩，便于区分各种类型。
-d 显示目录名称而非内容。
-D 列出文件或目录的更改时间。
-f 在每个文件或目录之前，显示完整的相对路径名称。
-F 在执行文件，目录，Socket，符号连接，管道名称名称，各自加上"*","/","=","@","|"号。
-g 列出文件或目录的所属群组名称，没有对应的名称时，则显示群组识别码。
-i 不以阶梯状列出文件或目录名称。
-L level 限制目录显示层级。
-l 如遇到性质为符号连接的目录，直接列出该连接所指向的原始目录。
-n 不在文件和目录清单加上色彩。
-N 直接列出文件和目录名称，包括控制字符。
-p 列出权限标示。
-P<范本样式> 只显示符合范本样式的文件或目录名称。
-q 用"?"号取代控制字符，列出文件和目录名称。
-s 列出文件或目录大小。
-t 用文件和目录的更改时间排序。
-u 列出文件或目录的拥有者名称，没有对应的名称时，则显示用户识别码。
-x 将范围局限在现行的文件系统中，若指定目录下的某些子目录，其存放于另一个文件系统上，则将该子目录予以排除在寻找范围外。
```





## 压缩解压缩命令🐦

### gzip

英文原意：GNU zip

功能描述：压缩文件，仅可压缩文件且不保留原文

```shell
gzip [文件]  # 压缩后文件格式为.gz
```



### gunzip

英文原意：GNU unzip

功能描述：解压缩.gz的压缩文件

```shell
unzip test.gz
```



### tar

- -c 新建打包文件
- -t 查看打包文件的内容含有哪些文件名

- -v 显示详细信息

- -f filename 指定文件名，filename 为要处理的文件

- -z 通过 gzip 的支持进行压缩/解压缩

- -x 解打包或解压缩的功能，可以搭配-C（大写）指定解压的目录

> 注意 -c -t -x 不能同时出现在同一条命令中



```shell
# 压缩
tar [-zcf] [压缩后文件名] [目录] # 压缩后文件格式：.tar.gz

# 解压缩
tar -zxvf pokemon.tar.gz  # 不可以-zxfv
```






### zip

命令名称：zip

命令所在路径：usr/bin/zip

执行权限：所有用户

语法：zip选项[-r][压缩后文件名][文件或目录]

-r压缩目录

功能描述：压缩文件或目录（保留原文件）

压缩后文件格式：.zip

### unzip

命令名称：unzip

命令所在路径：usr/bin/unzi

执行权限：所有用户

语法：unzip [压缩文件]

功能描述：解压zip的压缩文件

### bzip2

命令名称：bzip2

命令所在路径：ausr/bin/bzip2

执行权限：所有用户

语法：bzip2选项[-k] [文件]

-k产生压缩文件后保留原文件

功能描述：压缩文件

压缩后文件格式：.bz2

范例：# bzip2 -k boduo （压缩成.bz2）

\# tar -jcf pokemon.tar. bz2 pokemon（压缩成.tar.bz2，z→j）

### bunzip2

命令名称：bunzip2

命令所在路径：usr/bin/bunzip2

执行权限：所有用户

语法：bunzip2选项[-k] [压缩文件]

-k 解压缩后保留原文件

功能描述：解压缩

范例：# bunzip2 -mz.bz2  （解压.bz2）

\# tar -jxf Japan.tar. bz2（解压.tar.bz2，z→j，c→x）





## 权限管理命令🐦

### chmod

英文原意：change the permissions mode of a file

参考：https://blog.csdn.net/qq_35246620/article/details/78624256

```sh
chmod [-cfRv][--help][--version][<权限范围>+/-/=<权限设置...>][文件或目录]
chmod [-cfRv][--help][--version][数字代号][文件或目录]
chmod [-cfRv][--help][--reference=<参考文件或目录>][--version][文件或目录]
```



参数说明：

- -c, --changes：效果类似-v参数，但仅返回更改的部分；

- -f, --quiet, --silent：不显示错误信息；

- -R, --recursive：递归处理，将指定目录下的所有文件及子目录一并处理；

- -v, --verbose：显示指令执行过程；

- --reference=<参考文件或目录>：把指定文件或目录的权限全部设成和参考文件或目录的权限相同；

- <权限范围>+<权限设置>：开启权限范围的文件或目录的该项权限设置；

- <权限范围>-<权限设置>：关闭权限范围的文件或目录的该项权限设置；

- <权限范围>=<权限设置>：指定权限范围的文件或目录的该项权限设置。



- `r`：读取权限，数字代号为`4`；
- `w`：写入权限，数字代号为`2`；
- `x`：执行或切换权限，数字代号为`1`；
- `-`：不具任何权限，数字代号为`0`；
- `s`：当文件被执行时，根据`who`参数指定的用户类型设置文件的`setuid`或者`setgid`权限。





示例：

```sh
chomd 777 file  # 添加所有用户的权限
chmod a+w file  # 对所有用户增加w权限
chmod 670 file  # 取消所有者的执行权限及赋予其他用户的所有权限
```



### usermod

用来修改用户帐号的各项设定





### chown

英文原意：change file ownership

命令所在路径：/bin/chown

执行权限：所有用户

语法：chown [用户] [文件或目录]

功能描述：改变文件或目录的所有者，只有所有者和root可以改变权限

```shell
chown -R yorua /usr/local/lib
			-R 处理指定目录以及其子目录下的所有文件
```





### chgrp

命令名称：chgrp命令

英文原意：change file group ownership

命令所在路径：/bin/chgrp

执行权限：所有用户

语法：chgrp [用户组] [文件或目求]

功能描述：改变文件或目录的所属组

```zsh
chgrp mygroup hello.txt  # 改变文件 hello.txt 的所属组为mygroup
```





### umask

命令名称：umask命令

英文原意：the user file-creation mask

命令所在路径：shell内置命令

执行权限：所有用户

语法：umask [-S]







## 用户管理命令🐦

### adduser & useradd

👉 adduser

功能描述：添加新用户，会自动为创建的用户指定主目录、系统 shell 版本，会在创建时输入用户密码。

```shell
adduser kiana
```





👉 useradd

功能描述：添加新用户，需要使用参数选项指定上述基本设置，如果不使用任何参数，则创建的用户无密码、无主目录、没有指定shell版本。

```shell
useradd kiana

args:
	-d  # 指定用户的主目录
	-m  # 不存在则创建主目录  与-d配合使用
	-s  # 指定用户登录时的shell版本
	-M  # 不创建主目录
```



### deluser & userdel

👉 userdel

```sh
userdel kiana

args:
	-r  # 连同用户主目录一同删除
```



### usermod

```shell
usermod -s /bin/bash kiana  # 为用户指定shell类型
```





### passwd

功能描述：设置用户密码

```shell
passwd kiana
```





### w

功能描述：查看登录用户详细信息

![image-20210321235702980](http://store.secretcamp.cn/uPic/image-20210321235702980202103212357031616342223V3ZGUfV3ZGUf.png)

```
系统当前时间  服务器连续运行时间  当前登陆的用户数量   系统负载（1min、5min、15min）
```

```
TTY    //  终端设备的统称  pts是伪终端或虚拟终端
LOGIN@ // 登陆时间
IDLE   // 闲置时间
PCPU   // 用户当前操作占用CPU的时间
JCPU   // 用户所有操作累计占用的CPU时间
WHAT   // 当前操作
```





### who

功能描述：查看登录用户信息

```shell
who
```



### whoami

功能描述：查看当前用户

```shell
whoami
```



### su

英文原意：switch user

- su root：切换了root身份，但shell仍然是普通用户的Shell，当前路径也不会改变
- su - root：用户和shell环境一起切换成root身份，当前路径切换到root的home目录

只有切换了shell环境才不会出现环境变量错误

```shell
su  # 默认切换到root
su username  # 切换用户 环境变量不会改变
su - username  # 切换用户 环境变量会改变
```



### sudo

英文原意：super-user do

功能描述：临时使用root权限运行命令

```shell
sudo passwd  # 设置root用户密码
```



### last

命令所在路径：/usr/bin/last

执行权限：所有用户

功能描述：列出目前与过去登入系统的用户信息

```zsh
last
```



### lastlog

命令所在路径：/usr/bin/lastlog

执行权限：所有用户语法：lastlog

功能描述：检查某特定用户上次登录的时间

```shell
lastlog -u 502 （502为用户uid）
```





## 帮助及描述命令🐦

### man

英文原意：manual

功能描述：获得帮助信息

范例：

```shell
man ls  # 查看ls命令的帮助信息
man -k <keyword> # 查询 keyword 相关的帮助文档
```



man手册的格式：

```
NAME         　　命令名称及功能简要说明

SYNOPSIS     　  用法说明，包括可用的选项

DESCRIPTION     命令功能的详细说明，可能包括每一个选项的意义

OVERVIEW

OPTIONS         说明每一项的意义

FILES           此命令相关的配置文件       

BUGS        

EXAMPLES        使用示例

SEE ALSO        另外参照
```



使用方法： 使用 man 之后按下 h 可以显示操作说明

```
m： 设置mark
'： 前往 mark
''： 前往之前的位置

向前翻一屏：b
向后翻一屏：space

向前翻一行：k
向后翻一行：Enter

/pattern     向后查找    n：下一个
?pattern     向前查找    N：前一个
&pattern     严格匹配
```



👉 多section查看

对于有多个 section 的 man 手册，会按照预先默认的搜索路径和顺序去搜索文档，可以指定 section 去搜索内容。

```
man 5 passwd
```



section 描述：

```
1	Executable programs or shell commands  shell命令
2	System calls (functions provided by the kernel)  系统调用函数
3	Library calls (functions within program libraries) 标准库函数
4	Special files (usually found in /dev) 特殊文件，通常是在/dev目录下的文件
5	File formats and conventions eg /etc/passwd  对一些文件进行解释
6	Games 游戏
7	Miscellaneous (including macro packages and conventions), e.g. man(7),groff(7) 宏命令包、协定等
8	System administration commands (usually only for root) 只有系统管理员可以使用的命令
9	Kernel routines [Non standard] 内核相关的文件

```



### help

bash 命令

功能描述：获得 Shell 内置命令的帮助信息

```shell
$ help cd  
```



### whatis

打印 manual 帮助文档中的 NAME

```sh
$ whatis mysql

>> mysql (1)            - the MySQL command-line client
```



### uname

uname - print system information

```sh
uname -a  # 打印所有系统信息
```



### lsb_release

```
lsb_release -a
```



## 网络命令🐦

### ufw

```shell
sudo apt-get install ufw  # 安装ufw
sudo ufw enable  # 开启防火墙
sudo ufw disable # 关闭防火墙
sudo ufw allow 53 # 允许外部访问53端口
sudo ufw delete allow 53 # 禁止外部访问53端口
sudo ufw allow from 192.168.0.100 # 允许此IP访问所有的本机端口
sudo ufw status # 查看防火墙状态
sudo ufw delete [allow smtp] 删除上面建立的某条规则
```

> 如果云服务器不仅要打开系统防火墙，也要在云服务器控制台打开外部防火墙。



### curl

```shell
curl [options...] <url>
	-A, --user-agent  # 指定 User-Agent
	-u, --user <user:password>  # 用户认证
	-H, --header <header/@file>  # 请求头
	-i  # 显示 http response 的头信息，连同网页代码一起
	-I  # 只显示 http response 的头信息
	-X, --request <METHOD>  # 指定 HTTP 请求的方法
	-G, --get  # 指定使用 GET 方法
	-b, --cookie <data>  # 指定 cookie
	-v  # 显示一次 http 通信的整个过程
	-s, --slient  # Don't show progress meter or error messages.
	-L, --location  # 允许自动跳转
	-f, --fail  # Fail silently (no output at all) on HTTP errors (H)
```



### ping

```shell 
ping [-c] ip_addr  # -c 指定发送次数
```



### ifconfig

英文原意：interface configure

功能描述：查看和设置网卡信息



### traceroute

功能描述：显示数据包到主机间的路径

```shell
traceroute www.baidu.com
```



### netstat

英文原意：show network status



```shell
netstat [选项]
		-r, --route      # Display the kernel routing tables.
		-g, --groups     # Display multicast group membership information for IPv4 and IPv6.
    -l, --listening  # Show only listening sockets
    -a, --all        # Show both listening and non-listening sockets
    -n, --numeric    # Show numerical addresses
    -p, --program    # Show the PID and name of the program to which each socket belongs.        
        
        
netstat -an|grep 3306
```



输出：

```
Proto：The protocol (tcp, udp, udpl, raw) used by the socket.
Recv-Q：
Send-Q：
Local Address：Address  and  port number of the local end of the socket.           
Foreign Address：Address and port number of the remote end of the socket         
State：The state of the socket.
```





## 进程管理命令🐦

### jps

jps（Java Virtual Machine Process Status Tool）是 JDK 1.5 提供的一个显示当前所有 java 进程 pid 的命令，简单实用，非常适合在 linux/unix 平台上简单查看当前 java 进程的一些简单情况。

显示当前系统的 java 进程情况，及其 id 号

```
jps
```



### ps

英文原意：Process Status

```shell
ps [参数] 
args:
	-A -e 列出所有进程
	 a  all with tty, including other users
	-a  all with tty, except session leaders
	-d  all except session leaders
	 r  仅显示正在运行的程序
	 T  显示当前终端运行的程序
	-u  以用户为主的格式来显示程序状况
	-x  显示所有程序，不以终端机来区分
	-f  全格式显示，和其他参数配合使用
```



```sh
ps -ef   # 查看每个进程，使用标准格式
ps aux   # 查看每个进程，使用BSD格式
ps -ejH  # 以树结构显示所有程序
ps -eLf  # 显示进程的线程相关信息
ps -U root -u root u  # 显示root的所有进程
```





### kill

```shell
kill [-Signal] pid
kill -9 pid  // 通知进程立刻结束
kill -15 pid  // 通知进程自行结束，更优雅的结束，也是默认方式
```



### pkill

pkill 是 ps 命令和 kill 命令的结合，用来杀死指定进程

杀死多个同名进程的时候可以使用 pkill

```shell
pkill [OPTIONS] <PATTERN>

		-F pidfile：严格匹配pid
		-t, --terminal：指定终端，无需/dev/前缀


pkill -kill -t pts/1  # 强制用户下线
```



### pgrep

pgrep 是通过程序的名字来查询进程的工具，一般是用来判断程序是否正在运行。

```shell
pgrep -l mongo
pgrep -u ubuntu -l
pgrep -f zookeeper

  -l, --list-name  列出程序名和进程ID；
  -u, --euid 列出用户所属进程，接受用户id和用户名作为参数
  -f, --full  搜索进程的范围包括命令行，而不仅仅指进程名
```





### lsof

list open files

查看进程打开的文件

```zsh
lsof -i:<port>  # 根据端口查找进程
lsof -t {{path/to/file}} # 输出当前文件进程id
```



### service

service - run a System V init script

```sh
service SCRIPT COMMAND [OPTIONS]
service --help | -h | --version
```

SCRIPT 指 System V init script，位于 `/etc/init.d/SCRIPT` ，



`service --status-all` 显示所有脚本的状态，`+` 表示进程正在进行， `-` 表示进程不在运行， `?` 表示没有 status 命令

```sh
service --status-all

[ + ]  mongodb
[ + ]  mysql
[ - ]  ntp
[ - ]  plymouth
[ + ]  ssh
[ + ]  sysstat
[ + ]  ufw
[ - ]  uuidd
```



### systemctl

systemctl - Control the systemd system and service manager

```shell
systemctl [OPTIONS...] COMMAND [NAME...]

```











### ulimit

本机下打开的文件描述符个数的个数不能超过 1024，除去输入、输出、epoll 的文件描述符, 所以程序传入的最大打开文件描述符的参数不能超过 1020 。

```sh
# 查看一个进程的最多打开个数
ulimit -n

# 设置临时设置打开个数，在普通用户下最多能够临时打开10001
limit -n10001
```





## 系统状态命令🐦

### top

display Linux processes

参考：https://blog.csdn.net/qq_30754685/article/details/86678037



👉 Linux

<img src="http://store.secretcamp.cn/uPic/image-20210519114420168202105191144201621395860DoFk3JDoFk3J.png" alt="image-20210519114420168" style="zoom: 43%;" />



| 参数    | 说明                                                         |
| ------- | ------------------------------------------------------------ |
| PID     | 进程号                                                       |
| USER    | 用户                                                         |
| PR      | 进程的优先级别                                               |
| NI      | 进程的优先级别数值                                           |
| VIRT    | 进程占用的虚拟内存值                                         |
| RES     | 进程占用的物理内存值                                         |
| SHR     | 进程使用的共享内存值                                         |
| S       | 进程的状态，<br/>S 表示休眠，<br/>R 表示正在运行，<br/>Z 表示僵死状态，<br/>N 表示该进程优先值是负数 |
| %CPU    | 该进程占用的CPU使用率                                        |
| %MEM    | 该进程占用的物理内存和总内存的百分比                         |
| TIME+   | 该进程启动后占用的总的CPU时间                                |
| COMMAND | 进程启动的启动命令名称                                       |



输入 top 命令之后直接按键盘，可以实现不同的效果

-  Global-defaults
  - q：Quit
  - ? | h  :Help
  - 0：Zero-Suppress toggle（ 压制 0 值）
  - A：Alternate-Display-Mode toggle
  - d | s：Change-Delay-Time-interval
  - E：Extend-Memory-Scale in Summary Area（修改内存单位显示）
  - e：Extend-Memory-Scale in Task Windows（修改内存单位显示）
  - k ：Kill-a-task

- SUMMARY AREA Commands
  - C：Show-scroll-coordinates toggle（显示坐标标尺）
  - l：Load-Average/Uptime toggle
  - m：Memory/Swap-Usage toggle
  - 1：Single/Separate-Cpu-States toggle
- TASK AREA Commands
  - J：Justify-Numeric-Columns toggle（数字列左对齐）
  - j：Justify-Character-Columns toggle（字符串列右对齐）
  - x：Column-Highlight toggle（高亮主列）
  - c：Command-Line/Program-Name toggle
  - f | F：Fields-Management
  - n | #：Set-Maximum-Tasks
  - M：按 %MEM 排列
  - P：按 %CPU 排列
  - T：按 TIME+ 排列







👉 Mac OS

<img src="http://store.secretcamp.cn/uPic/image-20210516093750163202105160937501621129070EyjZwjEyjZwj.png" alt="image-20210516093750163" style="zoom:50%;" />

输入 top 命令之后直接按键盘，可以实现不同的效果

- -o  [key]：排序
- -c  [mode]：切换模式





### df

查看磁盘空间 

<img src="http://store.secretcamp.cn/uPic/image-20210321234556243202103212345561616341556GOneKYGOneKY.png" alt="image-20210321234556243" style="zoom:67%;" />

```shell
df -h

  -h  # 人性化展示
  -i  # 查看 inode 使用情况
```



### free

查看内存空间

```sh
free -h

  -h # 人性化展示
```



### uptime

![image-20210321231737204](../../Library/Application%20Support/typora-user-images/image-20210321231737204.png)

```
当前时间  系统运行时间  当前登录用户个数  系统负载（任务队列的平均长度） 1min 5min 15min
```









## 关机重启命令🐦

### shutdown

功能描述：关机

```sh
Shut down the system.

     --help      Show this help
  -H --halt      Halt the machine
  -P --poweroff  Power-off the machine
  -r --reboot    Reboot the machine
  -h             Equivalent to --poweroff, overridden by --halt
  -k             Don't halt/power-off/reboot, just send warnings
     --no-wall   Don't send wall message before halt/power-off/reboot
  -c             Cancel a pending shutdown
```



### reboot

功能描述：重启

```shell
reboot
```





### logout

功能描述：注销

```sh
logout
```





## 下载命令🐦

### apt

apt（Advanced Packaging Tool）

```
A、下载的软件的存放位置：/var/cache/apt/archives
B、安装后软件的默认位置：/usr/share
C、可执行文件位置：/usr/bin
D、配置文件位置：/etc
E、lib文件位置：/usr/lib
```



👉 安装

```sh
# 刷新存储库索引
apt update  

# 安装软件包
apt install  
```



👉 卸载

```sh
# 移除与packagename相关联的所有二进制文件，但是不会移除与之相关联的配置文件或数据文件（configuration or data files）
# 同样也不会移除packagename所依赖的包
apt remove packagename

# 移除与包packagename相关联的所有文件
# 该命令不会移除packagename所依赖的包，也不会移除位于用户home目录中的与packagename相关联的配置文件或数据文件
apt purge  

# 卸载当前系统中的所有孤立的包
apt autoremove 

# 清除下载的安裝包
apt autoclean  

# 模拟卸载pacagename包，但是不会真的卸载
apt -s remove packagename
```





👉 展示

```sh
apt list --install # 列出所有已经安装的包
```







### wget

wget 是 Linux 中的一个下载文件的工具

```shell
$ apt-get install wget # 安装wget
```





### aptitude

aptitude 与 apt 一样，是 Debian 及其衍生系统中功能极其强大的包管理工具。

与 apt 不同的是，aptitude 在处理依赖问题上更佳，例如，aptitude 在删除一个包时，会同时删除本身所依赖的包。

```sh
$ aptitude remove packagename
$ aptitude purge packagename
```



### dpkg

dpkg（Debian package），为 Debian 操作系统 专门开发的套件管理系统，用于软件的安装，更新和移除。

所有源自"Debian" 的 Linux 的发行版都使用 dpkg，例如 Ubuntu

apt 基于dpkg，侧重于远程包的下载和依赖管理，相当于 dpkg 的前端。

1. dpkg是用来安装.deb文件，但不会解决模块的依赖关系，且不会关心 ubuntu 的软件仓库内的软件，可以用于安装本地的deb文件。

2. apt 会解决和安装模块的依赖问题，并会咨询软件仓库，但不会安装本地的 deb 文件，apt是建立在 dpkg 之上的软件管理工具。

dpkg 绕过 apt 包管理数据库对软件包进行操作，所以用 dpkg 安装过的软件包用 apt 可以再安装一遍，系统不知道之前安装过了，将会覆盖之前 dpkg 的安装，所以 ubuntu 最好不要用 dpkg

```sh
dpkg [option...] action

action：
		-i, --install package-file...
		-r, --remove package...
		
		
option：
```







## 终端命令🐦

### screen

```shell
screen -ls          # 列出当前所有的session
screen -S name      # 新建一个session
screen -r name      # 回到detached的session
screen -R name      # 回到detached的session，没有则创建

screen -d name      # 远程detach某个session
screen -D -r name   # 踢掉上一个用户回到session
screen -wipe 　# 检查目前所有的screen作业，并删除已经无法使用的screen作业


Ctrl + d + r  # detach， 暂时离开当前session
Ctrl + d + z  # 把当前session放到后台执行
```



### history

```sh
history [选项] [历史命令保存文件]
		-c：  清空历史命令 （不要轻易使用）
		-w： 把缓存中的历史命令写入历史命令保存文件  ~/.bash_history （正常情况下账号登出后才会保存）
```

历史命令默认保存1000条，可以在/etc/profile中更改： `HISTSIZE=1000`



## 远程命令🐦

### ssh

一般情况下，安装好的 Ubuntu 系统中默认是只安装了 openssh-client，此时只能通过此系统连接访问其他系统，但不具有让其他系统访问的权限。

在终端查看 ssh 进程，输入 `ps -e|grep ssh` ，如果有安装 openssh-client 只会出现 ssh -agent，不会出现 sshd


```sh
sudo apt install openssh-client # 本地主机运行此条，实际上通常已经默认安装
sudo apt install openssh-server # 服务器运行此条命令安装

# 启动ssh，两种方式都可
service ssh start
/etc/init.d/ssh start  
```



然后就可以用 ssh 连接目标主机了

```sh
ssh <username>@<IP address or domain name>
ssh -X <username>@<IP address or domain name> # 如果需要调用图形界面程序
```

以上是使用密码登录，还可以使用公钥登录





### scp

 scp（secure copy）是 linux 系统下基于 ssh 登陆进行安全的远程文件拷贝命令，scp 是加密的，rcp 是不加密的，scp 是 rcp 的加强版。

👉 本机上传到服务器

```sh
scp /Users/yorua/.ssh/id-ed25519-tencentcloud.pub ubuntu@124.223.214.212:/home/ubuntu/.ssh/authorized_keys
```



👉 服务器上传到本机

```
scp  ubuntu@124.223.214.212:/home/ubuntu/.ssh/authorized_keys /Users/yorua/.ssh/id-ed25519-tencentcloud.pub
```



👉  参数说明

```
-r 递归复制整个目录。
```



### sftp

sftp 是一个交互式文件传输程式，它类似于 ftp, 但它进行加密传输，比 ftp 有更高的安全性。

👉 安装

```shell
# 安装
sudo apt install vsftpd

# 启动sftp，两种方式都可
service ssh start
/etc/init.d/vsftpd  restart
```



👉 连接主机

```
sftp ubuntu@124.223.214.212
```



👉 处理

```sh
sftp> lpwd  # 本机工作目录
Local working directory: /Users/yorua

sftp> lcd opt
sftp> lpwd
Local working directory: /Users/yorua/opt
```



👉 上传与下载

```sh
sftp> get aaa.txt /Users/yorua/
Fetching /home/ubuntu/aaa.txt to /Users/yorua/aaa.txt

sftp> put /Users/yorua/bbb.txt  /home/ubuntu
Uploading /Users/yorua/bbb.txt to /home/ubuntu
```



## 其他命令🐦

### updatebd



### xrandr

```shell
/etc/init.d/networking restart  # 重启网络服务
xrandr --size 800x600   # 图形模式下修改屏幕分辨率
```





### source

source 指简单地读取脚本里面的语句依次在当前 shell 里面执行，通常用于重新执行刚修改的初始化文档

source 没有建立新的子 shell，脚本里面所有新建、改变变量的语句都会保存在当前 shell 里面。

```shell
source ~/.zshrc
. ~/.zshrc
```







## 键盘命令🐦

```
Ctrl + a  // 光标移动到命令行开头
ctrl + e  // 光标移动到命令行结尾
Ctrl + c  // 强制中断程序的执行
Ctrl + z  // 中断同时挂起任务，可以用fg命令恢复中断的任务
Ctrl + d  // 表示exit
Ctrl + l  // clear清屏
Ctrl + u  // 清除从光标到行首的全部内容
Ctrl + k  // 清除从光标到行尾的全部内容
```







# Vim🦄

## 插入命令

```
a：在光标所在字符后插入
A：在光标所在行尾插入

i：在光标所在字符前插入
I：在光标所在行行首插入

o：在光标下插入新行
O：在光标上插入新行
```



## 定位命令

行中光标的移动：

- `0`：移至行首
- `^`：移动到第一个非空字符
- `$`：移至行尾
- `H`：到屏幕的第一行
- `L`：到屏幕的最后一行

按屏幕移动：

- `ctrl - f`：向下翻一页  forward
- `ctrl - b`：向上翻一页 before
- `ctrl + d`：向下翻半页  down
- `ctrl + u`：向上翻半页  up
- `zz`： 让光标所在的行居屏幕中央
- `zt`： 让光标所在的行居屏幕最上一行 top
- `zb`： 让光标所在的行居屏幕最下一行 bottom

基于单词的移动：

- `w`：正向移动到下一单词的开头
- `b`：反向移动到当前单词/上一单词的开头

全局定位：

- `gg` 到第一行
- `G` 到最后一行
- `nG` 到第n行
- `:n`： 到第n行

行本身的移动：

- `Ctrl-Shift-Up`       向上移动当前行
- `Ctrl-Shift-Down`     向下移动当前行



## 选择命令

V：选择一行

v：开始选择，配合定位命令



## 删除命令

按字符删除

- `x`： 删除光标所在处字符
- `nx`： 删除光标所在处后n个字符

按行删除：

- `d0`：删除到本行开头

- `d$` or `D`：删除到本行末尾

- `dd`：删除光标所在行
- `ndd`：删除n行
- `：n1，n2d`：删除指定范围的行 例如：`:999,1006d

全文删除：

`dgg`：删除光标所在行到文件开头内容

`dG`：删除光标所在行到文件末尾内容





## 复制和剪切命令

vim 有 12 个粘贴板，分别是 0、1、2、3、...、9、a、“、＋，用 `:reg` 命令可以查看各个粘贴板里的内容。

```
yy   复制当前行
nyy  复制当前行以下n行
y0   复制至行首
y$ or y^   复制至行尾

dd 剪切当前行
ndd 剪切当前行以下n行

p/P 粘贴在当前光标所在行下或行上
```



## 替换和取消命令

```
r 取代光标所在处字符

R 从光标所在处开始替换字符，按ESC结束

u 取消上一步操作（undo）
```



## 搜索和搜索替换命令

```
/string 搜索指定字符串 搜索时忽略大小写:set ic 

n 搜索指定字符串的下一个出现位置
N 搜索指定字符串的上一个出现位置

:%s/old/new/g 全文替换指定字符串  用old替换new

:n1,n2s/old/new/g 在一定范围内替换指定字符串

/g表示不询问 /c表示询问
```



## 撤销命令

- `u`：撤销最后执行的命令
- `U`：修正之前对该行的操作
- `Ctrl+R`： Redo



## 保存和退出命令

```
:w 保存修改
:w  new_filename 另存为指定文件
:wq 保存修改并退出
ZZ 保存修改并退出
:q! 不保存修改退出
:wq! 保存修改并退出（对只读文件强行保存修改，仅文件所有者及root可使用）
```



## 自定义配置

vim 所有用户的配置

```
/etc/vimrc（CentOS）
/etc/vim/vimrc（Ubuntu）
```

单个用户的配置

```
~/.vimrc
```



```
set mouse=a  使用鼠标

:set nu    设置行号
:set nonu  取消行号

Ctrl + T   跳回上一层

r [!命令]   导入命令执行结果
```



```
:map ^P I#<ESC>  // 跳到行首并开启插入模式，插入#，按下ESC
:map ^B 0x  // 跳到行首，删去第一个字符
```

- 连续行注释：

```
:nl,n2s/^#//g   // 把行首的#替换为空
```

- 替换：

```
ab mymail 1120053798@qq.com  // 在脚本中将[a空格回车]替换为b
```



> 以上都是临时操作，永久保存修改配置文件，位置在宿主目录（/root 或/home/mz）下的.vimrc下（无则创建）





## 插件

vim 插件是一个特殊的文件夹，其主要的结构如下：

- autoload/：插件公共代码，vim 在**执行 viml 的时候**自动载入。`:h autoload`
- colors/：配色主题定义文件。`:h colorscheme`
- ftplugin/：专用代码，以**文件类型加下划线**开头，遇到对应文件自动执行。`:h write-filetype-plugin`
- plugin/：通用代码，自动执行。`:h write-plugin`
- syntax/：语法高亮定义文件。`:h mysyntaxfile`

一般的插件都有 `autoload` 和 `plugin` 两个目录，简单的插件可能只有一个 `plugin` 目录，支持多种语言的插件会有一个 `ftplugin` 目录。





# 日志管理🦄

## rsyslogd

```sh
# 查看进程是否存在
ps -aux | grep rsyslogd
```



## 常用日志文件

```
/var/log/cron：记录定时任务相关
/var/log/cups：记录打印信息的日志
/var/log/dmesg：记录系统在开机时内核自检的信息
/var/log/lastlog：记录系统中所有用户最后一次的登录时间的日志，这个文件是二进制文件，用 lastlog 命令查看
/var/log/maillog：记录邮件信息
/var/log/secure：记录验证和授权方面的信息，即ssh、su、sudo之类的信息
/var/log/wtmp：永久记录所有用户的登录、注销信息，二进制文件，用 last 命令查看
/var/log/btmp：记录错误登录的日志，二进制文件，用 lastb 命令查看
/var/log/utmp：记录当前已经登录的用户的信息，用 w、who命令查看
```



## 日志配置文件

日志记录默认格式：

```
日期时间 主机 服务名/程序名 具体信息
```



配置文件：`/etc/rsyslog.d/50-default.conf`

格式服从  `服务名称[连接符号] 日志等级    日志记录位置`

```
auth,authpriv.*                 /var/log/auth.log
*.*;auth,authpriv.none          -/var/log/syslog
cron.*                          /var/log/cron.log
#daemon.*                       -/var/log/daemon.log
kern.*                          -/var/log/kern.log
#lpr.*                          -/var/log/lpr.log
mail.*                          -/var/log/mail.log
#user.*                         -/var/log/user.log
```



### 连接符号

```
*：  全部记录
.：  只要比后面的等级高的（包含该等级）日志都记录，例如cron.info表示只要比info等级高的都记录
.=： 只记录所需等级的日志
.!： 不等于，也就是除了该等级的日志外，其他等级的日志都记录
```



### 日志等级

debug：调试信息

info：通知信息

notice：普通信息

warning：警告信息

err：错误信息

crit：临界信息

alert：警告信息

emerg：寄了



### logrotate





# 效率工具🦄

## neovim



## onmyzsh

官方网址：https://ohmyz.sh

github：https://github.com/ohmyzsh/ohmyzsh

👉 准备

```sh
# 安装zsh
sudo apt install zsh

# 修改默认shell
chsh -s /bin/zsh

# root配置（如果要改root用户）
sudo vim /etc/passwd
将 root 行的 /bin/bash 改成 /bin/zsh
```



👉 自动安装

```sh
# 安装onmyzsh
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```



👉手动安装

```sh
git clone https://github.com/robbyrussell/oh-my-zsh.git

cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc

source ~/.zshrc
```



onmyzsh 插件安装：

如果安装了 onmyzsh，那么已经内置了一些插件，在 `$ZSH_CUSTOM/plugins`，只要在 `.zshrc` 中配置就可以直接使用，另一些则需要安装插件本身再配置

需要安装插件本身的：

- [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)：不补全

- [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting)：高亮
- [autojump](https://github.com/wting/autojump#installation)
- [thefuck](https://github.com/nvbn/thefuck)



可以直接配置使用：

- ufw：补全
- colored-man-pages：彩色man手册
- extract：新增一个无脑解压缩命令





## thefuck

github：https://github.com/nvbn/thefuck



```sh
Mac os：

brew install thefuck

Linux：

sudo apt update
sudo apt install python3-dev python3-pip python3-setuptools
sudo pip3 install thefuck --user
```



```sh
# 编辑bashrc配置文件
vim ~/.bashrc

# 在文件尾加入一行给thefuck取别名fuck
eval "$(thefuck --alias fuck)"

# 使生效
source ~/.bashrc
```



## autojump

github：https://github.com/wting/autojump



`man autojump` 可以查看 autojump 的使用说明

为了使得 autojump 在 shell 中永久有效，你需要运行下面的命令。

```sh
echo '. /usr/share/autojump/autojump.sh' >> ~/.bashrc
```



## tldr

tldr 全称 too long, don’t read.

```sh
pip3 install tldr
```



## spacevim

官方主页：https://spacevim.org/cn/

github：https://github.com/liuchengxu/space-vim

查看 spacevim 的安装指南

```sh
curl -sLf https://spacevim.org/cn/install.sh | bash -s -- -h
```



前缀键：SPACE（SPC）

用户配置文件：`~/.SpaceVim.d/init.toml`

插件位置：`~/.cache/vimfiles/repos/github.com`





## cowsay

显示一个可爱的小动物

```sh
sudo apt-get update
sudo apt-get install cowsay
```

参考：https://blog.csdn.net/qq_31524409/article/details/51167068





## cron

cron 用于设置周期性被执行的指令

crontab 是 UNIX 系统下定期执行任务的触发器。用户把要定期执行的任务记录在这个文件下面，然后crond定期的去检查这个定期执行列表，有要执行的工作时便自动执行。

默认情况下只有 root 用户才能使用 cron，其他用户需在 `/etc/cron.allow` 以及 `/etc/cron.deny` 中配置。

```
usage:	crontab [-u user] file
	crontab [ -u user ] [ -i ] { -e | -l | -r }
		(default operation is replace, per 1003.2)
	-e	(edit user's crontab)
	-l	(list user's crontab)
	-r	(delete user's crontab)
	-i	(prompt before deleting user's crontab)
```



👉 基本用法

```
crontab -e  # 编辑定时任务
```



👉 cron 表达式

[表达式生成](https://www.bejson.com/othertools/cron/)

```
*  *  * *  *   command
分 时 天 月 周   命令
```



👉 开启 cron 日志

1. 打开rsy

```
sudo vim /etc/rsyslog.d/50-default.conf
```

2. 取消 `cron.*  /var/log/cron.log` 的注释
3. 重启 rsyslog

```
sudo service rsyslog  restart
```

4. 查看日志

```
# cron日志
less /var/log/cron.log

# 系统日志
tail /var/log/syslog
```



👉 cron 的输出

cron 会通过邮件输出信息





👉 注意事项

1. crontab 由于 `.bashrc` 不会被执行，所以很多环境变量都没有，尽量用绝对路径

2. crontab 的默认 SHELL 是 sh，一些命令例如 source 没有，可以指定 SHELL：

   ```sh
   SHELL=/bin/bash
   */10 * * * * source /path/to/virtualenv/bin/activate && /path/to/build/manage.py some_command > /dev/null
   ```

   

 

## mail

安装后可以通过 mail 命令管理邮件

```
sudo apt install mailutils
```

输入 mail 不跟任何参数，然后输入 help 查看解释。

```sh
mail  # 默认信箱
```



```sh
mail -f /home/ubuntu/mbox # 指定信箱
```



## postfix

```sh
# 安装postfix
sudo apt install postfix

# 重新配置postfix
sudo dpkg-reconfigure postfix
```



## pigchaproxy

[linux使用教程](http://101.34.95.10:8081/linux_tutorial)

[官方地址](https://pigpigchacha.github.io/officialsite)

```sh
nohup pigchaproxy &

export http_proxy=http://127.0.0.1:15732 && export https_proxy=http://127.0.0.1:15732
```



# 其他🦄

## 修改DNS

临时修改。（重启后失效）

```sh
vim /etc/resolv.conf   # 打开resolv.conf 文件
nameserver 8.8.8.8     # 修改DNS
```

 

永久修改。（永久修改不失效）

```sh
vim /etc/resolvconf/resolv.conf.d/base //打开resolv.conf 文件

nameserver 8.8.8.8    #修改DNS
```





## 用户获取sudo权限

https://www.cnblogs.com/iken/articles/4760705.html



## 允许用户使用root登录实例

1. 修改 root 密码

```
sudo passwd root
```



2. 修改配置文件

```
sudo vi /etc/ssh/sshd_config 
```

 找到 `#Authentication`，将 `PermitRootLogin` 参数修改为 `yes` 并保存



3. 重启 ssh 服务

```
sudo service ssh restart
```



## 管理多个ssh

1. 生成公钥和私钥

```sh
ssh-keygen -t ed25519 -C "1120053798@qq.com" -f  ~/.ssh/id-ed25519-tencentcloud
ssh-keygen -t ed25519 -C "1120053798@qq.com" -f  ~/.ssh/id-ed25519-github
```



2. 配置config文件

```
Host *
  AddKeysToAgent yes
  IdentityFile ~/.ssh/id-ed25519-tencentcloud

 
Host *
  AddKeysToAgent yes
  IdentityFile ~/.ssh/id-ed25519-github
```



3. 将 ssh 密钥添加到 ssh-agent

```sh
# 启动ssh-agent
eval "$(ssh-agent -s)"

# 将ssh私钥添加到ssh-agent并将密码存储在密钥链中。 
ssh-add -K ~/.ssh/id-ed25519-tencentcloud
ssh-add -K ~/.ssh/id-ed25519-github
```





## 使用ssh公钥登录服务器

```sh
# 登录远程服务器
ssh ubuntu@124.223.214.212

# 在服务器上创建.ssh文件夹,如果已经存在就跳过此步
mkdir .ssh 

# 为了保证.ssh文件夹的安全，应取消其他用户对文件夹的所有权限
chmod 700 .ssh

# 退出登录
exit

# 本地主机的公钥复制到远程服务器,作为已认证密钥
scp /Users/yorua/.ssh/id-ed25519-tencentcloud.pub ubuntu@124.223.214.212:/home/ubuntu/.ssh/authorized_keys
```



## 阿里云 ESC git clone 慢

https://blog.csdn.net/zwkkkk1/article/details/94476963
