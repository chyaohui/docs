### 1、不要用 memcmp 比较结构体
比较两个结构体时，若结构体重含有大量的成员变量，为了方便，程序员往往会直接使用 memcmp 对这两个结构体进行比较，以避免对每个成员进行分别比较。这样的代码写起来比较简单，然而却很可能深藏隐患。

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
typedef struct padding_type {
    short m1;
    int m2;
} padding_type_t;
int main()
{
    padding_type_t a = {
        .m1 = 0,
        .m2 = 0,
    };
    padding_type_t b;
    memset(&b, 0, sizeof(b));
    if (0 == memcmp(&a, &b, sizeof(a))) {
        printf("Equal!\n");
    }
    else {
        printf("No equal!\n");
    }
    return 0;
}
```

是因为 `struct padding_type->m1` 类型是 short 类型，而 m2 的类型是 int 类型。根据对齐规则，需要进行 4 字节对齐。因此编译器会在 m1 后面插入两个 padding 字节，而这两个字节的内容却是 "随机" 的。结构体 b 由于调用了 memset 对整个结构体内存进行了清零，其 padding 值自然就为 0 。当使用 memcmp 对两个结构体进行比较时，结果就是不相同了，即返回值不为 0。

所以，除非在项目中可以保证所有的结构体都会使用 memset 来进行初始化，否则就不要直接使用 memcmp 来比较结构体。

### 2、有符号数和无符号数的位移区别
在代码规范中一般都会要求，如果没有符号要求，则尽量使用无符号整数，避免使用有符号整数。因为有符号整数在一些常见的操作中，将表现出与无符号整数大相径庭的行为。

```cpp
#include <stdlib.h>
int main ()
{
    int a = 0x80000000;
    unsigned int b = 0x80000000;
    printf("a right shift value is 0x%X\n", a >> 1);
    printf("b right shift value is 0x%X\n", b >> 1);
    return 0;
}
```
其输出结果：
```sh
$ gcc -Wall 15_2_sign_shift.c
$ ./a.out
a right shift value is 0xC0000000
b right shift value is 0x40000000
```

通过对其二进制数据进行分析
```sh
a : 10000000 00000000 00000000 00000000  -> 11000000 00000000 00000000 00000000
# a 为有符号整数，第 1 高位是符号位，按位右移时，高位补充符号位，因此第 2 位补充为 1，如上所得

