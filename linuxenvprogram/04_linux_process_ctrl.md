> 进程是操作系统的一个核心概念，每个进程都有自己唯一的标识：进程 ID，也有自己的生命周期。

## 1、进程ID
每个进程都会有一个非负整数表示的唯一进程 ID，可以使用 getpid 函数来获取进程的 pid。每个进程都有自己的父进程，父进程又会有自己的父进程，最终都会追溯到 1 号 init 进程。


procfs 文件系统会在 /proc 下为每个进程创建一个目录，名字是该进程的 pid，目录下有很多文件，用于记录进程的运行情况和统计信息。

进程 ID 是唯一的，但进程 ID 可以重用，内核采用了延迟重用的算法，这样可以防止将新创建的进程判为使用相同进程 ID 的已经退出的进程。

那么如何实现延迟重用呢？内核采用的方法如下：
* 位图记录进程 ID 的分配情况 (0 为可用，1 为已占用)
* 将上次分配的进程 ID 记录到 last_pid 中，分配进程 ID 时，从 last_pid+1 开始找起，从位图中寻找可用的 ID。
* 如果找到位图集合的最后一位仍不可用，则回滚到位图集合的起始位置，从头开始找(从 300 开始，300 以内保留系统使用)。

可以通过 procfs 或 sysctl 命令来查看 pid_max 值：
```sh
$ cat /proc/sys/kernel/pid_max
$ sysctl kernel.pid_max

# 可以调整pid_max的值，但内核也设置了硬上限
$ sysctl -w kernel.pid_max=4194304
```


## 2、进程的层次
新进程默认会继承父进程的进程组 ID 和 会话 ID。进程组和会话是为了支持 shell 作业控制而引入的概念。当有新的用户登录 shell 时，登录进程会为这个用户创建一个会话，用户的登录 shell 就是会话的首进程，作为整个会话的 ID。


1、**进程组**

引入进程组概念，可以更方便管理这一组进程，比如这项工作放弃了，不必向每个进程一一发信号，可以直接将信号发送个进程组，组内的所有进程都会收到信号。

* 后台进程组："&"
* 前台进程组，只有一个前台进程组


2、**会话**

会话的意义在于将很多的工作囊括在一个终端，选取其中一个作为前台来直接接收终端的输入及信号，其它的工作则放在后台执行。

系统提供 setsid 函数来创建会话，要求函数的调用进程不是进程组组长，会发生如下事情：
* 创建一个新会话，会话 ID 等于进程 ID，调用进程成为会话的首进程。
* 创建一个进程组，进程组 ID 等于进程 ID，调用进程成为进程组的组长。
* 调用进程没有控制终端，如果调用前有控制终端，则这种联系会断掉。

为何？如果允许进程组组长迁移到新的会话，而进程组的其它成员仍然在老的会话中，就会出现同一进程组的进程分属不同的会话，破坏了进程组和会话的严格层次关系。

## 3、创建进程fork
Linux 中可以调用 fork 函数来创建新的进程，fork 函数会返回两次。一般创建两个完全相同的进程并没有太大意义，大部分情况下，父子进程会执行不同的代码分支。fork 函数向子进程返回 0，并将子进程的进程 ID 返回给父进程，返回失败则返回 -1，并设置 errno。

**坑**：如果 fork 返回 -1，而程序没有判断返回值，直接将 -1 当成子进程的进程号，那么后面的代码执行 kill(-1, 9) 将杀死除了 init 以外的所有进程，只要它有权限。

POSIX 标准和 Linux 都没有保证优先调度父进程，决不能对父子进程的执行顺序作任何的假设，如果确实需要某一特定顺序，那么需要使用进程间同步的手段。


fork之后的子进程完全拷贝了父进程的地址空间，包括栈、堆、代码段等。写时拷贝是指子进程的页表指向与父进程相同的物理内存页，这些页面标记为只读，一旦父子进程中有任何一方尝试修改，就会引发缺页异常，内核会尝试为该页面创建一个新的物理页面，并将内容真正地复制到新的物理页中，让父子进程真正个子拥有自己的物理内存页，然后将页表中相应的表项标记为可写。


## 2、fork父子进程与文件的关系
执行 fork 函数，内核会复制父进程所有的文件描述符。对于父进程打开的所有文件，子进程也可以操作的，无论读写，父子进程公用一套文件偏移量。

父子进程同时读写同一个文件描述符而不采取任何同步的手段，那么就会因为共享文件偏移量而使数据混乱，不可阅读。

