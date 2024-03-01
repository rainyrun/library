# Makefile

问：java的自动化编译是如何完成的？怎么知道先编译哪个，后编译哪个？

## Makefile 介绍

目标

在这个示例中，我们的工程有 8 个 C 文件，和 3 个头文件，我们要写一个 Makefile 来告诉 make 命令如何编译和链接这几个文件。我们的规则是：

1）如果这个工程没有编译过，那么我们的所有 C 文件都要编译并被链接。
2）如果这个工程的某几个 C 文件被修改，那么我们只编译被修改的 C 文件，并链接目标程。
3）如果这个工程的头文件被改变了，那么我们需要编译引用了这几个头文件的 C 文件，并链接目标程序。

### Makefile 规则

```makefile
target ... : prerequisites ...
    command
    ...
    ...
```

target 也就是一个目标文件，可以是 Object File，也可以是执行文件。还可以是一个标签（Label）

prerequisites 就是，要生成那个 target 所需要的文件或是目标。

command 也就是 make 需要执行的命令。

target 这一个或多个的目标文件依赖于 prerequisites 中的文件，其生成规则定义在 command 中。说白一点就是说，prerequisites 中如果有一个以上的文件比 target 文件要新的话，command 所定义的命令就会被执行。

makefile 文件示例

```makefile
edit : main.o kbd.o command.o display.o \
           insert.o search.o files.o utils.o
    cc -o edit main.o kbd.o command.o display.o \
                    insert.o search.o files.o utils.o
main.o : main.c defs.h
    cc -c main.c
kbd.o : kbd.c defs.h command.h
    cc -c kbd.c
command.o : command.c defs.h command.h
    cc -c command.c
display.o : display.c defs.h buffer.h
    cc -c display.c
insert.o : insert.c defs.h buffer.h
    cc -c insert.c
search.o : search.c defs.h buffer.h
    cc -c search.c
files.o : files.c defs.h buffer.h command.h
    cc -c files.c
utils.o : utils.c defs.h
    cc -c utils.c
clean :
    rm edit main.o kbd.o command.o display.o \
        insert.o search.o files.o utils.o
```

命令一定要以一个Tab键作为开头

clean不是一个文件，它只不过是一个动作名字，有点像C语言中的lable一样，其冒号后什么也没有，那么，make就不会自动去找文件的依赖性，也就不会自动执行其后所定义的命令。要执行其后的命令，就要在make命令后明显得指出这个lable的名字。

### make 是如何工作的

在默认的方式下，也就是我们只输入 make 命令。那么，

1、make 会在当前目录下找名字叫 “Makefile” 或 “makefile” 的文件。
2、如果找到，它会找文件中的第一个目标文件（target），在上面的例子中，他会找到 “edit” 这个文件，并把这个文件作为最终的目标文件。
3、如果 edit 文件不存在，或是 edit 所依赖的后面的 .o 文件的文件修改时间要比 edit这个文件新，那么，他就会执行后面所定义的命令来生成 edit 这个文件。
4、如果 edit 所依赖的.o 文件也存在，那么 make 会在当前文件中找目标为.o 文件的依赖性，如果找到则再根据那一个规则生成.o 文件。（这有点像一个堆栈的过程）
5、当然，你的 C 文件和 H 文件是存在的啦，于是 make 会生成 .o 文件，然后再用 .o 文件生命 make 的终极任务，也就是执行文件 edit 了。

### 使用变量和自动推导

```makefile
# 这是一个 makefile 注释
objects = main.o kbd.o command.o display.o \
            insert.o search.o files.o utils.o

edit : $(objects)
        cc -o edit $(objects)

main.o : defs.h
kbd.o : defs.h command.h
command.o : defs.h command.h
display.o : defs.h buffer.h
insert.o : defs.h buffer.h
search.o : defs.h buffer.h
files.o : defs.h buffer.h command.h
utils.o : defs.h

.PHONY : clean
clean :
        -rm edit $(objects)
```

只要make看到一个[.o]文件，它就会自动的把[.c]文件加在依赖关系中，如果make找到一个whatever.o，那么whatever.c，就会是whatever.o的依赖文件。并且 cc -c whatever.c 也会被推导出来

.PHONY意思表示clean是一个“伪目标”

在rm命令前面加了一个小减号的意思就是，也许某些文件出现问题，但不要管，继续做后面的事。

文件默认为 Makefile，自定义的文件名可以用以下命令执行

```sh
make -f Make.Linux
make --file Make.Linux
```

### 其他语法

```makefile
# 把别的Makefile包含进来
include <filename>;
include foo.make *.mk $(bar)
```

make 查找引入的Makefile的目录

make还会在下面的几个目录下找：

0. 当前目录
1. 如果make执行时，有“-I”或“--include-dir”参数，那么make就会在这个参数所指定的目录下去寻找。
2. 如果目录 `<prefix>;/include`（一般是：`/usr/local/bin` 或 `/usr/include`）存在的话，make也会去找。

### 在规则中使用通配符





## 参考资料

《跟我一起写 Makefile》