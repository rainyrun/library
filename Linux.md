# Linux

## linux虚拟机安装

将linux操作系统安装在虚拟机上，虚拟机可以有多种选择：VMware、VirtualBox等。选择一种安装即可

在虚拟机上安装CentOS操作系统，操作系统的类型选择“RedHat”（CentOS是RedHat旗下的）。然后根据提示一步步安装即可。

[virtualbox centos7 虚拟机安装参考文档](https://www.cnblogs.com/xyinjie/p/9437049.html)

[virtualBox增强功能、共享文件夹设置](https://blog.csdn.net/buyueliuying/article/details/51645649)

[解决vbox增强功能安装中kenel-devel版本不匹配问题](https://blog.csdn.net/u012247980/article/details/52752276)

网络配置

CentOS在安装过程中没有网络，则安装后无法联网，需要进行网络配置。

以VirtualBox虚拟机上安装的CentOS为例

1. 在“管理->主机网络管理器”中，创建一个“仅主机(host-only)网络”。
2. 选中CentOS，点击“设置->网络”。
	1. 网卡1连接方式选择“网络地址转换NAT”。用来使CentOS能够连接外网。
	2. 网卡2连接方式选择“仅主机(host-only)网络”，界面名称选刚刚创建的那个网络的名称。这个网卡用来和主机进行通信。
3. 启动CentOS，在/etc/sysconfig/network-scripts/ifcfg-enp0s3文件中，把onboot=no改成noboot=yes
4. 在CentOS尝试ping外网，如果ping不通外网（如baidu.com），则使用dhclient命令，获得一个ip。使用ip addr查看虚拟机ip地址，一般10开头的是外网地址，192开头的是内网地址。
5. 使用主机ping虚拟机的内网地址，如果ping的通，tomcat就能连接。

## 概述

目录结构

- usr：相当于program files
- etc：存放配置文件
- root：系统管理员默认文件
- home：存放其他用户的目录
- lib：存放程序需要的共享库及内核模块
- mnt：临时文件




## 常用命令

|命令|作用|选项|参数|
|---|---|---|
|ls|显示当前目录的内容|-l：以长模式输出(更详细的内容)<br />-a：显示隐藏文件|文件路径。可以是多个，以空格分隔|
|ll|显示指定文件的内容||
|cd|切换目录 change directory|~：当前用户目录<br />~user_name：user_name的用户目录<br />/：根目录<br />-：上次访问目录<br />..：上一级目录<br />空：缺省当前用户目录|
|touch|创建文件|文件名|
|clear|清屏||
|pwd|显示当前工作目录print working directory||
|mkdir|创建目录|目录名<br />-p：父目录不存在，先创建父目录|
|cat|显示文本内容|文件名|
|more|分段显示文件内容。操作：<br />Enter：向下n行<br />b：后退一页<br />空格：翻一页|文件名|
|less|可以滑动查看文件内容。q退出。是more的改进版||文件名|
|tail|显示指定文件末尾内容|-10：后10行内容|
|cp|复制|-b：将原文件做备份<br />-r：复制目录|
|mv|剪切移动或重命名|-f：目标文件在|
|rm|删除|-f：强制删除（没有提示）<br />-r：递归处理，用于删除目录|
|find|搜索文件|语法：find[目录][参数]|
|ps|查看进程|-ef|
|ifconfig|显示网络信息||
|ping|测试与目标主机的连通性||
|file|确定文件类型||

### less

常用的键盘命令

|命令|行为|
|--|--|
|Page UP or b|向上翻滚一页|
|Page Down or space|向下翻滚一页|
|UP Arrow|向上翻滚一行|
|Down Arrow|向下翻滚一行|
|G|移动到最后一行|
|1G or g|移动到开头一行|
|/charaters|向前查找指定的字符串|
|n|向前查找下一个出现的字符串，这个字符串是之前所指定查找的|
|h|显示帮助屏幕|
|q|退出 less 程序|

### vi或vim

|分组|命令|作用|
|--|--|--|
|插入|i|插入到光标位置|
|复制或粘贴|yy|复制单行|
||nyy|复制多行|
||p|粘贴|
|定位|gg|到文本的第一行|
||shift+gg|到文本最后一行|
|删除|dd|删除光标所在行|
||ndd|删除n行|
|退出|:q|退出（未修改内容时）|
||:q!|退出不保存|
||:w|保持不退出|
||:wq|保持退出|
|查询|/查询的字符串|在非编辑模式下，输入/再输入查询的字符串|
||n|查询下一个命中的字符串|
||N|查询上一个命中的字符串|
|撤销|u|撤销刚才的操作|
||ctrl+r|恢复刚才撤销的动作|
|其他|:set number|显示行号|
||w !sudo tee %|修改只读文件|

解释如下：

:w – Write a file.

!sudo – Call shell sudo command.

tee – The output of write (vim :w) command redirected using tee.

% – is nothing but current file name

### grep

过滤命令，通常和其他命令一起使用

```sh
# 使用格式
grep -i 名称
# 查询install.log中含mysql的内容并显示在屏幕上
cat /root/install.log | grep -i mysql
```

### 管道

命令1 | 命令2

命令1的输出是命令2的输入

kill

kill -9 pid


快捷键
⬆：显示上一条命令（命令历史默认保留500条）
复制：鼠标左键选中的内容
粘贴：鼠标中键

### tar 命令

|选项|说明|
|---|---|
|-c|create 建立压缩档案的参数|
|-x|解压一个压缩文件的参数指令|
|-z|是否需要用gzip压缩|
|-v|压缩的过程中显示文件|
|-f|使用档名，在f之后要立即接档名|
|-C|解压到指定文件夹|

常用解压参数组合：zxvf
常用压缩参数组合：zcvf

```sh
# 将文件解压到/source/linux-2.6.29
tar zxvf /source/kernel.tgz -C /source/linux-2.6.29
# 将文件压缩到linux-2.6.29
tar czvf kernel.tgz linux-2.6.29
```



### 系统命令

重启：reboot

关机：halt


帮助 

查看内部命令：help + 命令

查看外部命令：man + 命令


网络服务

1. 使用service脚本来调度网络服务，如：
	- 启动 service network start
    - 关闭 service network stop
    - 重启 service network restart

2. 直接执行网络服务的管理脚本，如：
	- 启动 /etc/init.d/network start
	- 关闭 /etc/init.d/network stop
	- 重启 /etc/init.d/network restart



useradd testuser  创建用户testuser
passwd testuser  给已创建的用户testuser设置密码
说明：新创建的用户会在/home下创建一个用户目录testuser
usermod --help  修改用户这个命令的相关参数
userdel testuser  删除用户testuser
rm -rf testuser  删除用户testuser所在目录

上面的几个命令只有root账号才可以使用，如果你不知道自己的系统上面的命令在什么位置可以使用如下命令查找其路径：

locate useradd



命令行窗口下用户的相互切换：
su 用户名
说明：su是switch user的缩写，表示用户切换



用户组的添加和删除：
groupadd testgroup    组的添加
groupdel testgroup    组的删除
说明：组的增加和删除信息会在etc目录的group文件中体现出来。


### rpm

|命令|作用|备注|
|--|--|--|
| `rpm -qa | grep -i java` |查看本机上所有已安装成功的软件，只查看和java相关的||
| `rpm -e --nodeps softwarename` |删除安装的某软件|--nodeps表示强制删除|
| `rpm -ivh 包` |安装某个软件|

rpm 等包方式的话,就要查其中的数据库了,比如 rpm -q 进行查询.
-q <== 查询(查询本机已经安装的包时不需要版本名称)
-qi #查询被安装的包的详细信息(information)
-qa | grep dhcp <== 列出所有被安装的rpm package
-qc 列出配置文件(/etc下的文件)
-qd 列出帮助文件(man)
-ql dhcp <== 查询指定 rpm 包中的文件列表
-qf /bin/ls <== 查询哪个库里包含了 ls 文件(注意，需要安装了 /bin/ls 后才能查到)
-qp < rpm package name> <== 根据rpm包查询(.rpm 文件),可以接其他参数(如i查详细信息，l查文件列表 等)
-qR 列出需要的依赖套件



比如安装xxx.rpm包，以relocate 参数进行安装，安装到/opt/temp目录：

rpm -ivh --relocate /=/opt/temp xxx.rpm；

以prefix进行安装：

rpm -ivh --prefix= /opt/temp  xxx.rpm

## CentOS 7 相关的命令

### firewall

|命令|作用|
|--|--|
| `firewall-cmd —-list-ports` |查看已开放端口|
| `firewall-cmd —-permanent —-zone=public —-add-port=8080/tcp` |添加开放端口号8080|
| `firewall-cmd —-reload` |重启firewall|
| `firewall-cmd —-zone=public —-query-port=8080/tcp` |检查是否生效|
| `systemctl stop firewalld.service` |停止firewall|
| `systemctl disable firewalld.service` |禁止firewall开机启动|
| `firewall-cmd --state` |检验防火墙是否启动|

命令含义：

- -–zone：作用域
- -–add-port=80/tcp：添加端口，格式为：端口/通讯协议
- –-permanent：永久生效，没有此参数重启后失效(-—permanent放在前面与后面都行)


|命令|作用|
|--|--|
|`netstat -lnp | grep 80`| 检查端口被哪个进程占用。 80端口，自行更换；如：8888|

## Mac 相关的命令

1.查找端口进程

```
lsof -i:8000
```

2.终止进程 kill + pid

```
kill 85877
sudo kill -9 61342
```

查本机ip ifconfig

## Ubuntu 相关的命令

防火墙

|命令|作用|
|--|--|
| `sudo ufw enable` |防火墙的打开|
| `sudo ufw reload` |防火墙的重启|
| `ufw allow 8080` |打开8080端口|



Linux Command Line

## shell 相关

简单命令

|命令|说明|
|--|--|
|date|显示当前时间和日期|
|cal|显示当前月份的日历|
|df|硬盘空闲空间|
|free|内存空闲空间|
|exit|关闭终端session|

### 查看文件系统

|命令|参数or选项|说明|
|--|--|
|pwd||打印当前工作目录 print working directory|
|cd|pathname|改变目录 change directory to pathname|
||cd|切换到home directory|
||cd -|切换到之前的目录|
||cd ~username|切换到username用户的home directory|
|ls|pathname|列出pathname目录的内容 list directory|
||-a|列出所有文件，包括隐藏文件(名字以.开头的文件)|
||pathname1 pathname2|列出多个目录下的文件|
||-l|使显示内容为long format|
||-t|以修改时间排序|
|file|filename|查看文件类型|
|less||查看文件内容|

每一个用户都有一个 home directory，在这个目录下才能写入文件。

pathname 路径说明

1. 绝对路径(从根目录开始)
	- 以"/"开头
2. 相对路径(从当前目录开始)
	- 以"./"开头，可以省略
	- "."表示当前目录
	- ".."表示上一级目录

文件名规则

- 命令和目录对大小写敏感。
- 类Unix系统可以任意命名文件，扩展名对这类系统没有意义（但某些应用依赖扩展名定义文件类型）
- 文件名里的标点，尽量仅使用`.`, `-`, `_`。文件名中不要使用空格。

options 有2种格式

1. 短格式：-单个字符，如ls -l。多个option可以合并，如ls -la
2. 长格式：--单词，如ls -lt --reverse。

ls 命令的选项表

|选项|长选项|描述|
|--|--|--|
|-a|--all|列出所有文件，甚至包括文件名以圆点开头的默认会被隐藏的隐藏文件。|
|-d|--directory|通常，如果指定了目录名，ls 命令会列出这个目录中的内容，而不是目录本身。 把这个选项与 -l 选项结合使用，可以看到所指定目录的详细信息，而不是目录中的内容。|
|-F|--classify|这个选项会在每个所列出的名字后面加上一个指示符。例如，如果名字是 目录名，则会加上一个'/'字符。|
|-h|--human-readable|当以长格式列出时，以人们可读的格式，而不是以字节数来显示文件的大小。|
|-l||以长格式显示结果。|
|-r|--reverse|以相反的顺序来显示结果。通常，ls 命令的输出结果按照字母升序排列。|
|-S||命令输出结果按照文件大小来排序。|
|-t||按照修改时间来排序。|

输出格式

长格式的输出字段

`-rw-r--r-- 1 root root   32059 2007-04-03 11:05 oo-cd-cover.odf`

含义

|字段|含义|
|--|--|
|-rw-r--r--|对于文件的访问权限。第一个字符指明文件类型(以“－”开头说明是一个普通文件，“d”表明是一个目录）。其后三个字符是文件所有者的访问权限，再其后的三个字符是文件所属组中成员的访问权限，最后三个字符是其他所 有人的访问权限。|
|1|文件的硬链接数目。|
|root|文件所有者的用户名。|
|root|文件所属用户组的名字。|
|32059|以字节数表示的文件大小。|
|2007-04-03 11:05|上次修改文件的时间和日期。|
|oo-cd-cover.odf|文件名。|

### 操作文件和目录

|命令|参数or选项|说明|
|---|---------|---|
|cp|item1 item2|将文件或目录item1复制到文件或目录item2|
||item... directory|将一堆文件item...复制到目录directory|
|mv|item1 item2|将item1移动到或(重命名为)item2|
||item... directory|将一堆item移动到directory|
|mkdir|directory...|创建目录|
|rm|item...|删除文件和目录|
|ln|file link|创建硬链接|
||-s item link|创建符号链接，item是目标文件相对于符号链接的路径，或者目标文件的绝对路径|

通配符

|通配符|说明|
|---|---|
|*|匹配任意字符|
|?|匹配单个字符|
|[characters]|匹配集合中的任意一个字符|
|[!characters]|匹配不在集合中的任意一个字符|
|[[:class:]]|匹配指定class中的任意一个字符|

常用的 class

|class|说明|
|---|---|
|[:alnum:]|匹配任意字母or数字|
|[:alpha:]|匹配任意字母|
|[:digit:]|匹配任意数字|
|[:lower:]|匹配任意小写字母|
|[:upper:]|匹配任意大写字母|

### 操作命令的命令

|命令|参数or选项|说明|
|---|---------|---|
|type|command|显示一个指令的类型|
|which|command|显示可执行程序的安装路径|
|help|shell_builtins|shell builtin的帮助|
||mkdir --help|可执行程序的--help选项，显示指令支持的语法和选项|
|man|program|显示一个指令的说明手册，使用less来展示页面|
||section search_term|显示search_term的5号类型的手册。当有重名的不同类型的指令时，需要指定类型|
|apropos|floppy|显示说明页中含有floppy的指令。man的-k选项有相同的功能|
|info|program|显示一个命令的信息实体(GNU项目提供，用来替代man指令)|
||coreutils|指令的菜单|
|whatis||显示一个命令的简短描述|
|alias|name='string'|为一个或多个指令创建别名|
||空|查看所有的别名|
|unalias|name|取消别名|

指令(command)可能是以下4种类型

1. 可执行程序。像 `/usr/bin` 目录下的文件
2. shell的内建指令。被称为 shell builtins
3. shell程序。
4. 别名。可以从其他命令自定义。

Man Page Organization

|Section|Contents|
|----|----|
|1|User commands|
|2|Programming interfaces for kernel system calls|
|3|Programming interfaces to the C library|
|4|Special files such as device nodes and drivers|
|5|File formats|
|6|Games and amusements such as screen savers|
|7|Miscellaneous|
|8|System administration commands|

info Commands

|Command|Action|
|---|---|
|?|显示帮助|
|PgUp or Backspace|上一页|
|PgDn or Space|下一页|
|n|next 下一个node|
|p|previous 上一个node|
|u|up 父node，通常是一个menu|
|Enter|跳转到链接(以 `*` 开头)指向的页面|
|q|退出|

安装的软件的文档通常在 `/usr/share/doc` 目录

以 `.gz` 结尾的文件是被gzip程序压缩过的文件，可以使用 zless 命令查看文件内容

多个命令以";"隔开

`command1; command2; command3...`

### 重定向

|命令|参数or选项|说明|
|---|---------|---|
|cat|[file...]|Concatenate files 读入一个或多个文件，然后将它们复制到标准输出|
||空|读入标准输入的内容到标准输出，ctrl + d表示文件到达结尾(EOF)|
||> lazy_dog.txt|将标准输入的内容输出到文件lazy_dog中。即创建文件并输入内容|
||< lazy_dog.txt|读入lazy_dog的内容到标准输出|
|sort||Sort lines of text|
|uniq||通常和sort一起使用。接受排序的list并去除重复项|
||-d|查看已排序list中的重复项|
|grep|pattern [file...]|查找文件中符合pattern的行|
||-i|查找时忽视大小写|
||-v|打印不符合pattern的行|
|wc|filename|word count，显示一个文件中行、词、字节的个数|
||-l|只显示行数|
|head|filename|打印文件前10行|
||-n number|打印文件前number行|
|tail||打印文件后10行|
||-n number|打印文件后number行|
||-f|实时查看动态文件(如，日志)更新的内容|
|tee|filename|像T一样，读入标准输入，并写入标准输出和1~N个文件|

stdout、stderr 通常指向屏幕（Linux中一切皆文件），内容写入屏幕而不存储到硬盘

stdin通常指向键盘

重定向可以改变标准输入输出的指向

">"是重定向符，可以将输出保存到指向的文件。每次使用时，文件会从一开始就被重写。

没有一个符号重定向到标准错误输出，只能使用文件描述符。

标准输入、标准输出、标准错误输出的文件描述符分别为0、1、2

```sh
# 重定向标准输出
ls -l /usr/bin > ls-output.txt
# 创建一个空的新文件，或者清空一个已有文件
> newfile
# 将输出内容追加到文件
ls -l /usr/bin >> ls-output.txt
# 重定向标准错误输出（2> 中间没有空格）
ls -l /bin/usr 2> ls-error.txt
# 将标准输出、标准错误输出重定向到同一个文件。
# 方法1:
# 	1. 标准输出重定向到ls-output.txt
# 	2. 标准错误输出(2)重定向到标准输出(&1)
ls -l /bin/usr > ls-output.txt 2>&1
# 方法2: 使用 &> 符号
ls -l /bin/usr &> ls-output.txt
# 以追加的方式重定向
ls -l /bin/usr &>> ls-output.txt

# 不输出错误信息。/dev/null是一个特殊的文件，接受输入然后什么也不做
ls -l /bin/usr 2> /dev/null
```

管道 |

格式

`command1 | command2`

解释

将 command1 的输出当作 command2 的输入

例子

```sh
# 用less输出结果
ls -l /usr/bin | less

# 过滤器，将可执行程序排序并输出
ls /bin /usr/bin | sort | less
# 去重
ls /bin /usr/bin | sort | uniq | less
# 查看重复项
ls /bin /usr/bin | sort | uniq -d | less
```

### echo

```sh
# 显示目录
echo *
echo D*
echo *s
ehco [[:upper:]]*
# 显示用户的主目录
echo ~
ecoh ~foo
# 计算
echo $((2+2))
# 大括号brace expansion
echo Num{1..3} # Num1 Num2 Num3
echo {01..03} # 01 02 03
# 打印参数
echo $USER
# 当前可用参数
printenv | less
# 显示cp命令的目录
ls -l $(which cp)
```

双引号

双引号中的 

```sh
空格 \ ~ {}  
```
将被当作普通字符

```sh
$USER $((2+2)) $(cal)
```
仍可执行

单引号

单引号中的所有字符都被当成普通字符。

转义字符 `\`
```sh
echo "\$5.00" # $5.00
echo bad\&fimename # bad&filename
# -e选项 使用转移序列
sleep 10; echo -e "Time's up\a"
```

### 其他命令

|命令|参数or选项|说明|
|---|---------|---|
|clear||清屏|

光标移动命令

|命令|说明|
|---|---|
|Ctrl-a|移动到一行的开头|
|Ctrl-e|移动到一行的末尾|
|Ctrl-f|向前移动1个字符|
|Ctrl-b|向后移动1个字符|
|Alt-f|向前移动1个单词|
|Alt-b|向后移动1个单词|
|Ctrl-l|清屏，将鼠标移到左上角，同clear|

文本编辑命令

|命令|说明|
|---|---|
|Ctrl-d|删除光标所在的字符|
|Ctrl-t|用光标前的一个字符替换光标所在的字符|
|Alt-t|用光标前的一个单词替换光标所在的单词|
|Alt-l|将光标坐在位置到单词末尾的字符转换成小写|
|Alt-u|将光标坐在位置到单词末尾的字符转换成大写|

剪切和粘贴文本

|命令|说明|
|---|---|
|Ctrl-k|剪切光标所在位置到行末尾的文本|
|Ctrl-u|剪切行开头到光标所在位置的文本|
|Alt-d|剪切光标所在位置到当前单词末尾的文本|
|Alt-Backspace|剪切当前单词开头到光标所在位置的文本。如果光标在一个单词的开头，则剪切前一个单词|
|Ctrl-y|复制文本到光标所在位置|

补全

在敲命令的时候，按tab键，会补全命令。

可以补全的有：路径名、变量、用户名、命令、主机名(hostname)

|命令|说明|
|---|---|
|Alt-?|显示可能的补全列表。也可以长按tab键|
|Alt-*|插入所有可能的补全|

history 命令

历史命令存储在用户主目录的.bash_history文件里

|命令|说明|
|---|---|
|history|查看历史命令|
|!number|number是历史命令的行号，bash会自动将!number扩展为number行的命令|
|!!|重复最后一条命令|
|!string|重复最后一条以string开头的历史命令，谨慎使用|
|!?string|重复最后一条包含string的历史命令，谨慎使用|
|ctrl-r|搜索历史命令。ctrl-j将搜索到的命令复制到命令行；enter执行搜索到的命令；ctrl-r下一个搜索到的命令|
|ctrl-p|上一个搜索的命令。和⬆效果相同|
|ctrl-n|下一个搜索的命令。和⬇效果相同|
|Alt-<|history list的最开头的命令|
|Alt->|当前命令|
|Alt-p|搜索|
|Alt-n|前向搜索，非递增|
|Ctrl-o|执行history list的当前项，并前进到下一条|
|script [file]|记录已执行的命令到file|

### 权限

|命令|参数or选项|说明|
|---|---------|---|
|id||显示用户id|
|chmod|mode fileName|改变文件的mode(权限)，如chmod 600 foo.txt|
|umask||设置默认文件权限|
|su||切换用户|
|sudo||使用其他用户的权限运行命令|
|chown||改变文件的所有者|
|chgrp||改变文件的组|
|passwd||修改用户的密码|

当用户账户被创建时，会被分配一个数字叫做用户id(uid)，会被分配一个初始组id(gid)

用户信息在/etc/passwd文件中，组信息在/etc/group文件中。用户密码在/etc/shadow中。

mod 表示文件的权限。由10个字母组成(ll命令的结果，第一列就是mod)。例子：`-rwxrwxrwx`

- 第一个字母表示文件类型。-表示文件，d表示目录，l表示链接
- 之后3个字母表示owner的权限，即文件所有者的权限。r表示读，w表示写，x表示可执行，-表示没有该权限。
- 再后3个字母表示group的权限，即文件所有者所在组的其他人的权限。
- 最后3个字母表示world的权限，即其余所有使用这台计算机的人的权限。

Linux为用户创建一个同名的独属于用户自己的组。

```sh
# 查看所有用户的列表
cat /etc/passwd
# 查看当前活跃的用户列表
w
# 查看用户组
cat /etc/group 
# 查看当前登录用户的组内成员
groups
# 查看gliethttp用户所在的组,以及组内成员
groups gliethttp 
# 查看当前登录用户名
whoami 

cat /etc/passwd|grep -v nologin|grep -v halt|grep -v shutdown|awk -F":" '{ print $1"|"$3"|"$4 }'|more
```


chmod 符号标志

|符号|意思|
|--|--|
|u|user的缩写，指代文件或目录的所有者|
|g|组的所有者|
|o|others的缩写，意思是 world（其他所有人）|
|a|all的缩写，u,g,o的并集|
|+|添加权限|
|-|取消权限|
|=|设置权限|

例子

| 举例 | 意思 |
| ---- | ---- |
|u+x|给owner增加执行权限|
|+x|等同于a+x，给所有人添加执行权限|
|o-rw|world去除读写权限|
|go=rw|group，world的权限为读写|
|u-x,go=rw|user去除执行权限，group、world权限为读写。注意中间不要加空格|
|u+s|分配setuid|
|g+s|分配setgid|
|+t|分配sticky bit|

umask

创建一个文件时，使用umask设置默认权限。umask使用4位八进制数表示，二进制下1出现的位置的权限将被去掉。

| 说明 | 字段 |
| ---- | ---- |
|初始文件mode|--- rw- rw- rw-|
|Mask|000 000 000 010|
|结果|--- rw- rw- r--|

改变身份

| 命令 | 选项or参数 | 说明 |
| ---- | ---------- | ---- |
|su|[-[l]][user]|用其他用户身份新建一个shell。如果有-l选项（可以简写为-），新建的shell是登录后的shell，即会切换到用户的主目录下。不指定user时，默认时root用户|
||-c 'command'|新建shell运行command后，立马返回|
|sudo||被授权后，可以以其他用户的身份运行命令|






上传下载文件

```sh
scp root@49.233.196.23:/home/test.txt .   //下载文件到当前目录
scp test.txt root@107.172.27.254:/home  //上传文件
scp -r root@107.172.27.254:/home/test .  //下载目录
scp -r test root@107.172.27.254:/home   //上传目录

// 使用密钥
scp -i 密钥地址 ...
```

## 生成SSH公钥

```sh
# 用户的 SSH 密钥默认存储在其 ~/.ssh 目录下
cd ~/.ssh
ls
# 寻找一对以 id_dsa 或 id_rsa 命名的文件，其中一个带有 .pub 扩展名。
# 如果没有，则创建它们
ssh-keygen

```





top

默认按照CPU的使用率排序，通过“shift+m”按键将进程按照内存使用情况排序。

curl

curl是一个利用URL规则在命令行下工作的文本传输工具，它支持上传和下载。

```sh
# 下载单个文件，默认将输出打印到标准输出(stdout)中
curl http://www.centos.org

# -o:将文件保存为命令行中指定的文件名的文件中
curl -o mygettext.html http://www.gnu.org/software/gettext/manual/gettext.html

# -O:使用URL中默认的文件名保存文件到本地
curl -O http://www.gnu.org/software/gettext/manual/gettext.html

# 同时获取多个文件。若同时从同一个站点下载多个文件时，curl会尝试重用链接(connection)。 
curl -O URL1 -O URL2

# 通过-L选项进行重定向
# 默认情况下CURL不会发送HTTP Location headers(重定向)，当一个被请求页面移动到另一个站点时，会发送一个HTTP Location header作为请求，然后将请求重定向到新的地址上。 例如，访问google.com 时，会自动将地址重定向到google.com.hk上：
curl http://www.google.com
<HTML>
<HEAD>
<meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>302 Moved</TITLE>
</HEAD>
<BODY>
<H1>302 Moved</H1>
The document has moved
<A HREF="http://www.google.com.hk/url?sa=p&amp;hl=zh-CN&amp;pref=hkredirect&amp;pval=yes&amp;q=http://www.google.com.hk/&amp;ust=1379402837567135amp;usg=AFQjCNF3o7umf3jyJpNDPuF7KTibavE4aA">here</A>.
</BODY>
</HTML>

上述说明，所有请求的档案被转移到了http://www.google.com.hk 
这时可以通过使用-L选项进行强制的重定向：

# 让curl使用地址进行重定向，此时会查询google.com.hk站点
curl -L http://www.google.com

断点续传 
通过使用-C选项可对大文件使用断点续传功能，如:

# 当文件在下载完成之前结束该进程
$ curl -O http://www.gnu.org/software/gettext/manual/gettext.html
############## 20.1%

# 通过添加-C选项继续对该文件进行下载，已经下载过的文件不会被重新下载
curl -C - -O http://www.gnu.org/software/gettext/manual/gettext.html
############### 21.1%

从FTP服务器下载文件 
CURL同样支持FTP下载，若在url中指定的是某个文件路径而非具体的某个要下载的文件名，CURL则会列出该目录下的所有文件名而非下载该目录下的所有文件：

# 列出public_html下的所有文件夹和文件
curl -u ftpuser:ftppass -O ftp://ftp_server/public_html/ 
# 下载xss.php文件
curl -u ftpuser:ftppass -O ftp://ftp_server/public_html/xss.php



1、发送get请求

curl http://127.0.0.1:8080/tvseries/
2、发送post请求
curl使用POST提交表单数据时，除了-X参数指定请求方法外，还要使用--data参数添加提交数据：
curl -H "Content-Type:application/json" -X POST --data '{"name":"test","sex":"Male"}' http://127.0.0.1/tvseries/
3、发送delete请求

curl -X DELETE http://127.0.0.1:8080/tvseries/{id}/
4、发送put请求

curl -H "Content-Type:application/json" -X PUT --data '{"name":"test2"}' http://127.0.0.1:8080/tvseries/{id}/

查看头信息

如果需要查看访问页面的可以添加-i或--include参数：

$ curl -i itbilu.com
添加-i参数后，页面响应头会和页面源码（响应体）一块返回。如果只想查看响应头，可以使用-I或--head参数：
```