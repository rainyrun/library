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
||shift+4|光标定位到行尾|
||shift+6|光标定位到行首|
|删除|dd|删除光标所在行|
||ndd|删除n行|
|退出|:q|退出（未修改内容时）|
||:q!|退出不保存|
||:w|保持不退出|
||:wq|保持退出|
|查询|/查询的字符串|在非编辑模式下，输入/再输入查询的字符串|
||n|查询下一个命中的字符串|
||N|查询上一个命中的字符串|
|替换|:s|将某个文本替换成另一个文本|
||:s/str1/str2/| 用字符串 str2 替换行中首次出现的字符串 str1|
||:s/str1/str2/g|用字符串 str2 替换行中所有出现的字符串 str1|
||:%s/str1/str2/|替换每一行的第一个 str1 为 str2|
||:%s/str1/str2/g|替换每一行中所有 str1 为 str2|
||:1,$ s/str1/str2/g|功能同上|
||:g/str1/s//str2/g|功能同上|
||:.,$ s/str1/str2/g|用字符串 str2 替换正文当前行到末尾所有出现的字符串 str1|
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

wget 命令

wget是一个下载文件的工具。基本的语法是：wget [参数列表] URL。

```sh
# 下载单个文件或网页。-x会强制建立服务器上一模一样的目录，如果使用-nd参数，那么服务器上下载的所有内容都会加到本地当前目录。 
wget http://cn.wordpress.org/wordpress-3.1-zh_CN.zip # 文件的下载链接
# -O下载并以不同的文件名保存。wget默认会以最后一个“/”的后面的字符来命名文件
wget -O 下载文件名 下载链接
# 限速下载
wget --limit-rate=300k 下载链接
# 断点续传，重启中断的下载。-t参数表示重试次数，例如-t 100（重试100次），-t 0（无穷次重试，直到连接成功）。-T参数表示超时等待时间，例如-T 120，表示等待120秒连接不上就算超时。 
wget -c 下载链接
# 后台下载
wget -b 下载链接
# 查看后台下载的进度
tail -f wget-log
# 伪装代理名称下载
wget --user-agent="Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US) AppleWebKit/534.16 (KHTML, like Gecko) Chrome/10.0.648.204 Safari/534.16" 下载链接 
# 测试下载链接是否有效
wget --spider URL
# 增加重试次数
wget --tries=40 URL
# 批量下载
wget -i filelist.txt # filelist.txt 里有多个下载链接，以换行分隔
# 镜像网站，下载整个网站到本地。 
wget --mirror -p --convert-links -P ./LOCAL URL 
# –miror:开户镜像下载 
# -p:下载所有为了html页面显示正常的文件 
# –convert-links:下载后，转换成本地的链接 
# -P ./LOCAL：保存所有文件和目录到本地指定目录 

# 制作镜像站点。wget会登录到服务器上，读入robots.txt并按robots.txt的规定来执行。 
wget -m http://place.your.url/here
# 选择性下载。–accept=LIST 可以接受的文件类型，–reject=LIST 拒绝接受的文件类型。 
wget --reject=gif url # 不下载gif格式的文件
# 下载信息存入日志文件
wget -o download.log URL 
# 限制总下载文件大小，当超过该大小时，退出下载。注意：这个参数对单个文件下载不起作用，只能递归下载时才有效。
wget -Q5m -i filelist.txt 
# 下载指定格式文件  
wget -r -A.pdf url # 下载一个网站的所有PDF文件
# 来完成ftp链接的下载。 
wget ftp-url # 使用wget匿名ftp下载
wget –ftp-user=USERNAME –ftp-password=PASSWORD url # 使用wget用户名和密码认证的ftp下载 
# 递归下载服务器上所有的目录和文件，实质就是下载整个网站。如果这个网站引用了其他网站，那么被引用的网站也会被下载下来。不常用。可以用-l number参数来指定下载的层次。例如只下载两层，那么使用-l 2。 
wget -r http://place.your.url/here 

# 密码和认证。 
# wget只能处理利用用户名/密码方式限制访问的网站，可以利用两个参数： 
# –http-user=USER设置HTTP用户 
# –http-passwd=PASS设置HTTP密码 
# 对于需要证书做认证的网站，就只能利用其他下载工具了，例如curl。 

# 利用代理服务器进行下载。需要在当前用户的目录下创建一个.wgetrc文件。文件中可以设置代理服务器： 
# http-proxy = 111.111.111.111:8080 
# ftp-proxy = 111.111.111.111:8080 
# 分别表示http的代理服务器和ftp的代理服务器。如果代理服务器需要密码则使用： 
# –proxy-user=USER设置代理用户 
# –proxy-passwd=PASS设置代理密码 
# 这两个参数。 
# 使用参数–proxy=on/off 使用或者关闭代理。


-V,–version 显示软件版本号然后退出； 
-h,–help显示软件帮助信息； 
-e,–execute=COMMAND 执行一个 “.wgetrc”命令 

-o,–output-file=FILE 将软件输出信息保存到文件； 
-a,–append-output=FILE将软件输出信息追加到文件； 
-d,–debug显示输出信息； 
-q,–quiet 不显示输出信息； 
-i,–input-file=FILE 从文件中取得URL； 

-t,–tries=NUMBER 是否下载次数（0表示无穷次） 
-O –output-document=FILE下载文件保存为别的文件名 
-nc, –no-clobber 不要覆盖已经存在的文件 
-N,–timestamping只下载比本地新的文件 
-T,–timeout=SECONDS 设置超时时间 
-Y,–proxy=on/off 关闭代理 

-nd,–no-directories 不建立目录 
-x,–force-directories 强制建立目录 

–http-user=USER设置HTTP用户 
–http-passwd=PASS设置HTTP密码 
–proxy-user=USER设置代理用户 
–proxy-passwd=PASS设置代理密码 

-r,–recursive 下载整个网站、目录（小心使用） 
-l,–level=NUMBER 下载层次 

-A,–accept=LIST 可以接受的文件类型 
-R,–reject=LIST拒绝接受的文件类型 
-D,–domains=LIST可以接受的域名 
–exclude-domains=LIST拒绝的域名 
-L,–relative 下载关联链接 
–follow-ftp 只下载FTP链接 
-H,–span-hosts 可以下载外面的主机 
-I,–include-directories=LIST允许的目录 
-X,–exclude-directories=LIST 拒绝的目录 

中文文档名在平常的情况下会被编码， 但是在 –cut-dirs 时又是正常的， 
wget -r -np -nH –cut-dirs=3 ftp://host/test/ 
测试.txt 
wget -r -np -nH -nd ftp://host/test/ 
%B4%FA%B8%D5.txt 
wget “ftp://host/test/*” 
%B4%FA%B8%D5.txt 

由 於不知名的原因，可能是为了避开特殊档名， wget 会自动将抓取档名的部分用 encode_string 处理过， 所以该 patch 就把被 encode_string 处理成 “%3A” 这种东西， 用 decode_string 还原成 “:”，并套用在目录与档案名称的部分，decode_string 是 wget 内建的函式。 

wget -t0 -c -nH -x -np -b -m -P /home/sunny/NOD32view/ http://downloads1.kaspersky-labs.com/bases/ -o wget.log
```

