
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

## 4、背景概念介绍


### 4.1 系统调用

### 4.2 C库函数

### 4.3 线程安全

### 4.4 原子性

### 4.5 可重入函数

### 4.6 阻塞与非阻塞

### 4.7 同步与非同步