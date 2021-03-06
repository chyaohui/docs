目录：
---

> 文件 IO 是操作系统不可或缺的部分，也是实现数据持久化的手段。对于 Linux `一切皆文件` 的思想更说明了文件在 Linux 内核中的重要位置。

## 1、Linux中的文件
### 1.1 文件、文件描述符和文件表
* **狭义的文件**是指文件系统的物理文件，而**广义的文件**则可以是 Linux 管理的所有对象，包括设备、管道、内存等。这些广义的文件利用 `VFS` 机制，以文件系统的形式挂载在 Linux 内核中，对外提供一致的文件操作接口。

* 文件描述符是一个非负整数，本质上就是一个**句柄**。一切对用户透明的返回值，即可视为句柄。用户空间利用文件描述符与内核进行交互；而内核拿到文件描述符后，可以通过它得到用于管理文件的真正的数据结构。

* 使用文件描述符有两个**好处**：一是增加了安全性，句柄对用户完全透明，用户无法通过任何方式更改句柄对应的内部结果；二是增加了可扩展性，用户代码只依赖于句柄的值，实际结构的类型和与句柄的映射关系都随时可以变化，不会影响现有的用户代码。

* Linux 的每个进程都会维护一个**文件表**，以便维护该进程打开文件的信息，包括打开的文件的个数、每个打开文件的偏移量等信息。

### 1.2 内核文件表的实现
内核中进程对应的结构是 `task_struct`，进程的文件表保存在 `task_struct->files` 中。

待补充 。。。


## 2、打开文件

### 2.1 open 介绍
函数原型：
```cpp
int open(const char *pathname, int flags, mode_t mode);
```

open 函数的参数解释：
* pathname：表示要打开文件的路径。
* flags：用于指示打开文件的选项。常用的 `O_RDONLY`、`O_WRONLY`、`RDWR`，这三个选项必须有且只能有一个被指定。
* mode：只在创建文件时需求，用于指定所创建文件的权限位(还要受 umask 环境变量的影响)

### 2.2 更多选项

列出 Linux 平台支持的大部分选项：
* `O_APPEND`：每次进行写操作时，内核都会先定位到文件尾，再执行写操作。
* `O_ASYNC`：使用异步 IO 模式。
* `O_CLOEXEC`：在打开文件的时候，就为文件描述符设置 FD_CLOEXEC 标志。
* `O_CREAT`：当文件不存在时，就创建文件。
* `O_DIRECT`：对该文件进行直接 IO，不使用 VFS Cache。
* `O_DIRECTORY`：要求打开的路径必须是目录。
* `O_EXCL`：该标志用于确保是此次调用创建的文件，需要与 `O_CREAT` 同时使用，当文件已经存在时，open 函数会返回失败。
* `O_LARGEFILE`：表明文件为大文件。
* `O_NOATIME`：读取文件时，不更新文件最后的访问时间。
* `O_NONBLOCK`、`O_NDELAY`：将文件描述符设置为非阻塞的
* `O_SYNC`：设置为 IO 同步模式，每次进行写操作时都会将数据同步到磁盘，然后 write 才能返回。
* `O_TRUNC`：在打开文件的时候，将文件长度截断为 0，需要与 O_RDWR 或 O_WRONLY 同时使用。在写文件时，如果是作为新文件重新写入，一定要使用 O_TRUNC 标志，否则可能会造成旧内容依然存在于文件中的错误，如生成配置文件、pid 文件。

打开文件时，内核主要消耗了两种资源：文件描述符与内核管理文件结构 file。

### 2.3 如何选择文件描述符
根据 POSIX 标准，当获取一个新的文件描述符时，要返回最低的未使用的文件描述符。


### 2.4 文件描述符 fd 与文件管理结构 file
暂略。


## 3、creat 函数简介
creat函数用于创建一个新文件，其等价于：
```cpp
open(pathname, O_WRONLY | O_CREAT | O_TRUNC, mode);
```

由于历史原因，早期的 open 第二个参数只能是 0、1、2，这样就无法打开一个不存在的文件。因此一个独立系统调用 creat 被引入，现在的 open 函数通过用 O_CREAT 和 O_TRUNC 选项，可以实现 creat 的功能。