给普通用户添加root权限

sudo命令可以让用户在当前账号下使用root权限

```sh
# 1. 以root用户查看/etc/sudoers
ls -l /etc/sudoers
# 2. 将sudoers文件更改成可写状态
chmod 777 /etc/sudoers
# 3. 添加有root权限的用户到文件内
vim /etc/sudoers
## Allow root to run any commands anywhere
# root    ALL=(ALL)       ALL
# hadoop     ALL=(ALL)       ALL
# 4. 将文件改为只读
chmod 440 /etc/sudoers
# 切换普通用户，就可以用sudo命令了
```

### 修改命令行提示符的颜色

PS1是Linux终端用户的一个环境变量，用来定义命令行提示符的参数。

```sh
# 当前PS1的定义值
echo $PS1
```

PS1的常用参数以及含义:

```
\d ：代表日期，格式为weekday month date，例如："Mon Aug 1"
\H ：完整的主机名称
\h ：仅取主机名中的第一个名字
\t ：显示时间为24小时格式，如：HH：MM：SS
\T ：显示时间为12小时格式
\A ：显示时间为24小时格式：HH：MM
\u ：当前用户的账号名称
\v ：BASH的版本信息
\w ：完整的工作目录名称
\W ：利用basename取得工作目录名称，只显示最后一个目录名
\# ：下达的第几个命令
\$ ：提示字符，如果是root用户，提示符为 # ，普通用户则为 $
```

