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

|命令|作用|选项|
|---|---|---|
|ls|显示当前目录的内容|-l：以长模式输出(更详细的内容)<br />-a：显示隐藏文件文件路径。可以是多个，以空格分隔|
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
|ifconfig|显示网络信息(Linux/Unix)||
|ping|测试与目标主机的连通性||
|file|确定文件类型||

ipconfig(windows)

```sh
# 显示主机的所有信息
ipconfig /all
# 查看主机中存储的DNS信息
ipconfig /displaydns
# 清除缓存
ipconfig /flushdns
```





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

# 压缩zip文件
zip -q -r 压缩包名.zip 目录/文件名
# 解压zip文件
unzip 压缩包名.zip
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



# Linux Command Line

## shell 相关

shell 就是一个程序，它接受从键盘输入的命令，然后把命令传递给操作系统去执行。

bash （Bourne Again SHell） 是 shell 的一种。bash 是 sh 的增强版，sh 是最初 Unix 的 shell 程序。

简单命令

|命令|说明|
|--|--|
|date|显示当前时间和日期|
|cal|显示当前月份的日历|
|df -h|硬盘空闲空间|
|free|内存空闲空间|
|exit|关闭终端session|

### 查看文件系统

文件名和命令名是大小写敏感的。

|命令|参数or选项|说明|
|---|--------|---|
|pwd||打印当前工作目录 print working directory|
|cd|pathname|改变目录 change directory to pathname|
||cd|切换到home directory|
||cd -|切换到之前的目录|
||cd ~username|切换到username用户的home directory|
|ls|pathname|列出pathname目录的内容 list directory|
||-a|列出所有文件，包括隐藏文件(名字以.开头的文件)|
||pathname1 pathname2|列出多个目录下的文件|
||-l|使显示内容为long format，显示文件的更多属性|
||-i|显示文件的索引节点号|
||-t|以修改时间排序|
||--reverse|反向排序|
|file|filename|查看文件类型|
|less||查看文件内容|

```sh
# 格式  command -options arguments

# options 有2种格式
# 1. 短格式：-  单个字符，如ls -l。多个option可以合并，如
ls -la
# 2. 长格式：-- 单词，如
ls -lt --reverse。
```

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

#### less

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

cp命令

|选项|意义|
|---|----|
|-a, --archive|复制文件和目录，以及它们的属性，包括所有权和权限。通常，复本具有用户所操作文件的默认属性。|
|-i, --interactive|在重写已存在文件之前，提示用户确认。如果这个选项不指 定，cp 命令会默认重写文件。|
|-r, --recursive|递归地复制目录及目录中的内容。当复制目录时，需要这个 选项(或者 -a 选项)。|
|-u, --update|当把文件从一个目录复制到另一个目录时，仅复制目标目录中不存在的文件，或者是文件内容新于目标目录中已经存在的文件。|
|-v, --verbose|显示翔实的命令操作信息|

```sh
cp file1 file2
cp -i file1 file2
cp file1 file2 dir1
cp dir1/* dir2
cp -r dir1 dir2

mv file1 file2
mv -i file1 file2
mv file1 file2 dir1
mv dir1 dir2 # 移动目录 dir1(及它的内容)到目录 dir2

rm file1
rm -i file1
rm -r file1 dir1
rm -rf file1 dir1 # -f, --force 忽视不存在的文件，不显示提示信息。
# 删除前，可以先用ls查看要删除的内容，再使用rm删除

# 硬链接
ln file link
# 软链接
ln -s item link
# 修改软链接
ln –snf [新的源文件或目录] [软链接文件]
# 删除软链接
rm –rf ./软链接名称

ln fun fun-hard
ln fun dir1/fun-hard
ln -s fun fun-sym
ln -s ../fun dir1/fun-sym
ln -s /home/me/playground/fun dir1/fun-sym
ln -s dir1 dir1-sym
```

硬链接：一个文件的所有硬链接被删除，则这个文件被删除。硬链接不能跨物理设备，不能关联目录。
软链接：类似Windows的快捷方式，文件被删除，但快捷方式还在，只是指向的内容不存在了。

通配符

|通配符|说明|
|----|----|
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
|type|command|说明怎样解释一个命令名|
|which|command|显示会执行哪个可执行程序|
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

```sh
type ls
which cp
help cd # 适用于 shell 内部命令
mkdir --help # 适用于可执行程序
man ls
alias foo='cd /usr; ls; cd -'
unalias foo # 删除别名
```

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
|grep|pattern [file...]|打印文件中符合pattern的行|
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

wc ls-output.txt
# 行 字数 字节数

ls /bin /usr/bin | sort | uniq | grep zip