内核 creat 的实现代码如下所示：
```cpp
SYSCALL_DEFINE2(creat, const char __user *, pathname, int, mode)
{
    return sys_open(pathname, O_CREAT | O_WRONLY | O_TRUNC, mode);
}
```

## 4、关闭文件

close 用于关闭文件描述符，可以是普通文件、也可以是设备，还可以是 socket。在关闭时，VFS 会根据不同的文件类型，执行不同的操作。


## 5、文件偏移
文件偏移是基于某个打开文件来说的，一般情况下，读写操作都会从当前的偏移位置开始读写，所以 read 和 write 都没有显式地传入偏移量，并且在读写结束后更新偏移量。

1、**lseek简介**

函数原型：
```cpp
off_t lseek(int fd, off_t offset, int whence);
```

功能：将 fd 的文件偏移量设置为以 whence 为起点，偏移为 offset 的位置。其中 whence 可以为三个值：
* SEEK_SET：表示文件的起始位置
* SEEK_CUR：表示文件的当前位置
* SEEK_END：表示文件的末尾
* offset：取值正负均可
* 返回值：lseek 执行成功后，会返回新的文件偏移量
* 在 Linux 3.1 后，新增两个值：SEEK_DATA 和 SEEK_HOLE，分别用于查找文件中的数据和空洞。

2、**小心 lseek 的返回值**

对于 Linux 大部分系统调用来说，如果返回值是负数，那它一般都是错误的，但是对于 lseek 来说这条规则不适用，具体返回值说明：

* 当 lseek 执行成功后，它会返回最终以文件起始位置为起点的偏移位置，如果出错则返回 -1，同时 errno 被设置为对应的错误值。
* 一般情况下，对于普通文件 lseek 都是返回非负的整数，但是对于某些设备文件来说，是允许返回负的偏移量。因此要向判断出是否真正出错，必须在调用 lseek 前将 errno 重置为 0，然后再调用 lseek，同时检查返回值是否为 -1 及 errno 的值，只有当两个同时成立时，才表明 lseek 真正出错
* 因为文件偏移都是内核的概念，所以 lseek 并不会引起任何真正的 IO 操作。

## 6、读取文件

Linux 中读取文件操作时，最常用的就是 read 函数，其函数原型如下：
```cpp
ssize_t read(int fd, void *buf, size_t count);
```

* 功能：read 尝试从 fd 中读取 count 个字节到 buf 中，并返回成功读取的字节数，同时将文件偏移向前移动相同字节数。
* 返回 0 的时候则表示已经到了`文件尾`。
* read 还有可能读取比 count 小的字节数。

使用 read 进行数据读取时，要注意正确地处理错误：
* read 返回 -1 时，如果 errno 为 EAGAIN、EWOULDBLOCK 或 EINTR，一般情况下都不能视为错误。
* 因为前者是由于当前 fd 为非阻塞且没有可读数据时返回的，后者是由于 read 被信号中断所造成的，这两种情况基本上都视为正常情况。

当 fd 中的数据不够 count 时，read 会返回当前可以读取的字节数？
* 这种行为完全是由具体实现决定的。
* 同为 socket 文件系统，TCP 套接字的读取操作也会与 UDP 不同。当 TCP 的 fd 数据不足时，read 操作极可能会阻塞，而不是直接返回。
* 注：TCP 是否阻塞，取决于当前缓冲区可用数据多少，要读取的字节数，以及套接字设置的接收低水位大小。
* 在调用 read 的时候，只能根据 read 接口的说明，小心处理所有的情况，不能主观臆测内核的实现。


## 7、写入文件
Linux 中写入文件操作，最常用的就是 write 函数，其原型如下：
```cpp
ssize_t write(int fd, const void *buf, size_t count);
```
write 尝试从 buf 指向的地址，写入 count 个字节到文件描述符 fd 中，并返回成功写入的字节数，同时将文件偏移向前移动相同的字节数。write 有可能写入比指定 count 少的字节数。

