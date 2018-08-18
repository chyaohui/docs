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



