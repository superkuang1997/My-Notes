# Shell编程🦄

## shell概述

Shell 是一个命令行解释器，它为用户提供了一个向 Linux 内核发送请求以便运行程序的界面系统级程序，用户可以用 Shell 启动、挂起、停止甚至是编写一些程序。

Shell 是一个功能相当强大的编程语言，易编写，易调试，灵活性较强。Shell 是解释执行的脚本语言，在 Shell 中可以直接调用 Linux 系统命令，Linux 可执行的 Shell 可以根据 `/etc/shells` 进行查询。



## Shell脚本的执行方式

1. 通过 bash 命令来执行相应的脚本，如果安装了其他 shell，也可以用于执行

   通过该方式执行，即使脚本没有执行权限，也能够执行成功。

2. 通过相对路径或者绝对路径来执行脚本

   该脚本必须有执行的权限，才能够进行执行，如果没有执行权限则不能执

   ```sh
   ./test.sh
   ```

   由于在Linux中所有可执行的命令是在Linux中的PATH变量中配置的，该脚本执行的路径没有在 PATH 中配置，所以只执行脚本名，是不能够找到该脚本命令的，所以需要在执行脚本命令前加上 `./`



# Bash的基本功能🦄



## 通配符

```sh
?：匹配一个任意字符

*：匹配 0 个或任意多个任意字符，也就是可以匹配任何内容

[]：匹配中括号中任意一个字符。例如：[abc]代表一定匹配一个字符，或者是a，或者是b，或者是c。

[-]：匹配中括号中任意一个字符，-代表一个范围。例如：[a-z] 代表匹配一个小写字母

[^]：逻辑非，表示匹配不是中括号内的一个字符。例如：[0-9] 代表匹配一个不是数字的字符。
```



## 逻辑运算符

### ;符号

；表示多个命令顺序执行，命令之间没有任何逻辑联系，即使有命令错误也不影响后面的命令执行。

```sh
ls; pwd ; cd; /user; date 
```



### &符号

&：表示任务在后台执行，立刻返回到提示符状态

进程在 Shell 一直打开的情况下是没有问题的，如果关了 Shell 窗口甚至退出 ssh ，那么进程自动就结束了。

如果想退出窗口乃至退出登录仍然保持程序运行，再加上 `nohup`

最后的 `&` 表示让这个进程到后台去执行

```sh
nohup java -jar /usr/sbin/lb-agent.jar >>/var/log/lb-agent.log  &
```



### &&符号

&& ：表示前一条命令执行成功时，才执行后一条命令

```sh
ls ~ && echo "yes"
```



### |符号

|：表示管道，上一条命令的输出，作为下一条命令的输入

```sh
# 将 ps -e 的结果作为 grep mysql 的输入
ps -e | grep mysql
```



### ||符号

||：表示上一条命令执行失败后，才执行下一条命令

```sh
ls nowhere || echo "no"
> no

ls ~ && echo yes || echo "no"
> yes

ls nowhere && echo yes || echo "no"
> no
```



## 特殊符号

### ''单引号

在单引号中所有的特殊符号，如 "$" 和 "`"（反引号）都没有特殊含义。



### ""双引号

在双引号中特殊符号都没有特殊含义，但是 `$` 、反引号和 `\` 是例外，拥有“调用变量的值”、“引用命令”和“转义符”的特殊含义



### ``反引号

反引号括起来的内容是系统命令，在 Bash 中会先执行，然后将结果赋给变量。

它和作用和 `$()` 一样，不过推荐使用 `$()` ，因为反引号非常容易看错。

```sh
abc=`date`
echo $abc
> Tue 02 Aug 2022 03:59:35 PM CST
```



### $()符号

`$() ` 与反引号作用一样，用来引用系统命令。

```sh
abc=$(date)
echo $abc
> Tue 02 Aug 2022 03:59:35 PM CST
```



### $符号

`$` 用于调用变量的值，如需要调用变量name的值时，需要用 `$name` 的方式得到变量的值



### \转义符

跟在转义符之后的特殊符号将失去特殊含义，变为普通字符。如\$将输出“$”符号，而不当做是变量引用



### #符号

在 Shell 脚本中，# 开头的行代表注释



### #!释伴

`#!` 的名称叫做 Shebang，中文解释为“释伴”，即 “解释伴随行” 的简称。