## 8、文件的原子读写
* 使用 O_APPEND 可以实现在文件的末尾追加新数据，Linux 还提供 pread 和 pwrite 从指定偏移位置读取或写入数据。
* pread 不会从文件表中获取当前偏移，而是直接使用用户传递的偏移量，并且在读取完毕后，不会更改当前文件的偏移量。

## 9、文件描述符的复制
Linux 提供了三个复制文件描述符的系统调用，分别为：
```cpp
int dup(int oldfd);
int dup2(int oldfd, int newfd);
int dup3(int oldfd, int newfd, int flags);
```
* **dup** 会使用一个最小的未用文件描述符作为复制后的文件描述符。
* **dup2** 是使用用户指定的文件描述符 newfd 来复制 oldfd 的。如果 newfd 已经是打开的文件描述符，Linux 会先关闭 newfd，然后再复制 oldfd。
* 对于 **dup3**，只有定义了 feature 宏`_GNU_SOURCE`才可以使用，它比 dup2 多了一个参数，可以指定标志——不过目前仅仅支持 `O_CLOEXEC` 标志，可在 newfd 上设置 `O_CLOEXEC` 标志。定义 dup3 的原因与 open 类似，可以在进行 dup 操作的同时原子地将 fd 设置为 `O_CLOEXEC`，从而避免将文件内容暴露给子进程。

## 10、文件数据的同步

* 为了提高性能，操作系统会对文件的 IO 操作进行缓存处理。
* 对于读操作，如果要读取的内容已经存在于文件缓存中，就直接读取文件缓存。
* 对于写操作，会先将修改提交到文件缓存中，在合适的时机或过一段时间后，操作系统才会将改动提交到磁盘上。

Linux 提供了三个同步接口：
```cpp
void sync(void);
int fsync(int fd);
int fdatasync(int fd);
```
* sync 是阻塞调用。
* fsync 只同步 fd 指定的文件，并且直到同步完成才返回。不仅同步数据，还会同步所有被修改过的文件元数据
* fdatasync 只同步文件的实际数据内容，和会影响后面数据操作的元数据。性能会优于 fsync。

注：sync、fsync 和 fdatasync 只能保证 Linux 内核对文件的缓冲被冲刷了，并不能保证数据被真正写到磁盘上，因为磁盘也有自己的缓存。

## 11、文件的元数据
Linux 环境提供了三个获取文件信息的 API：
```cpp
#include <sys/types.h>
#include <sys/stat.h>
#include <unistd.h>
int stat(const char *path, struct stat *buf);
int fstat(int fd, struct stat *buf);
int lstat(const char *path, struct stat *buf);
```
* stat 得到 path 所指定的文件基本信息
* fstat 得到文件描述符 fd 指定文件的基本信息
* lstat 与 stat 基本相同，只有当 path 是一个链接文件时，lstat 得到的是链接文件本身而不是所指向文件的信息。

所得到的文件基本信息的结果 struct stat 的结构如下：
```cpp
struct stat {
dev_t st_dev;            /* ID of device containing file */
ino_t st_ino;            /* inode number */
mode_t st_mode;          /* protection */
nlink_t st_nlink;        /* number of hard links */
uid_t st_uid;            /* user ID of owner */
gid_t st_gid;            /* group ID of owner */
dev_t st_rdev;           /* device ID (if special file) */
off_t st_size;           /* total size, in bytes */
blksize_t st_blksize;    /* blocksize for file system I/O */
blkcnt_t st_blocks;      /* number of 512B blocks allocated */
time_t st_atime;         /* time of last access */
time_t st_mtime;         /* time of last modification */
time_t st_ctime;         /* time of last status change */
};
```

## 12、文件截断
Linux 提供了两个截断文件的 API：
```cpp
int truncate(const char *path, off_t length);
int ftruncate(int fd, off_t length);
```
* length 可以大于文件本身的大小，这时文件长度将变为 length 的大小，扩充的内容均被填充为 0。
* 我们日常编码中，在写入文件时如果并不需要旧数据，要在打开文件时就要强制截断文件，提高代码的健壮性。