ls /usr/bin | tee ls.txt | grep zip # ls.txt 是 ls 出的内容
```

## 从 shell 眼中看世界

当回车键被按下时，shell 在命令被执行前在命令行上自动展开任何符合条件的字符。

如 * 被展开成当前目录下的文件名。 echo 能看到展开效果。

- 字符展开
- 路径展开
- 波浪线展开
- 算术表达式展开
- 花括号展开
- 参数展开
- 命令替换

```sh
# 显示目录
echo *
echo D*
echo *s
ehco [[:upper:]]*
# 显示用户的主目录
echo ~
ecoh ~foo
# 计算 $((expression))
echo $((2+2))
echo $(((5**2) * 3)) # ** 取幂
# 大括号brace expansion
echo Num{1..3} # Num1 Num2 Num3
echo {01..03} # 01 02 03
echo a{A{1,2},B{3,4}}b # aA1b aA2b aB3b aB4b
# 打印参数
echo $USER
# 当前可用参数
printenv | less
# 命令替换
echo $(ls)
ls -l $(which cp) # -rwxr-xr-x 1 root root 71516 2007-12-05 08:58 /bin/cp
file $(ls /usr/bin/* | grep zip)

# 禁止展开
## 双引号 除 $ \ ` 外的字符，失去特殊含义
ls -l "two words.txt"
echo "$USER $((2+2)) $(cal)" # 可执行
## 单引号 禁止所有展开
echo 'text ~/*.txt {a,b} $(echo foo) $((2+2)) $USER' # text ~/*.txt  {a,b} $(echo foo) $((2+2)) $USER
## 转义字符 `\`
echo bad\&fimename # bad&filename
echo "\$5.00" # $5.00
# -e选项 使用转义序列
sleep 10; echo -e "Time's up\a"
```

### 其他命令

|命令|参数or选项|说明|
|---|---------|---|
|clear||清屏|
|history||显示历史列表|

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

- 第一个字母表示文件类型。-表示文件，d表示目录，l表示链接，c字符设备文件，b块设备文件（硬盘）。
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

sudo 不会重新启动一个 shell，也不会加载 另一个用户的 shell 运行环境

```sh
# 切换到root用户
su -
# 将命令传递到新shell运行
su -c 'ls -l /root/*'
# 查看sudo能运行的命令
sudo -l
# 更改文件所有者和用户组 chown 所有者:用户组
chown bob
chown bob:users
chown :admins
chown bob:  # 文件所有者改为用户 bob，文件用户组改为，用户 bob 登 录系统时，所属的用户组。
# 更改用户密码
passwd [user]
```

## 进程

| 状态 | 意义 |
|-----|-----|
|R |运行。这意味着，进程正在运行或准备运行。|
|S |正在睡眠。进程没有运行，而是，正在等待一个事件，比如 说，一个按键或者网络数据包。|
|D |不可中断睡眠。进程正在等待 I/O，比方说，一个磁盘驱动 器的 I/O。|
|T |已停止. 已经指示进程停止运行。稍后介绍更多。|
|Z |一个死进程或“僵尸”进程。这是一个已经终止的子进程， 但是它的父进程还没有清空它。(父进程没有把子进程从进程表中删除)|
|< |一个高优先级进程。这可能会授予一个进程更多重要的资源，给它更多的 CPU 时间。进程的这种属性叫做 niceness。具有高优先级的进程据说是不好的(less nice)，因为它占用了比较多的 CPU 时间，这样就给其它进程留下很少时间。|
|N |低优先级进程。一个低优先级进程(一个“好”进程)只有 当其它高优先级进程执行之后，才会得到处理器时间。|


```sh
# 列出当前终端的进程 TTY 是 “Teletype” 的简写，是指进程的控制终端。TIME 字段表示进程所消耗的 CPU 时间数量。
ps
ps x  # 所有进程
ps aux # 所有进程的详细信息
```

标题 意思
USER 用户 ID. 进程的所有者。
%CPU 以百分比表示的 CPU 使用率
%MEM 以百分比表示的内存使用率
VSZ 虚拟内存大小
RSS 进程占用的物理内存的大小，以千字节为单位。
START 进程运行的起始时间。若超过 24 小时，则用天表示。

```sh
# 连续显示系统进程更新的信息(默认情况下，每三分钟更新一次)
top
```

|行号 |字段 |意义|
|1 |top|程序名。|
||14:59:20|当前时间。|
||up 6:30|这是正常运行时间。它是计算机从上次启动到现在所 运行的时间。在这个例子里，系统已经运行了六个半 小时。|
||2 users|有两个用户登录系统。|
||load average:|加载平均值是指，等待运行的进程数目，也就是说， 处于运行状态的进程个数，这些进程共享 CPU。展示 了三个数值，每个数值对应不同的时间周期。第一个 是最后 60 秒的平均值，下一个是前 5 分钟的平均值， 最后一个是前 15 分钟的平均值。若平均值低于 1.0， 则指示计算机工作不忙碌。|
|2 |Tasks:|总结了进程数目和各种进程状态。|
|3 |Cpu(s):|这一行描述了 CPU 正在执行的进程的特性。|
||0.7%us|0.7% of the CPU is being used for user processes. 这 意味着进程在内核之外。|
||1.0%sy|1.0% 的 CPU 时间被用于系统(内核)进程。|
||0.0%ni|0.0% 的 CPU 时间被用于”nice”(低优先级)进程。|
||98.3%id|98.3% 的 CPU 时间是空闲的。|
||0.0%wa|0.0% 的 CPU 时间来等待 I/O。|
|4 |Mem:|展示物理内存的使用情况。|
|5 |Swap:|展示交换分区(虚拟内存)的使用情况。|

top 程序接受一系列从键盘输入的命令。h，显示程序的帮助 屏幕，q，退出 top 程序。

在一个终端中，输入 Ctrl-c，中断一个程序。Ctrl-z，可以停止一个前台进程。

在使用 Ctrl-c 的情况下，会发送一个叫做 INT(中断)的信 号;当使用 Ctrl-z 时，则发送一个叫做 TSTP(终端停止)的信号。程序，反过来，倾听信号 的到来，当程序接到信号之后，则做出响应。

```sh
# 后台运行，显示的数字为pid
xlogo &
# 列出从终端中启动的任务
jobs
# 让一个进程返回前台执行
fg %1
# 把程序移到后台。
bg %1
# 指定我们想要终止的进程 PID。  kill [-signal] PID...
kill 28401
kill -l # 完整信号表
# 给多个进程发送信号
killall [-u user] [-signal] name...
```

kill 命令的信号

|编号 |名字 |含义|
|----|----|----|
|1 |HUP|挂起。这是美好往昔的痕迹，那时候终端机通过电话 线和调制解调器连接到远端的计算机。这个信号被用 来告诉程序，控制的终端机已经“挂起”。通过关闭 一个终端会话，可以说明这个信号的作用。发送这个 信号到终端机上的前台程序，程序会终止。许多守护 进程也使用这个信号，来重新初始化。这意味着，当 发送这个信号到一个守护进程后，这个进程会重新启 动，并且重新读取它的配置文件。Apache 网络服务 器守护进程就是一个例子。|
|2 |INT|中断。实现和 Ctrl-c 一样的功能，由终端发送。通常， 它会终止一个程序。|
|9 |KILL |杀死。这个信号很特别。鉴于进程可能会选择不同的 方式，来处理发送给它的信号，其中也包含忽略信号， 这样呢，从不发送 Kill 信号到目标进程。而是内核立 即终止这个进程。当一个进程以这种方式终止的时候， 它没有机会去做些“清理”工作，或者是保存劳动成 果。因为这个原因，把 KILL 信号看作杀手锏，当其 它终止信号失败后，再使用它。|
|15 |TERM |终止。这是 kill 命令发送的默认信号。如果程序仍然 “活着”，可以接受信号，那么这个信号终止。|
|18 |CONT |继续。在停止一段时间后，进程恢复运行。|
|19 |STOP |停止。这个信号导致进程停止运行，而没有终止。像 KILL 信号，它不被发送到目标进程，因此它不能被忽略。|


命令名  命令描述
pstree  输出一个树型结构的进程列表，这个列表展示了进程间 父/子关系。
vmstat  输出一个系统资源使用快照，包括内存，交换分区和磁盘 I/O。为了看到连续的显示结果，则在命令名后加上延时的 时间(以秒为单位)。例如，“vmstat 5”。终止输出，按下 Ctrl-c 组合键。
xload 一个图形界面程序，可以画出系统负载的图形。
tload 与 xload 程序相似，但是在终端中画出图形。使用 Ctrl-c， 来终止输出。

## shell 环境

- printenv 打印部分或所有的环境变量
- set 设置 shell 选项
- export 导出环境变量，让随后执行的程序知道。
- alias 创建命令别名

shell 在环境中存储了两种基本类型的数据：环境变量和 shell 变量。

```sh
printenv | less
printenv USER

set | less
echo $HOME

alias
```

登录 shell 会读取一个或多个启动文件，正如表所示:
| 文件 | 内容 |
|---|---|
| /etc/profile |应用于所有用户的全局配置脚本。|
| ̃/.bash_profile |用户私人的启动文件。可以用来扩展或重写全局配置脚本中的设置。|
| ̃/.bash_login |如果文件  ̃/.bash profile 没有找到，bash 会尝试读取这个脚本。|
| ̃/.profile |如果文件  ̃/.bash profile 或文件  ̃/.bash login 都没有找到， bash 会试图读取这个文件。这是基于 Debian 发行版的默认 设置，比方说 Ubuntu。|

非登录 shell 会话(GUI建的会话)会读取以下启动文件，并继承它们父进程的环境设置

|文件|内容|
|---|---|
| /etc/bash.bashrc |应用于所有用户的全局配置文件。|
| ̃/.bashrc |用户私有的启动文件。可以用来扩展或重写全局配置脚本中的设置。|

```sh
# 告诉 shell 让这个 shell 的子进程可以使用 PATH 变量的内容
export PATH
# 强迫 bash 重新读取修改过的.bashrc 文件
source .bashrc
```

### vi或vim

|分组|命令|作用|
|--|--|--|
|插入|i|插入到光标位置|
||A|在当前行尾插入|
||o|当前行的下方打开一行。|
||O|当前行的上方打开一行。|
|复制或粘贴|yy|复制单行|
||nyy|复制多行|
||p|粘贴|
||P|粘贴到上一行|
|定位|gg|到文本的第一行|
||shift+gg|到文本最后一行|
||shift+4|光标定位到行尾|
||shift+6|光标定位到行首|
|删除|dd|删除光标所在行|
||ndd|删除n行|
||x|删除光标处的字符|
|退出|:q|退出（未修改内容时）|
||:q!|退出不保存|
||:w|保持不退出|
||:wq|保持退出|
|查询|/查询的字符串|在非编辑模式下，输入/再输入查询的字符串|
||n|查询下一个命中的字符串|
||N|查询上一个命中的字符串|
|查询一行|fx|查找当前行中的x字符，用;重复查找|
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
|连接行|J|将本行和下一行连成一行，用空格隔开|

解释如下：

:w – Write a file.

!sudo – Call shell sudo command.

tee – The output of write (vim :w) command redirected using tee.

% – is nothing but current file name

| 按键 | 移动光标 |
|----|----|
|l or 右箭头|向右移动一个字符|
|h or 左箭头|向左移动一个字符|
|j or 下箭头|向下移动一行|
|k or 上箭头|向上移动一行|
|0 (零按键)|移动到当前行的行首。|
|ˆ|移动到当前行的第一个非空字符。|
|$|移动到当前行的末尾。|
|w|移动到下一个单词或标点符号的开头。|
|W|移动到下一个单词的开头，忽略标点符号。|
|b|移动到上一个单词或标点符号的开头。|
|B|移动到上一个单词的开头，忽略标点符号。|
|Ctrl-f or Page Down|向下翻一页|
|Ctrl-b or Page Up|向上翻一页|
|numberG|移动到第 number 行。例如，1G 移动到文件的 第一行。|
|G|移动到文件末尾。|

删除操作

命令 删除的文本
x 当前字符
3x 当前字符及其后的两个字符。
dd 当前行。
5dd 当前行及随后的四行文本。
dW 从光标位置开始到下一个单词的开头。
d$ 从光标位置开始到当前行的行尾。
d0 从光标位置开始到当前行的行首。
dˆ 从光标位置开始到文本行的第一个非空字符。
dG 从当前行到文件的末尾。
d20G 从当前行到文件的第 20 行。

复制命令


命令 复制的内容
yy 当前行。
5yy 当前行及随后的四行文本。
yW 从当前光标位置到下一个单词的开头。
y$ 从当前光标位置到当前行的末尾。
y0 从当前光标位置到行首。
yˆ 从当前光标位置到文本行的第一个非空字符。
yG 从当前行到文件末尾。
y20G 从当前行到文件的第 20 行。


## 查找文件

find

```sh
# 输出主目录及以下的文件
find ~
# 增加测试条件：文件类型
find ~ -type f
# 增加测试条件：文件名
find ~ -type f -name "*.JPG" -size +1M | wc -l
# 逻辑操作符
find ~ \( -type f -not -perm 0600 \) -or \( -type d -not -perm 0700 \)

# 用户定义的行为  -exec command {} ;
find ~ -type f -name 'foo*' -exec ls -l '{}' ';'
find ~ -type f -name 'foo*' -exec ls -l '{}' + # 将查找出来的结果，合并成命令行的参数
```

find 命令支持的普通文件类型测试条件:
文件类型 描述
b 块设备文件
c 字符设备文件
d 目录
f 普通文件
l 符号链接

下面的字符可以被用来指定测量单位:
字符 单位
b 512 个字节块。如果没有指定单位，则这是默认值。
c 字节
w 两个字节的字
k 千字节 (1024 个字节单位)
M 兆字节 (1048576 个字节单位)
G 千兆字节 (1073741824 个字节单位)


测试条件	描述
 -cmin n	匹配的文件和目录的内容或属性最后修改时间正好在 n 分 钟之前。指定少于 n 分钟之前，使用 -n，指定多于 n 分钟 之前，使用 +n。
 -cnewer file	匹配的文件和目录的内容或属性最后修改时间早于那些文 件。
 -ctime n	匹配的文件和目录的内容和属性最后修改时间在 n\*24 小 时之前。
 -empty	匹配空文件和目录。
 -group name	匹配的文件和目录属于一个组。组可以用组名或组 ID 来表 示。
 -iname pattern	就像 -name 测试条件，但是大小写敏感。
 -inum n	匹配的文件的 inode 号是 n。这对于找到某个特殊 inode 的 所有硬链接很有帮助。
 -mmin n	匹配的文件或目录的内容被修改于 n 分钟之前。
 -mtime n	匹配的文件或目录的内容被修改于 n\*24 小时之前。
 -name pattern	用指定的通配符模式匹配的文件和目录。
 -newer file	匹配的文件和目录的内容早于指定的文件。当编写 shell 脚 本，做文件备份时，非常有帮助。每次你制作一个备份，更 新文件(比如说日志)，然后使用 find 命令来决定自从上次 更新，哪一个文件已经更改了。
 -nouser	匹配的文件和目录不属于一个有效用户。这可以用来查找属于删除帐户的文件或监测攻击行为。
 -nogroup	匹配的文件和目录不属于一个有效的组。
 -perm mode	匹配的文件和目录的权限已经设置为指定的 mode。mode 可以用八进制或符号表示法。
 -samefile name	相似于 -inum 测试条件。匹配和文件 name 享有同样 inode 号的文件。
 -size n	匹配的文件大小为 n。
 -type c	匹配的文件类型是 c。
 -user name	匹配的文件或目录属于某个用户。这个用户可以通过用户名 或用户 ID 来表示。


操作符列表:
操作符 描述
-and 匹配如果操作符两边的测试条件都是真。可以简写为 -a。 注意若没有使用操作符，则默认使用 -and。
-or 匹配若操作符两边的任一个测试条件为真。可以简写为 -o。
-not 匹配若操作符后面的测试条件是真。可以简写为一个感叹号 (!)。
() 把测试条件和操作符组合起来形成更大的表达式。这用来 控制逻辑计算的优先级。默认情况下，find 命令按照从左到 右的顺序计算。经常有必要重写默认的求值顺序，以得到期 望的结果。即使没有必要，有时候包括组合起来的字符，对 提高命令的可读性是很有帮助的。注意因为圆括号字符对 于 shell 来说有特殊含义，所以在命令行中使用它们的时候， 它们必须用引号引起来，才能作为实参传递给 find 命令。 通常反斜杠字符被用来转义圆括号字符。

### xargs
xargs 命令会执行一个有趣的函数。它从标准输入接受输入，并把输入转换为一个特定命令的参数列表。

```sh
find ~ -type f -name 'foo\*' -print | xargs ls -l
```

## 归档和备份

```sh
gzip foo.txt
gzip -d foo.txt.gz
# 测试压缩包的完整性
gzip -tv foo.txt.gz
# 查看压缩内容
gunzip -c foo.txt | less


bzip2 foo.txt
bunzip2 foo.txt.bz2
```

gzip 命令有许多选项。这里列出了一些:
选项 说明
-c 把输出写入到标准输出，并且保留原始文件。也有可能用 --stdout 和 --to-stdout 选项来指定。
-d 解压缩。正如 gunzip 命令一样。也可以用 --decompress 或 者 --uncompress 选项来指定.
-f 强制压缩，即使原始文件的压缩文件已经存在了，也要执 行。也可以用 --force 选项来指定。
-h 显示用法信息。也可用 --help 选项来指定。
-l 列出每个被压缩文件的压缩数据。也可用 --list 选项。
-r 若命令的一个或多个参数是目录，则递归地压缩目录中的文 件。也可用 --recursive 选项来指定。
-t 测试压缩文件的完整性。也可用 --test 选项来指定。
-v 显示压缩过程中的信息。也可用 --verbose 选项来指定。
-number 设置压缩指数。number 是一个在 1(最快，最小压缩)到 9(最慢，最大压缩)之间的整数。数值 1 和 9 也可以各自
用 --fast 和 --best 选项来表示。默认值是整数 6。


归档

```sh
# 归档
tar cf playground.tar playground
# 归档（绝对路径） 当创建归档文件的时候，tar 命令会简单地删除路径名开头的斜杠
tar cf playground2.tar ~/playground
# 列出内容
tar tvf playground.tar
# 抽取压缩包内容到当前目录
tar xf ../playground.tar
# 追加添加到压缩包
find playground -name 'file-A' -exec tar rf playground.tar '{}' '+'
# -T == --files-from; - 表示标准输入/输出
find playground -name 'file-A' | tar cf - --files-from=- | gzip > playground.tgz
# tar 命令， gzip 和 bzip2 压缩两者都直接支持，各自使用 z 和 j 选项
find playground -name 'file-A' | tar czf playground.tgz -T -
# 远程
ssh remote-sys 'tar cf - Documents' | tar xf -
```

模式 说明
c 为文件和/或目录列表创建归档文件。
x 抽取归档文件。
r 追加具体的路径到归档文件的末尾。
t 列出归档文件的内容。


同步

```sh
# 同步 playground 到 foo 目录。 -a 选项(递归和保护文件属性)和 -v 选项(冗余输出)
rsync -av playground foo
# --delete 这个选项，来删除可能在备份设备中已经存在但却不再存在于源 设备中的文件
rsync -av --delete /etc /home /usr/local /media/BigDisk/backup
# 在网络间同步
rsync -av --delete --rsh=ssh /etc /home /usr/local remote-sys:/backup
rsync -av --delete rsync://rsync.gtlib.gatech.edu/fedora-linux-core/development/i386/os fedora-devel
```

## grep 中使用正则表达式

grep 选项列表:
选项 描述
-i 忽略大小写。不会区分大小写字符。也可用 --ignore-case 来 指定。
-v 不匹配。通常，grep 程序会打印包含匹配项的文本行。这 个选项导致 grep 程序只会不包含匹配项的文本行。也可用 --invert-match 来指定。
-c 打印匹配的数量(或者是不匹配的数目，若指定了 -v 选 项)，而不是文本行本身。也可用 --count 选项来指定。
-l 打印包含匹配项的文件名，而不是文本行本身，也可用 --files-with-matches 选项来指定。
-L 相似于 -l 选项，但是只是打印不包含匹配项的文件名。也 可用 --files-without-match 来指定。
-n 在每个匹配行之前打印出其位于文件中的相应行号。也可用 --line-number 选项来指定。
-h 应用于多文件搜索，不输出文件名。也可用 --no-filename 选项来指定。

```sh
# ^$.[]{}-?*+()|\
grep -h '.zip' dirlist*.txt
grep -h '^zip' dirlist*.txt
grep -h '^[A-Z]' dirlist*.txt

# 扩展 正则表达式
echo "AAA" | grep -E 'AAA|BBB'
grep -Eh '^(bz|gz|zip)' dirlist*.txt
```

POSIX 标准的字符集

字符集	说明
[:alnum:]	字母数字字符。在 ASCII 中，等价于:[A-Za-z0-9]
[:word:]	与 [:alnum:] 相同, 但增加了下划线字符。
[:alpha:]	字母字符。在 ASCII 中，等价于:[A-Za-z]
[:blank:]	包含空格和 tab 字符。
[:cntrl:]	ASCII 的控制码。包含了 0 到 31，和 127 的 ASCII 字符。
[:digit:]	数字 0 到 9
[:graph:]	可视字符。在 ASCII 中，它包含 33 到 126 的字符。
[:lower:]	小写字母。
[:punct:]	标点符号字符。在 ASCII 中，等价于:
[:print:]	可打印的字符。在 [:graph:] 中的所有字符，再加上空格字 符。
[:space:]	空白字符，包括空格，tab，回车，换行，vertical tab, 和 form feed. 在 ASCII 中，等价于:[ \t\r\n\v\f]
[:upper:]	大写字母。
[:xdigit:]	用来表示十六进制数字的字符。在 ASCII 中，等价于: [0-9A-Fa-f]

{ } - 匹配一个元素特定的次数

限定符 意思
 n 匹配前面的元素，如果它确切地出现了 n 次。
n,m 匹配前面的元素，如果它至少出现了 n 次，但是不多于 m 次。
n, 匹配前面的元素，如果它出现了 n 次或多于 n 次。
,m 匹配前面的元素，如果它出现的次数不多于 m 次。

## 文本处理

• cat –连接文件并且打印到标准输出 
• sort –给文本行排序
• uniq –报告或者省略重复行
• cut –从每行中删除文本区域
• paste –合并文件文本行
• join –基于某个共享字段来联合两个文件的文本行 
• comm –逐行比较两个有序的文件
• diff –逐行比较文件
• patch –给原始文件打补丁
• tr –翻译或删除字符
• sed –用于筛选和转换文本的流编辑器
• aspell –交互式拼写检查器

```sh
# 显示特殊字符
cat -A foo.txt
# -n，给文本行添加行号；-s，禁止输出多个空白行。
cat -ns foo.txt


# 排序
sort file1.txt file2.txt file3.txt > final_sorted_list.txt
du -s /usr/share/* | sort -nr | head
ls -l /usr/bin | sort -nr -k 5 | head
# 对第一列排序 对第二列使用数值排序
sort --key=1,1 --key=2n distros.txt
# 对第三列，第七个字符开始，去掉开头空格，按数值逆序排序。之后...
sort -k 3.7nbr -k 3.1nbr -k 3.4nbr distros.txt
# 用 : 作为分隔符，以第七列排序
sort -t ':' -k 7 /etc/passwd | head
# 去除重复项
sort -u xx.txt
sort foo.txt | uniq

# uniq 只能对有序文件去重
```

sort 程序有几个有趣的选项。这里只是一部分列表:

选项 长选项	描述
 -b --ignore-leading-blanks	默认情况下，对整行进行排序，从每行的第一 个字符开始。这个选项导致 sort 程序忽略每行 开头的空格，从第一个非空白字符开始排序。
 -f --ignore-case	让排序不区分大小写。
 -n --numeric-sort	基于字符串的长度来排序。使用此选项允许根 据数字值执行排序，而不是字母值。
 -r --reverse	按相反顺序排序。结果按照降序排列，而不是 升序。
 -k --key=field1[,field2]	对从 field1 到 field2 之间的字符排序，而不是 整个文本行。看下面的讨论。
 -m --merge	把每个参数看作是一个预先排好序的文件。把 多个文件合并成一个排好序的文件，而没有执 行额外的排序。
 -o --output=file	把排好序的输出结果发送到文件，而不是标准 输出。
 -t --field-separator=char	定义域分隔字符。默认情况下，域由空格或制 表符分隔。
         



## 编译程序

用高级语言编写的程序，经过另一个称为编译器的程序的处理，会转换成机器语言。
一些编译器把高级指令翻译成汇编语言，然后使用一个汇编器完成翻译成机器语言的最后阶段。

一个叫做链接器的程序用来在编译器的输出结果和要编译的程序所需的库之间建立连接。这个过程的最终结果是一个可执行程序文件，准备使用。

有些程序比如 shell 脚本就不需要编译。它们直接执行。这些程序是用所谓的脚本或解释型语言编写的。一个解释器输入程序文件，读取并执行程序中包含的每一条指令。

```sh
# 构建程序
# 这个 configure 程序是一个 shell 脚本，由源码树提供。它的工作是分析程序建立环境，如检查是否安装了必要的外部工具和组件。
./configure
# Makefile 是一个配置文件，指示 make 程序究竟如何构建程序。没有它，make 程序就不能运行。Makefile 是一个普通文本文件。
make
```

## Shell 脚本

Shell 有些独特，因为它不仅是一个功能强大的命令行接口, 也是一个脚本语言解释器。

当没有指定可执行文件明确的路径名时，shell 会 自动地搜索某些目录，来查找此可执行文件。

```sh
# shebang (即#!符号) 被用来告诉操作系统将执行此脚本所用的解释器的名字
#!/bin/bash
# This is our first script.
echo 'Hello World!'
```

对于脚本文件，有两个常见的权限设置;权限为 755 的脚本，则每个人都能执行，和权限 为 700 的脚本，只有文件所有者能够执行。注意为了能够执行脚本，脚本必须是可读的。

为了能够运行此脚本，我们必须指定脚本文件明确的路径。

```sh
# 默认在以下目录搜索可执行脚本，脚本放在这些目录下，可以不指定明确路径
echo $PATH
# （.)命令是 source 命令的同义词，一个 shell 内部命令，用来读取一个指定的 shell 命令文件，并把它看作是从键盘中输入的一样
. .bashrc
```

如果我们编写了一个脚本，系统中的每 个用户都可以使用它，那么这个脚本的传统位置是/usr/local/bin。

在赋值过程中，变量名，等号和变量值之间必须没有空格

```sh
# 变量
a=z
b="a string"
c="a string and $b"
# Assign the string "z" to variable a.
# Embedded spaces must be within quotes.
# Other expansions such as variables can be
# expanded into the assignment.
d=$(ls -l foo.txt)
e=$((5 * 7))
f="\t\ta string\n"
# Results of a command.
# Arithmetic expansion.
# Escape sequences such as tabs and newlines. be
# expanded into the assignment.
# 在同一行中对多个变量赋值
a=5 b="a string"

# 添加花括号，shell 不再把末尾的 1 解释为变量名的一部分
mv $filename ${filename}1

# 文本输出的一种方式
command << token
text
token

cat << _EOF_
这里是文本，在这里，单引号和双引号会失去它们在 shell 中的特殊含义，会当成普通的符号。
_EOF_

cat <<- _EOF_
	会忽略开头的tab
	可以缩进
_EOF_
```


## Shell 语法

```sh
# case
case word in
    [pattern [| pattern]...) commands ;;]...
esac
# 模式以一个“)”为终止符

read -p "Enter selection [0-3] > "
case $REPLY in
    0)  echo "Program terminated."
        exit
        ;;
    1)  echo "Hostname: $HOSTNAME"
		uptime
		;; 
	2) df -h
        ;;
    *)  echo "Invalid entry" >&2
		exit 1
		;;
esac


read -p "enter word > "
case $REPLY in
    [[:alpha:]])        echo "is a single alphabetic character." ;;
    [ABC][0-9])         echo "is A, B, or C followed by a digit." ;;
    ???)                echo "is three characters long." ;;
    *.txt)              echo "is a word ending in '.txt'" ;;
    *)                  echo "is something else." ;;
esac

# 添加的“;;&”的语法允许 case 语句继续执行下一条测试，而不是简单地终止运行。


# 位置参数
# shell 提供了一个称为位置参数的变量集合，这个集合包含了命令行中所有独立的单词。这 些变量按照从 0 到 9 给予命名，超过9用{}来引用
# $#，可以得到命令行参数个数的变量
[me@linuxbox ~]$ ./posit-param a b c d
$# = 4
$0 = /home/me/bin/posit-param
$1 = a
$2 = b
$3 = c
$4 = d
$5 =
$6 =
$7 =
$8 =
$9 =
${10} =
${55} =
# 执行一次 shift 命令，就会导致所有的位置参数“向下移动一个位置”
count=1
while [[ $# -gt 0 ]]; do
    echo "Argument $count = $1"
    count=$((count + 1))
    shift
done

# 引用所有参数，如 myParam "a" "a b c"
# "$@" = ["a", "a b c"]
# $@ = ["a", "a", "b", "c"]
# "$*" = ["a a b c"]
# $* = ["a", "a", "b", "c"]


# for 循环
for variable [in words]; do
    commands
done

for i in A B C D; do echo $i; done
for i in {A..D}; do echo $i; done
for i in distros*.txt; do echo $i; done

# 如果省略掉 for 命令的可选项 words 部分，for 命令会默认处理位置参数

for (( expression1; expression2; expression3 )); do
    commands
done
# 等价于
(( expression1 ))
while (( expression2 )); do
commands
    (( expression3 ))
done
```

basename 命令清除一个路径名的开头部分，只留下一个文件的基本名称


## 参数展开

```sh
# 基本参数（变量，位置参数
$a # 等价于
${a}
$1
${11}

# 空变量
# :- 后是默认值，parameter值为空时，使用默认值
foo=
${parameter:-word}
echo ${foo:-"substitute value if unset"} # substitute value if unset
# parameter值为空时，退出，word的内容输出到标准错误
${parameter:?word}
# 若 parameter 没有设置或为空，展开结果为空。若 parameter 不为空，展开结果是 word 的 值会替换掉 parameter 的值;然而，parameter 的值不会改变。
${parameter:+word}
foo=
echo ${foo:+"substitute value if set"} # 结果是空
foo=bar
echo ${foo:+"substitute value if set"} # substitute value if set

# 这种展开会返回以 prefix 开头的已有变量名
${!prefix*}
${!prefix@}

echo ${!BASH*}
# BASH BASH_ARGC BASH_ARGV BASH_COMMAND BASH_COMPLETION
# BASH_COMPLETION_DIR BASH_LINENO BASH_SOURCE BASH_SUBSHELL
# BASH_VERSINFO BASH_VERSION

# 字符串展开
# 字符串parameter的长度
${#parameter}
# 返回 parameter 从 offset 开始到结尾的子串。若 offset 的值为负数，则认为 offset 值是从字符串的末尾开始算起
${parameter:offset}
# 返回 parameter 从 offset 开始， length 长度的子串
${parameter:offset:length}
# 位置参数，从offset开始，length个位置参数
${@:offset:length}

[me@linuxbox ~]$ foo="This string is long."
[me@linuxbox ~]$ echo ${foo:5}
string is long.
[me@linuxbox ~]$ echo ${foo:5:6}
string
[me@linuxbox ~]$ echo ${foo: -5}
long.
[me@linuxbox ~]$ echo ${foo: -5:2}
lo

# 从 paramter 所包含的字符串中清除开头一部分文本，# 形式清除最短的匹配结果，而该 ## 模式清除最长的匹配结果。
${parameter#pattern}
${parameter##pattern}

[me@linuxbox ~]$ foo=file.txt.zip
[me@linuxbox ~]$ echo ${foo#*.}
txt.zip
[me@linuxbox ~]$ echo ${foo##*.}
zip

# 从末尾开始，从 paramter 所包含的字符串中清除末尾一部分文本
${parameter%pattern}
${parameter%%pattern}

# 查找和替换。/string 可能会省略掉，这样会导致删除匹配的文本
${parameter/pattern/string}  # 找到的第一个被替换
${parameter//pattern/string} # 所有被替换
${parameter/#pattern/string} # 要求匹配项出现在开头
${parameter/%pattern/string} # 要求匹配项出现在末尾

[me@linuxbox~]$ foo=JPG.JPG
[me@linuxbox ~]$ echo ${foo/JPG/jpg}
jpg.JPG
[me@linuxbox~]$ echo ${foo//JPG/jpg}
jpg.jpg
[me@linuxbox~]$ echo ${foo/#JPG/jpg}
jpg.JPG
[me@linuxbox~]$ echo ${foo/%JPG/jpg}
JPG.jpg

# declare 命令可以用来把字符串规范成大写或小写字符
declare -u upper
declare -l lower
if [[ $1 ]]; then
    upper="$1"
    lower="$1"
    echo $upper
    echo $lower
fi

# 有四个参数展开，可以执行大小写转换操作:
# 格式 结果
# \${parameter„} 把 parameter 的值全部展开成小写字母。 
# \${parameter,} 仅仅把 parameter 的第一个字符展开成小写字母。
# \${parameterˆˆ} 把 parameter 的值全部转换成大写字母。
# \${parameterˆ} 仅仅把 parameter 的第一个字符转换成大写字母(首字母 大写)。

if [[ $1 ]]; then
    echo ${1,,}
    echo ${1,}
    echo ${1^^}
    echo ${1^}
fi

# 算术求值和展开
$((expression))

# 数基
# shell 支持任意进制的整形常量。
# 表示法  描述
# number  默认情况下，没有任何表示法的数字被看做是十进制数(以 10 为底)。
# 0number  在算术表达式中，以零开头的数字被认为是八进制数。
# 0xnumber  十六进制表示法
# base#number  number 以 base 为底
[me@linuxbox ~]$ echo $((0xff))
255
[me@linuxbox ~]$ echo $((2#11111111))
255
# 当表达式用于逻辑运算时，表达式遵循算术逻辑规则;也就是，表达式的计算结果是零， 则认为假，而非零表达式认为真
```

## 数组

```sh
# 创建数组
a[1]=foo # 赋值
echo ${a[1]}  # foo
# 创建数组
declare -a a

# 赋值
arrayName=(value1 value2 ...)
days=([0]=Sun [1]=Mon [2]=Tue [3]=Wed [4]=Thu [5]=Fri [6]=Sat)
[me@linuxbox~]$ foo=(a b c d e f)
[me@linuxbox~]$ foo=A 
[me@linuxbox~]$ echo ${foo[@]} # Abcdef

# 输出
[me@linuxbox ~]$ animals=("a dog" "a cat" "a fish")
[me@linuxbox ~]$ for i in ${animals[*]}; do echo $i; done
a
dog
a
cat
a
fish
[me@linuxbox ~]$ for i in ${animals[@]}; do echo $i; done
a
dog
a
cat
a
fish
[me@linuxbox ~]$ for i in "${animals[*]}"; do echo $i; done
a dog a cat a fish
[me@linuxbox ~]$ for i in "${animals[@]}"; do echo $i; done
a dog
a cat
a fish

# 长度
[me@linuxbox ~]$ a[100]=foo
[me@linuxbox ~]$ echo ${#a[@]} # number of array elements
1
[me@linuxbox ~]$ echo ${#a[100]} # length of element 100
3

# 下标
${!array[*]}
${!array[@]}
[me@linuxbox ~]$ foo=([2]=a [4]=b [6]=c)
[me@linuxbox ~]$ for i in "${foo[@]}"; do echo $i; done
a
b
c
[me@linuxbox ~]$ for i in "${!foo[@]}"; do echo $i; done
2
4
6

# 追加
foo=(a b c)
foo+=(d e f)
echo ${foo[@]} # abcdef

# 排序
a_sorted=($(for i in "${a[@]}"; do echo $i; done | sort))

# 删除数组
unset foo
unset 'foo[2]'

# 关联数组（有点像map）
declare -A colors
colors["red"]="#ff0000"
colors["green"]="#00ff00"
colors["blue"]="#0000ff"
# 访问元素
echo ${colors["blue"]}
```

## 不常用命令

```sh
# 组命令:
{ command1; command2; [command3; . . . ] }
# 子 shell:
(command1; command2; [command3;. . . ])

{ ls -l; echo "Listing of foo.txt"; cat foo.txt; } > output.txt
(ls -l; echo "Listing of foo.txt"; cat foo.txt) > output.txt

{ ls -l; echo "Listing of foo.txt"; cat foo.txt; } | lpr
```




```sh
ip addr # 查看虚拟机ip地址，一般10开头的是外网地址，192开头的是内网地址。
ip a # 缩写

ps -aux # 查看启动的进程

```








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

Mac 查看本机 JDK 安装目录

```sh
/usr/libexec/java_home -V
```





nslookup

nslookup用于查询DNS的记录，查询域名解析是否正常，在网络故障时用来诊断网络问题

```sh
nslookup -option1 -option2 host-to-find dns-server
```

```
nslookup domain [dns-server]
  //如果没有指定dns服务器，就采用系统默认的dns服务器。

nslookup -qt = type domain [dns-server]
type:
    A -->地址记录
    AAAA   -->地址记录
    AFSDB Andrew    -->文件系统数据库服务器记录
    ATMA -->ATM地址记录
    CNAME   -->别名记录
    HINHO  -->硬件配置记录，包括CPU、操作系统信息 
    ISDN   -->域名对应的ISDN号码
    MB   -->存放指定邮箱的服务器
    MG    -->邮件组记录
    MINFO   -->邮件组和邮箱的信息记录
    MR   -->改名的邮箱记录
    MX   -->邮件服务器记录
    NS  --> 名字服务器记录
    PTR    ->反向记录
    RP    -->负责人记录
    RT  -->路由穿透记录
    SRV    -->TCP服务器信息记录
    TXT   -->域名对应的文本信息
    X25  -->域名对应的X.25地址记录

nslookup -d [其他参数] domain [dns-server]     
//只要在查询的时候，加上-d参数，即可查询域名的缓存
```

nmap

确定哪个应用程序正在监听哪些端口



在Linux系统中，baiCtrl+c和ctrl+z都是中断命令,但是他们的作用却du不一样.

Ctrl+c是强制中断程序zhi的执行，,进程已经终dao止

Ctrl+z是将任务中止（暂停的意思）。

在这一点上，任务还没有结束，它仍然在进行中，它只是挂着。用户可以使用fg/bg操作继续前台或后台任务，fg命令重启前台中断的任务，bg命令重启后台中断的任务。

Ctrl+d 不是发送信号，而是表示一个特殊的二进制值，表示 EOF


[CentOS7.5升级gcc到8.3.0版本](https://www.cnblogs.com/NanZhiHan/p/11010130.html)|
