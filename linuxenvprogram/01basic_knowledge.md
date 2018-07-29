
目录：
------
* [1、一个 Linux 程序的诞生](#1一个linux程序的诞生)
* [2、程序的构成](#2程序的构成)
* [3、程序是如何 "跑" 的](#3程序是如何跑的)
* [4、背景概念介绍](#4背景概念介绍)
  - [4.1 系统调用](#41-系统调用)
  - [4.2 C 库函数](#42-c库函数)
  - [4.3 线程安全](#43-线程安全)
  - [4.4 原子性](#44-原子性)
  - [4.5 可重入函数](#45-可重入函数)
  - [4.6 阻塞与非阻塞](#46-阻塞与非阻塞)
  - [4.7 同步与非同步](#47-同步与非同步)


> 基础知识看似简单，但是想要真正理解它们，是需要花一番功夫的。除了需要积累经验以外，更需要对它们进行不断的思考和理解。


## 1、一个Linux程序的诞生
HelloWorld 程序：
```c
#include <stdio.h>
int main(void)
{
    printf("Hello World!\n");
    return 0;
}
```

使用 gcc 编译程序：
```sh
$ gcc -g -Wall hellowolrd.c -o helloworld
```

整个过程看似简单，其实涉及到预处理、编译、汇编和链接等多个步骤，只不过 gcc 作为工具集自动完成了所有的步骤。
* **预处理**。预处理用于处理预处理命令，比如可以将头文件的内容包含到本文件中，即头文件中所有代码都会在 `#include` 处展开。可以通过 `gcc -E helloworld.c > helloworld.i` 在预处理后停止后边的操作，得到预处理后的文件 `helloworld.i`。思考 `为什么不能在头文件中定义全局变量?` 因为定义全局变量的代码会存于所有以#include包含头文件的文件中，都定义一个全局变量，造成了冲突。

* **编译**。编译环节是对源代码进行语法分析，并优化产生对应的汇编代码的过程。同样可以使用 gcc 得到汇编代码 `gcc -S helloworld.c -o helloworld.s` 。

* **汇编**。汇编的过程比较简单，就是将源代码翻译成可执行的指令，并生成目标文件。对应的 gcc 命令为 `gcc -c helloworld.c -o helloworld.o` 。

* **链接**。链接是生成最终可执行文件的最后一个步骤。它的工作就是将各个目标文件--包括库文件，链接成一个可执行程序。在这个过程中，涉及的概念比较多，如地址和空间的分配、符号解析、重定位等。该工作由 GNU 的链接器 ld 完成的。

实际上我们可以使用 -v 选项来查看完整和详细的 gcc 编译过程：
```sh
$ gcc -g -Wall -v helloworld.c -o helloworld
```

## 2、程序的构成
Linux 下的二进制可执行程序的格式一般为 ELF 格式，内容如下：
```
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00 
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              EXEC (Executable file)
  Machine:                           Advanced Micro Devices X86-64
  Version:                           0x1
  Entry point address:               0x4027e0
  Start of program headers:          64 (bytes into file)
  Start of section headers:          107352 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         8
  Size of section headers:           64 (bytes)
  Number of section headers:         29
  Section header string table index: 28

Section Headers:
  [Nr] Name  Type  Address  Offset   Size   EntSize   Flags  Link  Info  Align
  [13] .text PROGBITS  00000000004027e0  000027e0    000000000000fb68  0000000000000000  AX       0     0     16
  [25] .data PROGBITS  000000000061a040  0001a040    0000000000000200  0000000000000000  WA       0     0     32
  [26] .bss  NOBITS    000000000061a240  0001a240    0000000000000d20  0000000000000000  WA       0     0     32
```
ELF 文件的主要内容就是由各个 section 及 symbol 表组成的，较为熟悉的是 text 段、data 段、bss 段。
* **text 段**。为代码段，用于保存可执行指令。
* **data 段**。为数据段，用于保存有非 0 初始值的全局变量和静态变量。
* **bss 段**。用于保存没有初始化或初始化为 0 的全局变量和静态变量。当程序加载时，bss 段变量会被初始化为 0。

其它几个算是比较重要的段：
* **debug 段**。用于保存调试信息。
* **dynamic 段**。用于保存动态链接信息。
* **fini 段**。用于保存进程退出时的执行程序，当进程结束时，系统会自动执行这部分代码。
* **init 段**。用于保存进程启动时的执行程序，当进程启动时，系统会自动执行这部分代码。
* **rodata 段**。用于保存只读数据，如 const 修饰的全局变量、字符串常量。
* **symtab 段**。用于保存符号表。

对于与调试相关的段，如果不使用 -g 选项，则不会生成，但与符号相关的段仍然会存在，可以使用 strip 去掉符号等信息，一般嵌入式的产品中，为了减少程序占用的空间，都会使用 strip 去掉非必要的段。


## 3、程序是如何跑的
程序是如何 "跑" 起来的？在 Linux 环境下，可以使用 strace 跟踪系统调用，从而帮助自己研究系统程序加载、运行和退出的过程。

```
$ strace ./hello
execve("./hello", ["./hello"], [/* 21 vars */]) = 0
brk(0)                                  = 0x1f7b000
mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f2c5359b000
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
open("/etc/ld.so.cache", O_RDONLY)      = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=19420, ...}) = 0
mmap(NULL, 19420, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7f2c53596000
close(3)                                = 0
open("/lib64/libc.so.6", O_RDONLY)      = 3
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0000\356\1\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0755, st_size=1924768, ...}) = 0
mmap(NULL, 3750184, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f2c52fe8000
mprotect(0x7f2c53172000, 2097152, PROT_NONE) = 0
mmap(0x7f2c53372000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x18a000) = 0x7f2c53372000
mmap(0x7f2c53378000, 14632, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7f2c53378000
close(3)                                = 0
mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f2c53595000
mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f2c53594000
mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f2c53593000
arch_prctl(ARCH_SET_FS, 0x7f2c53594700) = 0
mprotect(0x7f2c53372000, 16384, PROT_READ) = 0
mprotect(0x7f2c5359c000, 4096, PROT_READ) = 0
munmap(0x7f2c53596000, 19420)           = 0
fstat(1, {st_mode=S_IFCHR|0620, st_rdev=makedev(136, 1), ...}) = 0
mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f2c5359a000
write(1, "Hello World!\n", 13Hello World!
)          = 13
exit_group(0)                           = ?
+++ exited with 0 +++
```
在 Linux 环境中，执行一个命令时，首先是由 shell 调用 fork，然后在子进程中来真正执行这个命令（这一过程在 strace 输出中无法体现）。strace 是 hello 开始执行后的输出。首先是调用 execve 来加载 hello，然后ld会分别检查 ld.so.nohwcap 和 ld.so.preload。其中，如果 ld.so.nohwcap 存在，则 ld 会加载其中未优化版本的库。如果 ld.so.preload 存在，则 ld 会加载其中的库——在一些项目中，我们需要拦截或替换系统调用或 C 库，此时就会利用这个机制，使用 LD_PRELOAD 来实现。之后利用 mmap 将 ld.so.cache 映射到内存中，ld.so.cache 中保存了库的路径，这样就完成了所有的准备工作。接着 ld 加载 c 库—— libc.so.6，利用 mmap 及 mprotect 设置程序的各个内存区域，到这里，程序运行的环境已经完成。后面的 write 会向文件描述符 1（即标准输出）输出 "Hello world！\n"，返回值为 13，它表示 write 成功的字符个数。最后调用 exit_group 退出程序，此时参数为 0，表示程序退出的状态。

## 4、背景概念介绍
### 4.1 系统调用
* 系统调用是操作系统提供的服务，是应用程序与内核通信的接口。在 x86 平台上，有多种陷入内核的途径，最早是通过 `int 0x80` 指令来实现的，后来新增指令 `sysenter`，性能消耗大约是 `int 0x80` 的一半左右，不过相对于普通函数调用来说，系统调用的性能消耗也还是巨大的。所以在追求极致性能的程序中，都在尽力避免系统调用，比如 C 库的 gettimeofday 就避免了系统调用。

* 用户空间的程序默认是通过栈来传递参数的。对于系统调用来说，内核态和用户态使用的是不同的栈，这使得系统调用的参数只能通过寄存器的方式进行传递。

* 程序员写代码时根本不用关心参数是如何编译的，编译器已经默默地为我们做了一切----压栈、出栈、保存返回地址等操作，但是编译器如何知道调用函数是普通函数还是系统调用呢？如果是后者，编译器就不能简单地使用栈来传递参数了。

### 4.2 C库函数
Linux 环境下，使用的 C 库一般都是 glibc，它封装了几乎所有的系统调用，代码中使用的 "系统调用"，实际上就是调用 C 库中的函数。C 库函数同样位于用户态，所以编译器可以统一处理所有的函数调用，而不用区分该函数到底是不是系统调用。

在 glibc 的代码中，用了大量的编译器特性以及编程技巧，可读性不高。open 在 glibc 中对应的实现函数实际上是 `__open_nocancel`。
```c
int __open_nocancel(const char *file, int oflag, ...) {
    int mode = 0;
    if (oflag & O_CREAT) {
        va_list arg;
        va_start(arg, oflag);
        mode = va_arg(arg, int);
        va_end(arg);
    }
    return INLINE_SYSCALL(openat, 4, AT_FDCWD, file, oflag, mode);
}
```
其中 `INLINE_SYSCALL` 是我们关心的内容，这个宏完成了对真正系统调用的封装：`INLINE_SYSCALL->INTERNAL_SYSCALL`。
```c
# define INTERNAL_SYSCALL(name, err, nr, args...) \
    ({ \
    register unsigned int resultvar; \
    EXTRAVAR_##nr \
    asm volatile ( \
    LOADARGS_##nr \
    "movl %1, %%eax\n\t" \
    "int $0x80\n\t" \
    RESTOREARGS_##nr \
    : "=a" (resultvar) \
    : "i" (__NR_##name) ASMFMT_##nr(args) : "memory", "cc"); \
    (int) resultvar; })
```
关键代码使用嵌入式汇编写的，`move%1, %%eax` 表示将第 1 个参数(__NR_##name)赋给寄存器 eax。`__NR_##name` 为对应的系统调用号，对于 open 来说，其为 `__NR_openat`。系统调用号再文件 /usr/include/asm/unistd_64.h 中定义，代码如下：
```c
$ cat /usr/include/asm/unistd_64.h | grep openat
#define __NR_openat        257
```

### 4.3 线程安全
线程安全是指代码可以在多线程环境下 "安全" 地执行。何谓安全？即符合正确的逻辑结果，是程序员期望的正常执行结果。为了实现线程安全，该代码要么只能使用局部变量或资源，要么就是利用锁等同步机制，来实现全局变量或资源的串行访问。
```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
static int counter = 0;
#define LOOPS 10000000
static void * thread(void * unused)
{
    int i;
    for (i = 0; i < LOOPS; ++i) {
        ++counter;
    }
    return NULL;
} 
int main(void)
{
    pthread_t t1, t2;
    pthread_create(&t1, NULL, thread, NULL);
    pthread_create(&t2, NULL, thread, NULL);
    pthread_join(t1, NULL);
    pthread_join(t2, NULL);
    printf("Counter is %d by threads\n", counter);
    return 0;
}
```


### 4.4 原子性
对于计算机来说，如果变量是原子的，那么对这个变量的任何访问和更改都是原子的。如果操作是原子的，那么这个操作将是不可分割的，要么成功，要么失败，不会有任何的中间状态。

用户 A 向用户 B 转账 1000 元，简单来说，这里最起码有两个步骤：
* 用户 A 的账户减少 1000 元；
* 用户 B 的账户增加 1000 元；

对于原子操作，如果因电力中断导致转账操作进行一半就失败时，用户 A 账户肯定不会减少 1000 元，要保证用户 A 减少 1000 元和用户 B 增加 1000 元，必须同时成立，不会出现一个中间结果

### 4.5 可重入函数

### 4.6 阻塞与非阻塞

### 4.7 同步与非同步