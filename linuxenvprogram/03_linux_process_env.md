> 进程是操作系统运行程序的一个实例，也是操作系统分配资源的单位。在 Linux 环境中，每个进程都有独立的进程空间，以便对不同的进程进行隔离，使之不会相互影响。

## 1、main是C程序的开始么

```cpp
#include <stdlib.h>
#include <stdio.h>
static void __attribute__ ((constructor)) before_main(void)
{
    printf("Before main...\n");
} 
int main(void)
{
    printf("Main!\n");
    return 0;
}
```
其执行结果：
```
Before main...
Main!
```
注：与 constructor 属性对应的，还有 descontructor 属性，拥有 descontructor 属性的函数，会在 main 结束之后被调用。


## 2、活雷锋-exit
* 我们知道分配内存之后，如果不使用 free 来释放内存，就会造成内存的泄露。同样文件打开以后，如果忘记 close 也会造成资源的泄露，那么进程退出后，这些资源是否真的泄露呢？
* 当进程正常退出时，会调用 C 库的 exit，而当进程崩溃或被 kill 掉时，C 库的 exit 则不会被调用，只会执行内核退出进程的操作。
* 从 exit 源码可以得知，即使应用程序在应用层有内存泄露或文件句柄泄露也不必担心，当进程退出时，内核的 exit_group 调用将会默默地在后面做着清理工作，释放所有内存，关闭所有文件，以及其它资源，前提是这些**资源是该进程独享的**。

## 3、atexit介绍
* atexit 用于注册进程正常退出时的回调函数，若注册了多个回调函数，最后的调用书序与注册顺序相反。

* 使用 atexit 注册的退出函数是在进程正常退出时才会被调用。正常退出是指使用 exit 退出或使用 main 中最后的 return 语句退出。若因受到信号而导致程序退出，atexit 注册的退出函数则不会被调用。

* atexit 的实现是依赖于 C 库的代码，当进程收到信号，如果没有注册对应的信号处理函数，那么内核就会执行信号的默认动作，一般是直接终止进程。这时进程的退出完全由内核来完成，自然不会调用 C 库的 exit 函数，也就无法调用注册的退出函数。


## 4、小心使用环境变量
在 Linux 环境下，程序在启动时都会从 Shell 环境下继承当前的环境变量，我们也可以通过 C 库的接口来增加、修改或删除当前进程的环境变量。
```cpp
int putenv(char *string);
```
* putenv 用于增加或修改当期的环境变量。
* string 的格式为 "name=value"。
* 如果当前环境变量没有改名称的环境变量，则增加这个新的环境变量。
* 如果已经存在，则使用新值


示例：
```cpp
static void set_env_string(void)
{
    char test_env[] = "test_env=test";
    if (0 != putenv(test_env)) {
        printf("fail to putenv\n");
    }
    printf("1. The test_evn string is %s\n", getenv("test_env"));
} static void show_env_string(void)
{
    printf("2. The test_env string is %s\n", getenv("test_env"));
}
int main()
{
    set_env_string();
    show_env_string();
    return 0;
}
```
执行结果：
```
1. The test_evn string is test
2. The test_env string is (null)
```

> 原因在于使用putenv添加环境变量时，参数直接被当作环境变量的一部分了。对于本例而言，set_env_string 中的 test_env 数组直接被环境变量引用了。而 test_env 是一个局部变量，在执行 set_env_string 的时候，test_env 已经不存在了，对应栈上的内存会在后面的函数调用中使用，并存入其他值。因此，在进入 show_env_string 的时候，就无法得到正确的值了。

如果非要用 putenv 来设置环境变量，就必须要保证参数是一个长期存在的内容。因此，只能选择全局变量、常量或动态内存等。为了避免犯错，我们应该尽量使用另外一个接口 setenv


## 5、使用动态库
* 一般情况下，库文件的开发者会同时提供动态库和静态库两个版本
* 静态库在链接阶段会被直接链接进最终的二进制文件中，体积会比较大，但不再依赖于库文件
* 动态库并不被链接到文件中，只保存了依赖关系，生成二进制文件体积较小，在运行阶段需要加载动态库

1、**编译生成和使用动态库**

首先，来编译并生成一个动态库：
```cpp
void dynamic_lib_call(void)
{
    printf("dynamic lib call\n");
}
```
编译生成动态库与编译普通的可执行程序略有不同：
```
gcc -Wall -shared dlib.c -o libdlib.so
```
然后再编写一个简单例子，来使用这个动态库，代码如下：
```cpp
#include <stdlib.h>
#include <stdio.h>
extern void dynamic_lib_call(void);
int main(void)
{
    dynamic_lib_call();
    return 0;
}
```
编译运行：
```
gcc -Wall main.c -omain -L. -ldlib

# -l: 指示生成文件依赖的库
# -L: 指示 gcc 在哪个目录中查找依赖的库文件
```