`#!` 通常出现在类 Unix 系统的脚本中第一行，作为前两个字符。在 #! 之后，可以有一个或数个空白字符，后接解释器的绝对路径，用于指明执行这个脚本文件的解释器。

如果脚本文件中没有 `#!` 这一行，那么执行时会默认采用当前 Shell 去解释这个脚本，即 `$SHELL`

`#!`  的好处在于，允许脚本和数据文件充当系统命令，无需在调用时由用户指定解释器，从而对用户和其它程序隐藏其实现细节。



注意：

 `#!` 之后的解释程序必须写绝对路径，因为它是不会自动到 `$PATH` 中寻找解释器的



### 双括号

双括号命令允许你在比较过程中使用高级数学表达式。test命令只能在比较中使用简单的算术操作。双括号命令提供了更多的数学符号，这些符号对于用过其他编程语言的程序员而言并不陌生。双括号命令的格式如下：





# 变量🦄

变量是计算机内存的单元，其中存放的值可以改变。当 Shell 脚本需要保存一些信息时，如一个文件名或是一个数字，就把它存放在一个变量中。每个变量有一个名字，所以很容易引用它。使用变量可以保存有用信息，使系统获知用户相关设置，变量也可以用于保存暂时信息。

Bash中变量的默认类型为字符串型，如果要进行数值运算，则必须制定变量类型为数值型。



## alias命令别名

写入环境变量中的别名可以永久生效

```shell
alias # 查询命令别名
alias[别名]=['原命令']    # 设定命令别名
unalias [别名] # 删除别名
```

命令执行时顺序

- 第一顺位执行用绝对路径或相对路径执行的命令。

- 第二顺位执行别名。

- 第三顺位执行Bash的内部命令（例如cd）

- 第四顺位执行按照$PATH环境变量定义的目录查找顺序找到的第一个命令。



## 本地变量

定义变量

```shell
name=kiana
```

调用变量

```shell
echo $name  # kiana
```

叠加变量

```shell
name="$name"kiana
echo $name  # kianakiana
```

查看变量

```shell
set
```

删除变量

```shell
unset name
```



## 环境变量

用户自定义变量只在当前的Shell中生效，而环境变量会在当前Shell和这个Shell的所有子Shell当中生效。如果把环境变量写入相碰的配置文件，那么这个环境变量就会在所有的Shell中生效

声明环境变量

```shell
export 变量名=变量值
```

查询环境变量

```
env
```

删除环境变量

```shell
unset 变量名
```

PATH变量叠加

```shell
PATH = "$PATH":/root  # 将/root写入PATH 临时生效
```



## 位置参数变量

位置参数变量实际上是预定义变量的一种

```
$n：n为数字，$0代表命令本身，$1-$9代表第一到第九个参数

$*：这个变量代表命令行中所有的参数，$*把所有的参数看成一个整体

$@：这个变量也代表命令行中所有的参数，不过$@把每个参数区分对待

$#：这个变量代表命令行中所有参数的个数
```



## 预定义变量

```
$?：最后一次执行的命令的返回状态。如果这个变量的值为0，证明上一个命令正确执行；
    如果这个变量的值为非0（具体是哪个数，由命令自己来决定），则证明上一个命令执行不正确了

$$：当前进程的进程号（PID）

$!：后台运行的最后一个进程的进程号（PID）
```





# 数值运算🦄

## declear

```
declare [+/-][选项] 变量名

```



## expr/let



## 运算式

```
$(())
$[]
```



## 运算符

<img src="http://store.secretcamp.cn/uPic/image-20220401104913604202204011049131648781353zXjSe5zXjSe5.png" alt="image-20220401104913604" style="zoom:40%;" />





# 环境变量配置🦄

环境变量配置文件中主要是定义对系统的操作环境生效的系统默认环境变量，比如 PATH、HISTSIZE、PSI、HOSTNAME 等默认环境变量。





## 环境变量配置文件

系统中主要有以下五类环境变量配置文件

```shell
# 对系统中所有用户生效
/etc/profile
/etc/profile.d/*.sh  # 即/etc/profile.d目录下所有以.sh结尾的文件
/etc/bashrc

# 对当前用户生效
~/.bash_profile
~/.bashrc
```

`/etc/profile` 定义了登录（login shell）情况下的环境变量配置

`/etc/bashrc` 定义了非登录（nologin shell）情况下的环境变量配置



### 登录Shell

