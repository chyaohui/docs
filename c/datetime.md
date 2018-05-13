> 在平时的 C 语言编程中，经常会遇到关于时间和日期的相关操作。时间日期的操作其实并不难也不复杂，但是作为程序员还是必须要熟练掌握相关操作的，就像对待字符串和内存操作一样。

## time_t 类型时间
在 C 语言中可以用 time_t 类型表示时间，time_t 类型时间其实就是所谓的「日历时间」，在 Linux 系统中就是距离 1970-01-01 08:00:00 这个时间点所经过的秒数，通常 time_t 是一个和 long 一样长的整数，但它似乎无法表示 1970 年以前的时间。

与 time_t 相关的最常用函数是 time_t time(time_t *t)，它的参数是一个 time_t 类型的指针，返回一个与传入参数相等的 time_t 类型值，如果 time() 参数是 NULL，则返回当前时间。

```cpp
time_t tt = time(NULL)
```

## struct tm 类型时间
现在我们已经可以用 C 语言表示时间了，但是这个时间是距离 1970 年的秒数，只是个相对时间，通常我们需要得到更为详细的时间，这就要用到 struct tm 类型了，它可以表示更具体的时间信息。
```cpp
struct tm {
    int tm_sec;  
    int tm_min;  
    int tm_hour; 
    int tm_mday; 
    int tm_mon;  
    int tm_year; 
    int tm_wday; 
    int tm_yday; 
    int tm_isdst;
};
```
struct tm 是一个结构体，我们需要先弄清楚它的具体成员信息，一个 struct tm 类型包括以下成员：
* tm_year：表示年份，它是从 1900 年开始算的，所以程序中需要将 tm_year + 1900。
* tm_mon：表示月份，它的值范围是 0 到 11，0 表示 1 月份，所以程序中需要将 tm_mon + 1。
* tm_mday：表示当月的几号。
* tm_hour：表示小时。
* tm_min：表示分钟。
* tm_sec：表示秒。
* tm_wday：表示星期几，它的值范围是 0-6，0 是星期天，1 是星期一，6 是星期六。
* tm_yday：表示当年的第几天，要注意的是 1 月 1 日是第 0 天。
* tm_isdst：表示是否是夏令时。

与 struct tm 类型相关的函数是 localtime()，它可以把一个 time_t 类型构造成 struct tm 类型，具体来看下面的代码：
```cpp
#include <stdio.h>
#include <time.h>

int main(int argc, char *argv[])
{
    time_t tt = time(NULL);
    struct tm *tm = localtime(&tt);
    printf("%d-%02d-%02d %02d:%02d:%02d\n",
        tm->tm_year + 1900, tm->tm_mon + 1,
        tm->tm_mday, tm->tm_hour,
        tm->tm_min, tm->tm_sec);
}

[root@sina ~]$ gcc main.c -omain && ./main 
2018-05-07 11:39:02
```

## string 类型时间
那么如何把 struct tm 类型转换为 time_t 类型呢？就用 mktime() 函数，注意参数是指针类型。那么如何输出时间呢？可以简单的使用 ctime() 函数和 asctime() 函数，需要注意的是返回字符串结尾包含换行符 \n。
```cpp
#include <stdio.h>
#include <time.h>

int main(int argc, char *argv[])
{
    struct tm st;
    st.tm_year = 2018 - 1900;
    st.tm_mon = 4;
    st.tm_mday = 13;
    st.tm_hour = 10;
    st.tm_min = 47;
    st.tm_sec = 33;
    st.tm_isdst = 0;
    time_t tt = mktime(&st);
    printf("%s", asctime(&st));
    printf("%s", ctime(&tt));
}

[root@sina ~]$ gcc main.c -omain && ./main 
Sun May 13 10:47:33 2018
Sun May 13 10:47:33 2018
```
我们自己用 struct tm 构造了一个时间，并且在执行 mktime() 函数后，tm_wday 属性也会自动计算出来。asctime() 函数是把 struct tm 格式转换为字符串格式，而 ctime() 是将秒数转换为字符串格式。