颜色设置参数

在PS1中设置字符颜色的格式为：`\[\e[F;Bm\]........\[\e[0m\]`，其中“F“为字体颜色，编号为30-37，“B”为背景颜色，编号为40-47，`\[\e[0m\]` 作为颜色设定的结束。

颜色对照表：

```
F    B
30  40 黑色
31  41 红色
32  42 绿色
33  43 黄色
34  44 蓝色
35  45 紫红色
36  46 青蓝色
37  47 白色

如要设置命令行的格式为绿字黑底(\[\e[32;40m\])，显示当前用户的账号名称(\u)、主机的第一个名字(\h)、完整的当前工作目录名称(\w)、24小时格式时间(\t)，可以直接在命令行键入如下命令

PS1='[\[\e[32;40m\]\u@\h \w \t]$ \[\e[0m\]'
```

修改.bashrc文件，永久保存命令行样式。

```sh
vim ~/.bashrc
source .bashrc
```

### 修改文件夹显示的颜色

```sh
# 1、拷贝/etc/DIR_COLORS文件为当前主目录的 .dir_colors
cp /etc/DIR_COLORS ~/.dir_colors
# 2、修改~/.dir_colors中DIR对应的颜色
vim ~/.dir_colors
# 第59行：DIR 01;34（01：粗体，34：蓝色）
# 修改为：DIR 01;33（01：粗体，33：黄色）
```

```
解释
1、文件类型
   1）直接用，有以下几种：
        no 　　　 NORMAL, NORM 全局默认
        fi　　　　FILE 普通文件
        di 　　　 DIR 目录
        ln　　　　SYMLINK, LINK, LNK 链接
        pi　　　　FIFO, PIPE 管道
        do　　　　DOOR Door
        bd　　　　BLOCK, BLK 块设备
        cd　　　　CHAR, CHR 字符设备
        or　　　　ORPHAN 目标不存在到符号链接
        so　　　　SOCK 套接字Socket
        su　　　　SETUID 属主setuid有效的文件
        sg　　　　SETGID 属组setuid有效到文件
        tw　　　　STICKY_OTHER_WRITABLE Directory that is sticky and other-writable ( t,o w)
        ow　　　　OTHER_WRITABLE Directory that is other-writable (o w) and not sticky
        st　　　　STICKY Directory with the sticky bit set ( t) and not other-writable
        ex　　　　EXEC Executable file (i.e. has ‘x’ set in permissions)
        mi　　　　MISSING Non-existent file pointed to by a symbolic link (visible when you type ls -l)
        lc　　　　LEFTCODE, LEFT Opening terminal code
        rc 　　　 RIGHTCODE, RIGHT Closing terminal code
        ec　　　　ENDCODE, END Non-filename text    
    2）扩展名通过“.”加上扩展名
    　 *.extension Every file using this extension e.g. *.jpg
2、效果的具体代码如下
    * 效果列表：
          00 　　　　默认
          01 　　　　加粗
          04 　　　　下划线
          05 　　　　闪烁
          07 　　　　反显
          08 　　　　隐藏
    * 颜色列表：
          31～37　　　　分别表示前景色为红、绿、橙、蓝、紫、青、灰
          90～97　　　　分别表示前景色为深灰、淡红、淡绿、黄色、淡蓝、淡紫、青绿、白色
          40～47　　　　分别表示背景色为黑、红、绿、橙、蓝、紫、青、灰
          100～106　　　分别表示背景色为深灰、淡红、淡绿、黄色、淡蓝、淡紫、青绿
```