#### Login Shell

Login Shell 就是进入 Shell 时是需要输用户名密码的，例如启动图形界面最后一步需要你输入用户名密码，只有输入正确，才能进入桌面，进入的就是一个 Login Shell 。

读取配置文件的顺序：`/etc/profile -> /etc/bashrc -> ~/.bashrc -> ~/.bash_profile`

#### Nologin Shell

Nologin Shell 就是不需要输入密码就能进入的 Shell 。

读取配置文件的顺序：`/etc/profile -> /etc/bashrc -> ~/.bashrc`



## 配置文件的优先级

[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-D8rA1S5z-1659174111115)(http://store.secretcamp.cn/uPic/env2021032108532316162880031tuVEK1tuVEK.png)]

## 其他环境变量

注销时生效的环境变量

```
~/.bash_logout
```

历史命令保存文件

```
~/.bash_history
```



## Shell登录信息

👉 本地终端欢迎信息

```shell
vim /etc/issue
```

| 转义符 | 作用                             |
| ------ | -------------------------------- |
| \d     | 显示当前系统日期                 |
| \s     | 显示操作系统名称                 |
| \l     | 显示登录的终端号，这个比较常用。 |
| \m     | 显示硬件体系结构，如i386、i686等 |
| \n     | 显示主机名                       |
| \o     | 显示域名                         |
| \r     | 显示内核版本                     |
| \t     | 显示当前系统时间                 |
| \u     | 显示当前登录用户的序列号         |

👉 远程终端欢迎信息

```shell
vim /etc/issue.net
```

- 转义符在 `/etc/issue.net` 文件中不能使用
- 是否显示此欢迎信息，由 ssh 的配置文件 `/etc/ssh/sshd_config` 决定，加入 `Banner /etc/issue.net` 才能显示，并重启SSH服务



👉 登录后欢迎信息

```shell
vim /etc/motd
```

不管是本地登录，还是远程登录，都可以显示此欢迎信息







# 输入/输出重定向🦄

## 标准输入/输出/错误

一般情况下，每个 Unix/Linux 命令运行时都会打开三个文件：

- 标准输入文件（stdin）：stdin的文件描述符为 0，Unix/Linux 程序默认从 stdin 读取数据，标准输入就是命令的输入，默认指向键盘。
- 标准输出文件（stdout）：stdout 的文件描述符为 1，Unix/Linux 程序默认向 stdout 输出数据，标准输出就是命令的输出，默认指向终端屏幕。
- 标准错误文件（stderr）：stderr的文件描述符为 2，Unix/Linux 程序会向 stderr 流中写入错误信息，标准错误是命令错误信息的输出，默认指向屏幕。

linux 中用 0 代表标准输入，1 代表标准正确输出，2 代表标准错误输出。

```
键盘   /dev/stdin  0  标准输入
显示器 /dev/sdtout 1  标准输出
显示器 /dev/sdterr 2  标准错误输出
```



## 重定向

重定向是Linux中的一项功能，因此在执行命令时，可以更改标准的输入/输出设备。



## 标准输入重定向

```sh
# 将文件作为命令的输入，如：mail -s "mymail" helloworld@163.com < mailfile
命令 < 文件
```



## 标准输出重定向

符号 > 默认情况下相当于 1>

```sh
命令 > 文件   # 以覆盖的方式把命令的正确输出输出到指定的文件或设备当中
命令 >> 文件  # 以追加的方式把命令的正确输出输出到指定的文件或设备当中
```



## 标准错误输出重定向

注意，错误输出重定向符 > 或 >> 的后边不能有空格！

```sh
错误命令 2>文件   # 以覆盖的方式，把命令的错误输出输出到指定的文件或设备当中。
错误命令 2>>文件  # 以追加的方式，把命令的错误输出输出到指定的文件或设备当中。文件
```



```shell
echo 'export PATH="/usr/local/opt/redis@3.2/bin:$PATH"' >> ~/.zshrc   # 通过输出重定向写入环境变量
```



## 同时重定向

`2>&1` ：表示把「标准错误」重定向为和「标准输出」一样

`1>&2` ：表示把「标准输出」重定向为和「标准错误」一样。

```sh
命令 > 文件 2>&1   # 以覆盖的方式，把正确输出和错误输出都保存到同一个文件当中。
命令 &>> 文件 2>&1  # 以追加的方式，把正确输出和错误输出都保存到同一个文件当中。

命令 &> 文件   # 以覆盖的方式，把正确输出和错误输出都保存到同一个文件当中。
命令 >> 文件 2>&1 # 以追加的方式，把正确输出和错误输出都保存到同一个文件当中。
命令 >>文件one 2>>文件two  # 把正确的输出追加到文件1中，把错误的输出追加到文件2中
```



## 丢弃信息

```
命令 > 文件 2>/dev/null
命令 > /dev/null
```



# shell命令🦄

## echo

```sh
-e # 支持反斜线控制的字符转换

\\ # 反斜杠本身
\a # 输出告警音
\b # 退格键，即删除键
\e # escape键
\f # 换页符
\n # 换行符
\r # 回车
\t # 制表符，即tab
\v # 垂直制表符
```





## sh

Bourne shell，基本用法同 bash

```shell
sh                     # 开启交互式shell
sh /path/to/script.sh    # 运行脚本
sh -c                 # 执行命令，然后退出
sh -s                  # 从stdin读取并执行命令
```



## bash

Bourne-Again SHell

```sh
bash                       # 开启交互式shell
bash /path/to/script.sh    # 运行脚本
bash -c "command"          # 执行命令，然后退出
bash -x path/to/script.sh  # 执行脚本，在执行之前打印每个命令
bash -e path/to/script.sh  # 执行脚本中的命令，在出现第一个错误时停止
bash -s                    # 从stdin读取并执行命令
```





## nohup

nohup 英文全称 no hang up（不挂起），用于在系统后台不挂断地运行命令，退出终端不会影响程序的运行。

```sh
nohup Command [ Arg … ] [　& ]
```

nohup 命令，在默认情况下（非重定向时），会输出一个名叫 nohup.out 的文件到当前目录下，如果当前目录的 nohup.out 文件不可写，输出重定向到 `$HOME/nohup.out` 文件中。

```sh
nohup pigchaproxy > $HOME/logs/pigchaproxy.log 2>&1 &
```

如果要停止运行，你需要使用查找到 nohup 运行脚本的 PID，然后使用 kill 命令来删除。



## export

设置环境变量

```

```



## xargs

unix 命令都带有参数，部分命令可以接受 stdin 作为参数，例如下方 grep 命令就可以接受 stdin 作为参数

```
cat /etc/passed | grep root
```

所以代码就等同于

```
grep root /etc/passwd
```

但是大多数命令不接受 stdin 作为参数，只能在命令行输入参数，这就导致无法用管道去传递参数，例如echo 命令就不支持管道传递参数

```sh
$ echo "hello world" | echo
  -bash: echo: write error: Broken pipe
```

xargs（extended arguments） 命令的作用就是将管道或标准输入（stdin）转为命令行参数

```sh
 xargs [options] [command [initial-arguments]]
```

用例：

```sh
$ echo "hello world" | xargs echo
  hello world
```



👉 分隔符

默认情况下，xargs 将空格和换行符作为分隔符，把标准输入分解成一个个命令行参数，以下就将标准输入分解成了三个参数 a b c

```sh
echo "a b c" | xargs mkdir
```

使用 -d 参数可以修改分隔符

```sh
echo "a\tb\tc" | xargs -d "\t" mkdir
```



# 条件判断🦄

## 判断格式

### test命令

test 命令是 Shell 环境中测试条件表达式的实用工具。

```sh
# 方法一
test -e /root/install.log
test -n $(find /var/log/crashes -name "app-*.log"）
```



### 单中括号

左中括号 [ 是调用 test 的命令标识，右中括号是关闭条件判断

```sh
# 方法二：中括号，注意前后必须有空格
[ -e /root/install.log ]

[ -e /root/install.log ] && echo 'yes' || echo 'no'
```



### 双中括号

双中括号







## 文件类型判断

```sh
-b <文件>：如果文件为一个块特殊文件，则为真；
-c <文件>：如果文件为一个字符特殊文件，则为真；
-d <文件>：如果文件为一个目录，则为真；
-e <文件>：如果文件存在，则为真；
-f <文件>：如果文件为一个普通文件，则为真；
-g <文件>：如果设置了文件的SGID位，则为真；
-G <文件>：如果文件存在且归该组所有，则为真；
-k <文件>：如果设置了文件的粘着位，则为真；
-O <文件>：如果文件存在并且归该用户所有，则为真；
-p <文件>：如果文件为一个命名管道，则为真；
-r <文件>：如果文件可读，则为真；
-s <文件>：如果文件的长度不为零，则为真；
-S <文件>：如果文件为一个套接字特殊文件，则为真；
-u <文件>：如果设置了文件的SUID位，则为真；
-w <文件>：如果文件可写，则为真；
-x <文件>：如果文件可执行，则为真。
-n <String>：如果字符串非空，则为真
```



## 文件比较

```sh
file1 -nt file2  # 判断文件1的修改时间是否比文件2的新，如果新则为真
file1 -ot file2  # 判断文件1的修改时间是否比文件2的旧，如果旧则为真
file1 -ef file2  # 判断文件1是否和文件2的Inode号一致，可以理解为两个文件是否为同一个文件，判断用于判断硬链接是很好的方法
```



## 整数比较

```sh
整数1 -eq 整数2 # 判断整数 1 是否和整数 2 相等（相等为真）
整数1 -ne 整数2 # 判断整数 1 是否和整数 2 不相等（不相等位置）
整数1 -gt 整数2 # 判断整数 1 是否大开整数 2（大于为真）
整数1 -lt 整数2 # 判断整数 1 是否小于整数 2（小于位置）
整数1 -ge 整数2 # 判断整数 1 是否大于等于整数 2（大于等于为真）
整数1 -le 整数2 # 判断整数 1 是否小于等于整数 2（小于等于为真）
```



## 字符串判断

```sh
-z 字符串    # 判断字符串是否为空（为空返回真）
-n 字符串    # 判断字符串是否为非空（非空返回真）
字符串1 == 字符串2  # 判断字符串1是否和字符串2相等（相等返回真）
字符串1 != 字符串2  # 判断字符串1是否和字符串2不相等（不相等返回真）
```



## 多重条件判断

```sh
if1 -a if2 # 逻辑与，判断 1 和判断 2 都成立，最终的结果才为真
if1 -o if2 # 逻辑与，判断 1 和判断 2 都成立，最终的结果才为真
!if         # 逻辑非，使原始的判断式取反
```

示例：

```sh
$ aa=11
$ [ -n "$aa" -a "$aa" -gt 23 ] && echo "yes" echo "no"
> no
```



# 控制流🦄

## if

```sh
[ -e /ununtu ] && echo yes || echo no
```



👉 单分支

```sh
# 形式一：
if [ 条件判断式 ]; then
		command
fi

# 形式二：
if [ 条件判断式 ]
	 then
	 		 command
fi
```





👉 双分支

```sh
if [ 条件判断式 ] 
		then
			条件成立时，执行的程序 
		else
			条件不成立时，执行的另一个程序 
fi
```



👉 多分支

```sh
if [ 条件判断式1 ] 
		then
			条件成立1时，执行的程序 
elif [ 条件判断式2 ] 
		then
			条件成立1时，执行的程序 
else
		条件均不成立时，执行的程序 
fi
```



## case

case语句和 if..elif..else 语句都是多分支条件语句，case 语句只能判断一种条件关系，而 if 语句可以判断多种条件关系。

```sh
case $arg in
	"val1")
				command1
				;;
	"val2")
				command2
				;;
	*)
				command3
				;;
esac		
```



## for

👉 列表for循环

执行次数和列表中常数或字符串的个数相同

```sh
for variable in (list)
do 
    command
    command
    ...
done
```



示例：

```sh
for variable  in 1 2 3 4 5 
do
    echo "Hello, welcome $variable  times "
done
```





👉 非列表for循环

非列表for循环的方式只可从命令行来传递参数

```sh
for variable
do 
    command
    command
    ...
done
```



示例：

```sh
echo "number of arguments is $#"
echo "What you input is :"

# 使用命令打印数组
for argument 
do
    echo "$argument "
done

$ sh example.sh a b c
```



👉 类C风格for循环

```sh
for((expr1; expr2; expr3))
do
    command
    command
    ...
done
```

示例：

```sh
for ((i=1; i<=5; i++)) 
do
    echo "$i"
done
```



## while

```sh
while [ 条件判断式 ]
do
		程序
done
```



## until

和 while 循环相反，until 循环时只要条件判断式不成立则进行循环，并执行循环程序。一旦循环条件成立，则终止循环。

```sh
until [ 条件判断式 ]
do
		程序
done
```