b : 10000000 00000000 00000000 00000000  -> 01000000 00000000 00000000 00000000
# b 为无符号整数，按位右移，高位补 0 ，得如上结果。
```

### 3、数组和指针

要认识到指针其实就是一个变量，只不过这个变量用于保存地址的，或者说它保存的值可以被视为地址。因为指针类型可以合法的使用`*`运算符，其实就是将变量的值视为一个地址，然后从这个地址中读取值。

```cpp
#include <stdlib.h>
#include <stdio.h>
int main(void)
{
    short *p1 = 0;
    int **p2 = 0;
    ++p1;
    ++p2;
    printf("p1 = %d, p2 = %d\n", p1, p2);
    return 0;
}
```
可以想一下，这个程序是否会崩溃？如果不崩溃输出什么结果？如果真正理解了指针，看完代码，就可以迅速地说出最终的结果。

### 4、再论数组首地址
暂略。


### 5、整数类型转换
```cpp
#include <stdlib.h>
#include <stdio.h>
#define PRINT_COMPARE_RESULT(a, b) \
        if (a > b) { \
        printf(#a " > " #b "\n"); \
        } else if ( a < b) { \
        printf(#a " < " #b "\n"); \
        } else { \
        printf(#a " = " #b "\n"); \
        }

int main(void)
{
    signed int a = -1;
    unsigned int b = 2;
    signed short c = -1;
    unsigned short d = 2;
    PRINT_COMPARE_RESULT(a, b);
    PRINT_COMPARE_RESULT(c, d);
    return 0;
}
```

输出结果：
```sh
$ ./a.out
a > b
c < d
```

当 signed int 和 unsigned int 进行比较时，signed int 会被转换为 unsigned int。-1 的值即 0xFFFFFFFF，就被视为无符号整数的最大值，因此 a > b。C 标准规定，当进行整数提升时，如果 int 类型可以表示原始类型的所有值时，它就被转换为 short，那么它们就会被转换为 int 类型，则实际是对 (int) -1 和 (int) 2 进行比较，结果自然是 c < d。

### 6、小心 volatile 的原子性误解
关于 volatile 的说明，可以理解为易变的，防止编译器对其优化，因此用途一般由以下三种：
* 外部设备寄存器映射后的内存 - 因为外部寄存器随时可能由于外部设备的状态变化而改变，因此映射后的内存需要用 volatile 来修饰。
* 多线程或异步访问的全局变量
* 嵌入式编程 -- 防止编译器对其优化

经常会错误地理解第 2 种情况：认为 int 类型的加减操作是原子的，因此使用了 volatile 后就无须使用锁来进行竞争保护了，其实 volatile 只能保证在访问该变量时，每次都是从内存中读取最新值，并不会使用寄存器中缓存的值，而对变量的修改，volatile 并不提供原子性的保证。

### 7、x==x 何时为假？
```cpp
#include <stdio.h>
#include <string.h>
int main(void)
{
    float x = 0xffffffff;
    if (x == x) {
        printf("Equal\n");
    }
    else {
        printf("Not equal\n");
    }
    if (x >= 0) {
        printf("x(%f) >= 0\n", x);
    }
    else if (x < 0) {
        printf("x(%f) < 0\n", x);
    }
    int a = 0xffffffff;
    memcpy(&x, &a, sizeof(x));
    if (x == x) {
        printf("Equal\n");
    }
    else {
        printf("Not equal\n");
    }
    if (x >= 0) {
        printf("x(%f) >= 0\n", x);
    }
    else if (x < 0) {
        printf("x(%f) < 0\n", x);
    }
    else {
        printf("Surprise x(%f)!!!\n", x);
    }
    return 0;
}
```

简单解释下原因：
* 当`float x=0xffffffff`时，将整数赋值给一个浮点数，由于 float 和 int 都占用了 4 字节，但浮点数的存储格式与整数不同，其需要一定的数位来作为小数位，所以 float 的表示范围要小于 int。这里涉及了 C 语言中的类型转换。
* 当整数转换为浮点数时，尽管数值会有所变化，但结果一定是一个合法的浮点值。所以 x 一定等于 x，且 x 不是大于等于 0，就是小于 0。
* 当使用 memcpy 将 0xff 填充到 x 的地址时，这时保证了x储存的一定是 0xffffffff，但很可惜它不是一个合法的浮点值，而是一个特殊值 NaN。
* 作为一个非法的浮点数 NaN，当它与任何数值相比较时，都会返回假。所以就有了比较意外的结果 x==x 为假，x 即不大于 0，不小于 0，也不等于 0。


### 8、小心浮点陷阱

1、**浮点数的精度限制**

浮点数的存储格式与整数完全不同。大部分的实现采用的是 IEEE 754 标准，float 类型是 1 个 sign bit、8 个 exponent bits 和 23 个 mantissa bits。而 double 类型是 1 个 sign bit、11 个 exponent bits 和 52 个 mantissa bits。关于浮点数是如何表示小数部分的，可以自行参考维基百科。简单来说，小数部分是依靠2的负多少次方来近似表示的，因此浮点数存在精度的问题，对浮点数进行比较时，要使用范围比较。

```cpp
#include <stdlib.h>
#include <stdio.h>
int main(void)
{
    float x = 0.123-0.11-0.013;
    if (x == 0) {
        printf("x is 0!\n");
    }
    if (-0.0000000001 < x && x < 0.0000000001) {
        printf("x is in 0 range!\n");
    }
    return 0;
}
```
输出结果：
```sh
$ gcc -Wall 15_8_float1.c
$ ./a.out
x is in 0 range!
```

从数学的角度看，`float x=0.123-0.11-0.013`，得到的一定是 0。但对于浮点数来说，因为其不能精确地表示小数，因此x最终的结果是一个趋近于 0 的值。故而不能用 0 和 x 直接进行比较，而是要使用一个范围来确定 x 是否为 0。

2、**两个特殊的浮点值**

浮点数有两个特殊的值，一个是 NaN，另一个是 infinite，即无限。下边看一个示例：
```cpp
#include <stdio.h>
#include <stdlib.h>
int main(void) {
    float x = 1 / 0.0;
    printf("x is %f\n", x);
    x = 0 / 0.0;
    printf("x is %f\n", x);
    return 0;
}
```
输出结果为：
```sh
$ a.out
x is inf
x is -nan
```

当 1 除以 0.0 时，得到的是 infinite，而用 0 除以 0.0 时，得到的就是 NaN。虽然这里完全只是一则普通的除法运算，但也会产生 NaN 的情况。

那么当使用除法运算时，对除数进行检查，保证其不为 0.0，是否就可以避免 NaN 了？再看下面的代码：
```cpp
#include <stdlib.h>
#include <stdio.h>
int main(void)
{
    float x;
    while (1) {
        scanf("%f", &x);
        printf("x is %f\n", x);
    }
    return 0;
}
```
编译执行：
```sh
$ ./a.out
inf
x is inf
nan
x is nan
```

令人惊讶的是，scanf 不仅接受 inf 和 nan 的输入，并将其视为浮点数的两种特殊值。那么对于 UI 程序来说，当遇到浮点数值的时候，我们必须首先判断其是否为合法的浮点值。C 库提供了两个库函数 isinf 和 isnan，分别用于判断浮点数是否为 infinite 和 NaN。