**场景**：一般来讲，调用 exec 的子进程时，父进程打开的文件描述符应该一并关闭，内核事实上并没有主动这样做。试想 Webserver 首先以 root 权限启动，打开只有拥有 root 权限才能打开的端口和日志等文件，再降到普通用户，fork 出一些 worker 进程，在进程中解析脚本、写日志、输出结果等操作。由于子进程完全可以操作父进程打开的文件，因此子进程中的脚本只要继续操作这些文件描述符，就能越权操作 root 用户才能操作的文件。

> 为了解决这个问题，Linux 引入了 close on exec 机制，设置 FD_CLOSEXEC 标志位的文件，在子进程中调用 exec 家族函数时将相应的文件关闭，设置该标志位的方法有两种：

* open 时，带上 O_CLOSEXEC 标志位 - 推荐
* open 时如果未设置，那就在后面调用 fcntl 函数的 F_SETFD 操作来设置。


## 4、进程创建vfork
早期 fork 没有实现写时拷贝的机制，而是直接对父进程的数据段、堆和栈进行完全拷贝，效率十分低下。很多程序 fork 后执行 exec 家族函数，这更是一种浪费，因此引入 vfork 压根不会拷贝父进程的内存数据，而是直接共享。后来引入写时拷贝的机制，其效率提高了很多，这样 vfork 其实就可以退出历史舞台了，除了一些需要将性能优化到极致的场景，大部分情况不需要使用 vfork 函数。

vfork 创建子进程，共享父进程的内存数据，而系统将保证子进程先于父进程获得调度，父进程将被一直挂起，直到子进程退出或执行 exec。