## 高精度时间函数
C 语言中也可以使用函数 gettimeofday() 来获得时间，它的精度可以达到微秒，而且可以获取当地时区的信息。具体的函数原型及涉及的结构体如下：
```cpp
#include <sys/time.h>
int gettimeofday(struct timeval *tv, \
                 struct timezone *tz);

// 时间相关结构
struct timeval {
    time_t      tv_sec;
    suseconds_t tv_usec;
};

// 时区相关结构
struct timezone {
    int tz_minuteswest;
    int tz_dsttime;
};
```
gettimeofday 可以获取到微秒级的时间，它有一个典型的应用场景，来计算测试某代码片段花费的时间，可以精确到微秒。
```cpp
struct timeval tv1;
struct timeval tv2;

gettimeofday(&tv1, NULL);
... ... // 代码片段
gettimeofday(&tv2, NULL);
ret = tv2 - tv1 // 伪代码
```

## clock 计算程序运行时间
在 time.h 中，clock() 函数返回程序运行到这条语句所消耗的时间，单位可以通过 CLOCKS_PER_SEC 来确认，我这里测试输出是 1000000，则证明是微秒。和 gettimeofday 一样，我们可以方便的用它来计算程序某一段语句所消耗的时间。

```cpp
#include <stdio.h>
#include <time.h>
 
int main()
{
    int i = 0;
    printf("CLOCKS_PER_SEC: %ld\n", \
                     CLOCKS_PER_SEC);
    long c = clock();
    while(i < 1<<30) i++;
    printf("The while loop cost %ld.\n", \
                            clock() - c);
    c = clock();
    for(i = 0; i < 1<<30; i++);
    printf("The for loop cost %ld.\n", \
                          clock() - c);
}

[root@sina ~]$ gcc main.c -omain && ./main 
CLOCKS_PER_SEC: 1000000
The while loop cost 2950000.
The for loop cost 2970000.
```

clock_t 类型时间我们称为时钟计时单元，一个时钟计时单元的时间长短是由 CPU 控制的，一个 clock tick 不是 CPU 的一个时钟周期，而是 C/C++ 的一个基本计时单位。

## 系统时间与硬件时间
顺道说下系统时间与 CMOS 时间的关系，系统时间是由操作系统来维护的，CMOS 时间是 CMOS 芯片保存的时间。系统启动时，操作系统将 CMOS 读出时间记录为系统时间，同时操作系统也会自动每隔一段时间将系统时间写入 CMOS 中。如果使用 date 命令修改系统时间后马上重启电脑，操作系统还没有来得及将系统时间同步到 CMOS，这样开机后还是没有修改的时间，所以推荐手动使用命令 clock 将系统时间同步到 CMOS 中。
```sh
$ date -s "2018-05-13 12:15:01"
$ clock -w
```

## 最后总结
其实 C 语言给我们提供了很多个关于时间操作的函数，文中记录了一些比较常用的函数用法，掌握了这些函数的用法后基本上可以应付大部分程序中关于时间的操作。内容还是比较多的，这里再次简单总结下几类函数使用：
* time_t 类型时间：从 1970 年距离当前时间的秒数，相关函数 time() mktime() gettimeofday()
* struct tm 类型时间：具体的时间函数，localtime() mktime()
* string 类型时间：输出为字符串格式的时间日期，ctime() asctime()
* format string 类型时间：格式化的字符串格式时间日期，strftime() 文中没有具体讲解
* clock tick 类型时间：计算程序运行的时间，精度为微秒，clock()

![关系图](https://mmbiz.qpic.cn/mmbiz_jpg/yVibDjicRT1Vt3uz8FCibNk6bFlnqC5DbloblSsjricaPyIvzAQRsq2aLkBqSrPbjAf9Se6QicOwJiaApufKRhKEdjXg/0?wx_fmt=jpeg)
