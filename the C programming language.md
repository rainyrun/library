# the C programming language

## 入门

```c
#include <stdio.h>

main()
{
    printf("hello, world\n");
}
```

编译 `cc hello.c` 输出可执行文件 a.out，输入 `./a.out` 即可运行程序

字符输入/输出

## 七 输入与输出

### 文件访问

```c
FILE *fp;
FILE *fopen(char *name, char *mode);
// 从文件中返回下一个字符
int getc(FILE *fp)
// 将字符写入 fp 指向的文件中
int putc(int c, FILE *fp)

// 文件的格式化输入输出
int fscanf(FILE *fp, char *format, ...)
int fprintf(FILE *fp, char *format, ...)

// 将多个文件连接起来的 cat 程序
#include <stdio.h>
/* cat: concatenate files, version 1 */
main(int argc, char *argv[])
{
    FILE *fp;
    void filecopy(FILE *, FILE *);
    if (argc == 1) {
        /* no args; copy standard input */
        filecopy(stdin, stdout);
    } else {
        while(--argc > 0) {
            if ((fp = fopen(*++argv, "r")) == NULL) { 
                printf("cat: can't open %s\n", *argv);
                return 1;
            } else {
                filecopy(fp, stdout);
                fclose(fp);
            }
            return 0;
        }

    }
 }
/* filecopy: copy file ifp to file ofp */ 
void filecopy(FILE *ifp, FILE *ofp)
{
    int c;
    while ((c = getc(ifp)) != EOF) {
        putc(c, ofp);
    }
}
```