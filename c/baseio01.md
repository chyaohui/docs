> 今天说说 C 语言中关于基础 IO 的基本使用方式。


## 输入与输出

## 操作句柄
打开文件其实就是在操作系统中分配一些资源用于保存该文件的状态信息及文件的标识，以后用户程序可以用这个标识做各种操作，关闭文件则释放占用的资源。

打开文件的函数：
```c
#include <stdio.h>
FILE *fopen(const char *path, const char *mode);
```
FILE 是 C 标准库定义的结构体类型，其包含文件在内核中的标识（文件描述符）、I／O 缓冲区和当前读写位置信息，调用者不需知道 FILE 的具体成员，由库函数内部维护，调用者不应该直接访问这些成员。像 FILE* 这样的指针指针称为句柄（Handle）。

打开文件操作是对文件资源进行操作的，所以有可能打开文件失败，所以在打开函数时一定要判断返回值，如果失败则返回错误信息，以方便快速定位错误。

打开文件应该与关闭文件成对存在，虽然程序在退出时会释放相应的资源，但是对于一个长时间运行服务程序来说，经常打开而不关闭文件是会造成进程资源耗尽的，因为进程的文件描述符个数是有限的，及时关闭文件是个好习惯。

关闭文件的函数：
```c
#include <stdio.h>
int fclose(FILE *fp);
```
### fopen函数参数mode总结
* "r"：只读，文件必须存在。
* "w"：只写，如果不存在则创建，存在则覆盖。
* "a"：追加，如果不存在则创建。
* "r+"：允许读和写，文件必须存在。
* "w+"：允许读和写，文件不存在则创建，存在则覆盖。
* "a+"：允许读和追加，文件不存在则创建。


## stdin/stdout/stderr
在用户程序启动时，main 函数还没开始执行之前，会自动打开三个 FILE* 指针分别是：stdin、stdout、stderr，这三个文件指针是 libc 中定义的全局变量，在 stdio.h 中声明，printf 向 stdout 写，而 scanf 从 stdin 读，用户程序也可以直接使用这三个文件指针。

- stdin 只用于读操作，称为标准输入
- stdout 只用于写操作，称为标准输出
- stderr 也用于写操作，称为标准错误输出

通常程序的运行结果打印到标准输出，而错误提示打印到标准错误输出，一般标准输出和标准错误都是屏幕。通常可以标准输出重定向到一个常规文件，而标准错误输出仍然对应终端设备，这样就可以将运行结果与错误信息分开。


## 字节为单位的IO函数
fgetc 函数从指定的文件中读一个字节，getchar从标准输入读一个字节，调用 getchar() 相当于 fgetc(stdin)
```c
#include <stdio.h>
int fgetc(FILE *stream);
int getchar(void);
```

fputc 函数向指定的文件写入一个字节，putchar 向标准输出写一个字节，调用 putchar() 相当于调用 fputc(c, stdout)。
```c
#include <stdio.h>
int fputc(int c, FILE *stream);
int putchar(int c);
```

为什么使用 int 类型？可以看到这几个函数的参数和返回值类型都是 int，而非 unsigned char 型。因为错误或读到文件末尾时将返回 EOF，即 -1，如果返回值是 unsigned char（0xff），与实际读到字节 0xff 无法区分，如果使用 int 就可以避免这个问题。

## 操作读写位置函数
当我们在操作文件时，有一个叫「文件指针」的家伙来记录当前操作的文件位置，比如刚打开文件，调用了 1 次 fgetc 后，此时文件指针指向了第 1 个字节后边，注意是以字节为单位记录的。

改变文件指针位置的函数：
```c
#include <stdio.h>
int fseek(FILE *stream, long offset, int whence);
whence：从何处开始移动，取值：SEEK_SET | SEEK_CUR | SEEK_END
offset：移动偏移量，取值：可取正 | 负
void rewind(FILE *stream);
```
举几个简单例子：
```c
fseek(fp, 5, SEEK_SET);     // 从文件头向后移动5个字节
fseek(fp, 6, SEEK_CUR);     // 从当前位置向后移动6个字节
fseek(fp, -3, SEEK_END);    // 从文件尾向前移动3个字节
```

offset 可正可负，负值表示向文件开头的方向移动，正值表示向文件尾方向移动，如果向前移动的字节数超过文件开头则出错返回，如果向后移动的字节数超过了文件末尾，再次写入会增加文件尺寸，文件空洞字节都是 0

```c
$ echo "5678" > file.txt

fp = fopen("file.txt", "r+");
fseek(fp, 10, SEEK_SET);
fputc('K', fp)
fclose(fp)

// 通过结果可以看出字母K是从第10个位置开始写的
liwei:/tmp$ od -tx1 -tc -Ax file.txt 
0000000    35  36  37  38  0a  00  00  00  00  00  4b                    
           5   6   7   8  \n  \0  \0  \0  \0  \0   K
```

rewind(fp) 等价于 fseek(fp, 0, SEEK_SET)

ftell(fp) 函数比较简单，直接返回当前文件指针在文件中的位置
```c
// 实现计算文件字节数的功能
fseek(fp, 0, SEEK_END);
ftell(fp);
```

## 字符串为单位的IO函数
fgets 从指定的文件中读一行字符到调用者提供的缓冲区，读入内容不超过 size 。
```c
char *fgets(char *s, int size, FILE *stream);
char *gets(char *s);
```

首先要说明 gets() 函数强烈不推荐使用，类似 strcpy 用户不可以指定缓冲区大小，很容易造成缓冲区溢出错误。不过 strcpy 程序员还是可以避免，而 gets 的输入用户可以提供任意长的字符串，唯一避免方法就是不使用 gets，而使用 fgets(buf, size, stdin)

fgets 函数从 stream 所指文件读取以 '\n' 结尾的一行，包括 '\n' 在内，存到缓冲区中，并在该行结尾添加一个 '\0' 组成完整的字符串。如果文件一行太长，fgets 从文件中读了 size-1 个字符还没有读到 '\n'，就把已经读到的 size-1 个字符和一个 '\0' 字符存入缓冲区，文件剩余的半行可以在下次调用 fgets 时继续读。

若一次 fgets 调用在读入若干字符后到达文件末尾，则将已读到的字符加上 '\0' 存入缓冲区并返回，如果再次调用则返回 NULL，可以据此判断是否读到文件末尾。

fputs 向指定文件写入一个字符串，缓冲区保存的是以 '\0' 结尾的字符串，与 fgets 不同的是，fputs 不关心字符串中的 '\n' 字符。
```c
int fputs(const char *s, FILE *stream);
int puts(const char *s);
```



## 记录为单位的IO函数

## 格式化IO函数

## IO缓冲区




getchar()
putchar()

fgetc()
fputc()

getc()
putc()

puts()
gets()

fputs()
fgets()