参考左耳朵耗子的一篇文章《[vfork 挂掉的一个问题](https://coolshell.cn/articles/12103.html)》。



## 5、daemon进程的创建

* **执行 fork() 函数，父进程退出，子进程继续**。
  - 父进程有可能是进程组的组长(不能执行后面要执行的 setsid 函数)，子进程继承了父进程的进程组 ID，并且拥有了自己的进程 ID，一定不会是进程组的组长，所以子进程一定是可以执行后面要执行的 setsid 函数。
  - 如果 daemon 是从终端命令行启动的，那么父进程退出会被 shell 检测到，shell 会显示提示符，让子进程在后台运行。

* **修改进程的当前目录为根目录(/)**。如果当前工作路径上包含有除根文件系统以外的其它文件系统，那么这些文件系统将无法卸载，一般做法是将当前工作目录切换成根目录。

* **调用 setsid 函数**。切断与控制终端的所有关系，并创建一个新的会话。无论终端是否发送 SIGINT、SIGQUIT、SIGTSTP 信号，也无论终端是否断开，都与要创建的 daemon 进程无关。

* **设置文件模式创建掩码为0**。为了让 daemon 进程创建文件的权限属性与 shell 脱离关系。

* **再次执行 fork，父进程退出，子进程继续**。为什么再次 fork 呢？原因是 daemon 进程有可能会打开一个终端设备，为了确保万无一失，只有确保 daemon 进程不是会话的首进程，才能保证打开的终端设备不会成为控制终端。

* **关闭 stdin/stdout/stderr**。默认文件描述符 0、1、2 指向的就是控制终端，此时 daemon 进程与控制终端已无关联，因此这三者都没有意义。关闭后可以防止后面的程序在文件描述符 0、1、2 上执行 IO 库函数而导致报错。

注：glibc 提供了 daemon 函数，但是它并没有执行第二次的 fork。


## 6、进程的终止

在不考虑线程的情况下，进程的退出有以下 5 种方式。
* 正常退出有 3 种：
  - 从 main 函数 return 返回
  - 调用 exit
  - 调用 _exit
* 异常退出有 2 种：
  - 调用 abort
  - 接收到信号，由信号终止

1、**_exit函数**

* _exit() 函数中 status 参数定义了进程终止状态，父进程可以通过 wait() 来获取该状态值。
* 虽然 status 是 int 型，但是仅有低 8 位可以被父进程所用，所以写 exit(-1) 结束进程时，在终端执行 "$?" 会发现返回值是 255。
* 如果是 shell 相关编程，那么退出值最好不要大于 128。

POSIX 标准规定了退出状态及其含义：
|值|含义|
---|--
|0|命令成功执行并退出|
|1~125|命令未成功地退出，具体含义由个子的命令来定义|
|126|命令找到了，文件无法执行|
|127|命令找不到|
|>128|命令因收到信号而死亡|


2、**exit函数**

exit() 函数的最后也会调用 _exit() 函数，但是 exit 在调用 _exit 之前，还做了其它工作：
* 执行用户通过调用 atexit 函数或 on_exit 定义的清理函数。
* 关闭所有打开的流，所有缓冲的数据均被写入，通过 tmpfile 创建的临时文件都会被删除
* 调用 _exit，走向内核清理。

3、**return退出**
调用 main() 的运行时函数会将 main 的返回值当作 exit 的参数。


## 7、等待子进程
1、**僵尸进程**

* **产生僵尸进程**。进程退出会进行内核清理，也就是释放进程所有的资源，包括：内存、文件、信号量、共享内存，或者引用计数减一，或者彻底释放。不过进程的退出没有将所有的资源完全释放，比如进程的 PID 依然占用，此时进程不可运行(没有地址空间)，进程进入僵尸状态。

* **为何需要僵尸进程**。僵尸进程依然保留的资源有进程控制块 task_struct、内核栈等，可以为父进程提供一些信息，如进程为何退出、是否正常退出、退出码、消耗CPU时间、收到的信号、上下文切换、最大内存驻留集、产生多少缺页中断。父进程收集了这些信息后，就可以释放了，进程就脱离了僵尸状态，彻底消失了。

* **如何查看进程是否是处于僵尸状态**。ps 命令输出的进程状态 Z，就表示进程处于僵尸状态。另 procfs 提供的 status 信息中的 State 给出的值是 Z，也表示进程处于僵尸状态。

* **清理僵尸进程的方法**：
  - 父进程调用 wait 函数，为子进程 "收尸"
  - 父进程退出，init 进程会为子进程 "收尸"

* **浪费资源**。系统不希望大量进程长期处于僵尸状态，因为会浪费系统资源。除了少量的内存资源外，比较重要的是进程 ID。僵尸进程并没有将自己的进程 ID 归还给系统，而是依然占有这个进程 ID，因此系统不能将该 ID 分配给其他进程。

* **如果不关心子进程的退出状态**。将父进程对 SIGCHLD 的处理函数设置为 SIG_IGN，或在调用 sigaction 函数时设置为 SA_NOCLDWAIT 标志位。都会明确告诉子进程，不会为子进程 "收尸"。子进程退出时，内核检查到父进程设置的标志位，不会进入僵尸状态，而是调用 release_task 函数直接释放该子进程。

* **如果关心子进程的退出信息**。在应该在流程上妥善设计，能够及时调用 wait，使子进程处于僵尸状态的时间不会太久。

2、**等待子进程wait**

wait 函数的局限性：
* 不能等待特定的子进程。
* 如果不存在子进程退出，wait() 只能阻塞。
* wait() 函数只能发现子进程的终止事件，不能探知子进程的暂停和恢复执行。


3、**等待子进程waitpid**

```cpp
pid_t waitpid(pid_t pid, int *status, int options);
```
waitpid 与 wait 函数相同的地方：
* 返回值含义相同，都是终止子进程或因信号停止或因信号恢复而执行的子进程的进程 ID。
* status 的含义相同，都是用来记录子进程的相关事件。

waitpid 函数可以明确指定要等待哪一个子进程的退出，扩展的功能不仅仅如此：
* pid > 0: 表示等待进程 ID 为 pid 的子进程。
* pid = 0: 表示等待与调用进程同组的任意子进程。
* pid =-1: 表示等待任意子进程，同 wait 类似。
* pid <-1: 等待所有子进程中，进程组 ID 与 pid 绝对值相等的所有子进程。


waitpid 函数的第三个参数 options 是一个位掩码，可以同时存在多个标志。
* WUNTRACE：除了关心终止子进程的信息，也关心那些因信号而停止的子进程信息。
* WCONTINUED：除了关心终止子进程的信息，也关心那些因收到信号而恢复执行的子进程的状态信息。
* WNOHANG：指定的子进程并未发生状态变化，立刻返回，不会阻塞。这种情况下返回值是 0。

Linux提供了SIGSTOP（信号值19）和 SIGCONT（信号值18）两个信号，来完成暂停和恢复的动作，可以通过执行 `kill-SIGSTOP` 来暂停一个进程的执行，通过执行 `kill -SIGCONT` 来让一个暂停的进程恢复执行。

waitpid 成功返回的时候，可能是等到了子进程的终止事件，也可能是等到了暂停或恢复执行的事件，这需要通过 status 的值来区分。

|进程的退出类型|宏|说明|
---|---|---
|正常退出|WIFEXITED(status)|如果子进程正常退出，则返回True，否则返回False|
|正常退出|WEXITSTATUS(status)|如果子进程正常退出，用来获取进程的退出状态|
|收到信号退出|WIFSIGNALED(status)|如果进程是被信号杀死，返回True，否则返回False|
|收到信号退出|WTREMSIG(status)|如果进程是被信号杀死的，则返回杀死进程的信号值|
|收到信号退出|WCOREDUMP(status)|如果子进程产生了 coredump，则返回True，否则False|
|收到信号停止|WIFSTOPPED(status)|如果子进程收到相关信号，暂停执行，则返回True|
|收到信号停止|WSTOPSIG(status)|如果子进程处于停机状态，则返回导致子进程停止的信号的值|
|子进程恢复执行|WIFCONTINUED(status)|如果由于SIGCONT信号，子进程恢复执行，则返回True|

如果用户不关心进程的终止事件，只关心进程的停止事件，则不能通过 waitpid 做到，为了解决这个缺陷，wait 家族的最重要成员，`waitid()` 函数。


## 8、exec家族
整个 exec 家族有 6 个函数，这些函数都是构建在 execve 系统调用之上的。该系统调用作用是，将新程序加载到进程的地址空间，丢弃旧的程序，进程的栈、数据段、堆栈等会被新程序替换。

1、**execve函数**

```cpp
int execve(const char *filename, char *const argv[], char *const envp[]);

// 示例代码
char *args[] = {"/bin/ls", "-l",NULL};
if(execve("/bin/ls",args, NULL) == -1) {
    perror("execve");
    exit(EXIT_FAILURE);
}
```
无需检查返回值，只要返回，必然是 -1。可以从 errno 判断出错的原因，常见的情况有以下几种：
* EACCESS：第一个参数 filename，不是普通文件，或该文件没有可执行的权限。
* ENOENT：文件不存在。
* ETXTBSY：存在其它进程尝试修改 filename 所指代的文件。
* ENOEXEC：文件存在，也可执行，但是无法执行，比如 Windows 下的可执行程序。


2、**exec家族**
从内核角度来说，提供 execve 系统调用就足够了，但是从应用层编程的角度来讲，execve 函数就不那么好使了。
* 第 1 个参数必须是绝对路径或当前工作目录的相对路径，不能使用 PATH。
* 第 3 个参数是环境变量指针数组，得自己负责维护环境变量

```cpp
int execl(const char *path, const char *arg, ...);
int execlp(const char *file, const char *arg, ...);
int execle(const char *path, const char *arg, ..., char * const envp[]);
int execv(const char *path, char *const argv[]);
int execvp(const char *file, char *const argv[]);
int execve(const char *path, char *const argv[], char *const envp[]);
```

六个函数本质上都是调用 execve 函数，分为上下两个半区，分类依据是参数采用列表(l, 表示list)还是数组(v, 表示vector)。带 p 的表示可以使用环境变量 PATH，带 e 的表示要自己维护环境变量，而不使用当前的环境变量。

|函数名|参数格式|是否自动搜索PATH|是否使用当前环境变量|
---|---|---|---
|execl|列表|不是|是|
|execlp|列表|是|是|
|execle|列表|不是|不是，需自己组装环境变量|
|execv|数组|不是|是|
|execvp|数组|是|是|
|execve|数组|不是|不是，需自己组装环境变量|


3、**执行exec后进程继承的属性**

调用 exec 之后进程保持的属性：

|属性|相关的函数|属性|相关的函数|
---|---|---|---
|进程ID|getpid|根目录| |
|父进程ID|getppid|文件模式创建掩码|umask|
|进程组ID|getpgid|文件锁和记录锁|flock 和 fcntl|
|会话ID|getsid|进程信号屏蔽|sigprocmask|
|控制终端|tcgetpgrp|进程挂起的信号|sigpending|
|真实用户ID|getsid|已用的时间|times|
|真实组ID|getgid|资源限制|getrlimit、setrlimit|
|附加组ID|getgroups|nice值|nice|
|告警剩余时间|alarm|semadj值|semop|
|当前工作目录|getcwd| | |



## 9、system函数

1、**system函数接口**

system 函数的缺点是很明显的。首先是效率，一般要创建两个进程，一个是 shell 进程，另外一个或多个是用于 shell 所执行的命令，如果对效率要求较高，最好是自己直接调用 fork 和 exec 来执行既定的程序。

system 函数的返回值：
* 当 command 为 NULL 时，返回 0 或 1
* 创建进程失败，或获取子进程终止状态失败，则返回 -1。
* 如果子进程不能执行 shell，那么 system 返回值会与 _exit(127)终止时一样
* 如果所有的系统调用都执行成功，system 函数就会返回执行 command 的子 shell 的终止状态。
