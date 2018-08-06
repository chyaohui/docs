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