运行时报错：
```
error while loading shared libraries: libdlib.so: cannot open shared object file: No such file or directory
```
原因在于 -L 只是在编译的过程中指示库的位置，而在程序运行的时候，动态库的加载路径默认为 /lib 和 /usr/lib。可以通过 /etc/ld.so.conf 配置文件和环境变量 LD_LIBRARY_PATH 指示额外的动态库路径。


2、**程序的"平滑无缝"升级**

* 动态库的一个重要优点就是，可执行程序并不包含动态库中的任何指令，而是在运行时加载动态库并完成调用
* 这就给我们提供了升级动态库的机会。只要保证接口不变，使用新版本的动态库替换原来的动态库，就完成了动态库的升级。更新完库文件以后启动的可执行程序都会使用新的动态库。
* 这样的更新方法只能够影响更新以后启动的程序，对于正在运行的程序则无法产生效果，因为程序在运行时，旧的动态库文件已经加载到内存中了。我们只能更新位于磁盘上的动态库的物理文件，而不能影响已经位于内存中的库了。

能否让运行中的服务程序也能在升级库以后使用新的指令，做到“平滑无缝”的升级呢？
* 使用一个结构体来管理动态库的接口
* 利用 dlopen、dlsym 等来加载动态库，更新接口。重新申请新的内存，来保存新的动态库接口
* 在调用服务接口时，要利用局部变量保存服务接口
* 释放旧接口的关键在于，要保证没有旧接口正在被使用。根据自己的业务，找到一个时间点——在这个时间点上，所有的线程（准确地说是请求流程）都已经服务过一次。这时，新来的请求就会使用新的接口，于是我们也就可以安全地释放旧接口了。


## 6、避免内存问题

1、**尴尬的realloc**

对于良好的代码风格，有一项很重要的要求是一个函数只专注做一件事情。如果该函数像瑞士军刀一样能实现多个功能，那基本上可以断言这不是一个设计良好的函数。

C 库中的 realloc 函数就是一个典型的反面教材：
```cpp
void *realloc(void *ptr, size_t size);
```
realloc 可以将 ptr 指向的内存调整个 size 大小，看似功能很明确，其实一共有三种不同的行为：
* 参数 ptr 为 NULL，而 size 不为 0，则等同于 malloc(size)。
* 参数 ptr 不为 NULL，而 size 为 0，则等同于 free(ptr)。
* 参数 ptr 和 size 均不为 0，其行为类似于 free(ptr)；malloc(size)

举例说明：
```cpp
void * ptr = realloc(ptr, new_size);
if (!ptr) {
    //错误处理
}
```
当 realloc 分配内存失败的时候，ptr 会返回 NULL。但是这时 ptr 原来指向的内存并没有被释放，而 ptr 却已经被赋值为 NULL 了，这就造成了 ptr 原有内存泄漏。正确的做法是：
```cpp
void * new_ptr = realloc(ptr, new_size);
if (!new_ptr) {
    //错误处理
} 
ptr = new_ptr; 
// 只有在分配内存成功的情况下，才会让 ptr 等于 new_ptr
```


2、**如何防止内存越界**

通过良好的编程习惯基本上是可以避免内存越界问题的。防范的根本思想在于在对缓冲区（一般为数组）进行拷贝前，要保证复制的长度不要超过缓冲区的空间大小。

* 使用memcpy函数
```cpp
#define BUF_SIZE 64
char dst_buf[BUF_SIZE];
memcpy(dst_buf, src_buf, BUF_SIZE);

// 或
char dst_buf[64];
memcpy(dst_buf, src_buf, sizeof(dst_buf));
```
这样就可以做到缓存大小和复制长度的同步修改。

使用安全的库函数也可以保证复制的长度不超过缓冲区的空间，下面来介绍 4 种库函数。
* 使用strncat代替strcat
* 使用strncpy代替strcpy
* 使用snprintf代替sprintf
* 使用fgets代替gets


3、**如何定位内存问题**

valgrind 作为一个免费且优秀的工具包，提供了很多有用的功能，其中最有名的就是对内存问题的检测和定位。六种常见的内存问题：
* 动态内存泄漏；
* 资源泄漏，代码中以文件描述符为例；
* 动态内存越界；
* 数组越界；
* 动态内存double free；
* 使用野指针。

```
$ valgrind --track-fds=yes --leak-check=full \
           --undef-value-errors=yes ./mem
```
