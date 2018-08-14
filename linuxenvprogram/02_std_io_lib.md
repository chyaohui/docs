> 标准 IO 库

## 1、IO缓存
C 库的 IO 接口对文件 IO 进行了封装，为了提高性能，引入了缓存机器，共有三种缓存机制：全缓存、行缓存、无缓存。
* **全缓存**：一般用于访问真正的磁盘文件。C 库会为文件访问申请一块内存，只有当文件内容将缓存填满或执行冲刷函数 flush 时，C 库才会将缓存内容写入内核中。

* **行缓存**一般用于访问终端。当遇到一个换行符时，就会引发真正的 IO 操作。需要注意的是，C 库的行缓存也是固定大小的。因此，当缓存已满，即使没有换行符时也会引发 IO 操作。

* **无缓存**，顾名思义，C 库没有进行任何的缓存。任何 C 库的 IO 调用都会引发实际的 IO 操作。

C 库提供了接口，用于修改默认的缓存行为，相关代码如下：
```cpp
void setbuf(FILE *stream, char *buf);
void setbuffer(FILE *stream, char *buf, size_t size);
void setlinebuf(FILE *stream);
int setvbuf(FILE *stream, char *buf, int mode, size_t size);
```

示例代码：
```cpp
int main(void)
{
    printf("Hello ");
    if (0 == fork()) {
        printf("child\n");
        return 0;
    }
    printf("parent\n");
    return 0;
}
```



## 2、fopen与open标志位对比
|fopen 标志位|open 标志位|用途|
|:-----:|:-----:|-----|
|r|O_RD_ONLY|以只读方式打开文件|
|r+|O_RDWR|以读写方式打开文件|
|w|O_WRONLY\|O_CREAT\|O_TRUNC|写方式打开文件，文件存在截断为0，不存在则创建文件|
|w+|O_RDWR|O_CREAT|O_TRUNC|读写方式打开文件，文件存在截断为0，不存在则创建文件|
|a|O_WRONLY+O_APPEND+O_CREAT|追加写的方式打开文件，文件不存在时创建文件|
|a+|O_RDWR+O_APPEND+O_CREAT|追加读写的方式打开文件，文件不存在时创建文件|
|b|无|表示打开的文件是二进制流而不是文本流|

