> 上篇文章中总结了如何理解 Linux 系统的平均负载，并展示了在不同场景下如何分析平均负载升高的方法。而多个进程竞争 CPU 会导致进程上下文的频繁切换，进程上下文切换对系统来说是一种不可忽视的开销，频繁切换会占用 CPU 处理任务的有效时间，从而也是导致系统负载升高的一个重要原因。




## 如何理解Linux的上下文切换

* Linux 是一个多任务操作系统，它支持同时运行的任务数量远大于 CPU 个数。其实这些任务没有真正的同时运行，是因为系统在很短的时间内，将 CPU 轮流分配给它们，造成多任务同时运行的错觉。


* 而在每个任务运行前，CPU 都需要知道任务从哪里加载、从哪里开始运行，需要系统事先设置好 `CPU 寄存器`和`程序计数器`。CPU 寄存器是 CPU 内置的容量小、速度极快的内存。而程序计数器则是用来存储 CPU 正在执行的指令位置、或即将执行的下一条指令位置。它们都是 CPU 在运行任务前必须依赖的环境，也被叫做 `CPU 上下文`。


* `上下文切换`，就是先把前一个任务的 CPU 上下文保存起来，然后加载新任务的上下文到这些寄存器和程序计数器，最后再跳到程序计数器所指的新位置，运行新任务。而这些保存下来的上下文，会存储在系统内核中，并在任务重新调度执行时再次加载进来。这样就能保证任务原来的状态不受影响，让任务看起来还是连续运行。


根据任务的不同，CPU 的上下文切换可以分为几个不同的场景，也就是：进程上下文切换、线程上下文切换、中断上下文切换。


## 进程上下文切换

**1、用户空间与内核空间**

Linux 按照特权等级，把进程的运行空间分为内核空间和用户空间，分别对应着 CPU 特权等级的 `Ring 0` 和 `Ring 3`。

* 内核空间(Ring 0)具有最高权限，可以直接访问所有资源。
* 用户空间(Ring 3)只能访问受限资源，不能直接访问内存等硬件设备，必须通过系统调用陷入内核中才能访问这些特权资源。
* 进程既可以在用户空间运行，又可以在内核空间运行。在用户空间运行时被称为进程的用户态，而陷入内核空间的时候，被称为进程的内核态。


**2、系统调用**

从用户态到内核态的转变，需要通过`系统调用`来完成。比如查看文件时，需要执行多次系统调用：open、read、write、close等。系统调用的过程如下：

* 首先，把 CPU 寄存器里原来用户态的指令位置保存起来
* 为了执行内核代码，CPU 寄存器需要更新为内核态指令的新位置，最后跳转到内核态运行内核任务。
* 系统调用结束后，CPU 寄存器需要恢复原来保存的用户态，然后再切换到用户空间，继续运行进程
* 所以，一次系统调用的过程，其实是发生了两次 CPU 上下文切换。

但系统调用的过程中并不会涉及虚拟内存等进程用户态的资源，也不会切换进程，这和平时说的进程上下文切换是不一样的：

* 进程上下文切换，是指从一个进程切换到另一个进程运行
* 系统调用过程中一直是同一个进程在运行

因此，系统调用的过程通常称为特权模式切换，而不是上下文切换。


**3、进程上下文切换**

进程是由内核来管理和调度的，进程的切换只能发生在内核态，因此进程的上下文不仅包括了虚拟内存、栈、全局变量等用户空间的资源，还包括了内核堆栈、寄存器等内核空间的状态。

因此进程的上下文切换就比系统调用时多了一步：在保存当前进程的内核状态和 CPU 寄存器之前，需先把该进程的虚拟内存、栈等保存下来；而加载了下一进程的内核态后，还需要刷新进程的虚拟内存和用户栈。

保存上下文和恢复上下文的过程并不是`免费`的，需要内核在 CPU 上运行才能完成。据测试，每次上下文切换都需要几十纳秒到数微妙的 CPU 时间。特别是在进程上下文切换次数较多的情况下，很容易导致 CPU 将大量时间消耗在寄存器、内核栈、虚拟内存等资源的保存和恢复上，从而大大缩短了真正运行进程的时间。

Linux 通过 TLB 来管理虚拟内存到物理内存的映射关系。当虚拟内存更新后，TLB 也需要刷新，内存的访问也会随之变慢。特别是多处理器系统上，缓存是被多个处理器共享的，刷新缓存不仅会影响当前处理器的进程，还会影响共享缓存的其它处理器的进程。


**4、进程上下文何时切换**

Linux 为每个 CPU 维护了一个就绪队列，将活跃进程按照优先级和等待 CPU 的时间排序，然后选择最需要 CPU 的进程，也就是优先级最高和等待 CPU 时间最长的进程来运行。那么，进程在什么时候才会被调度到 CPU 上运行呢？

* 进程执行完终止了，它之前使用的 CPU 会释放出来，这时再从就绪队列中拿一个新的进程来运行
* 为了保证所有进程可以得到公平调度，CPU 时间被划分为一段段的时间片，这些时间片被轮流分配给各个进程。当某个进程时间片耗尽了就会被系统挂起，切换到其它等待 CPU 的进程运行。
* 进程在系统资源不足时，要等待资源满足后才可以运行，这时进程也会被挂起，并由系统调度其它进程运行。
* 当进程通过睡眠函数 sleep 主动挂起时，也会重新调度。
* 当有优先级更高的进程运行时，为了保证高优先级进程的运行，当前进程会被挂起，由高优先级进程来运行。
* 发生硬件中断时，CPU 上的进程会被中断挂起，转而执行内核中的中断服务程序。


## 线程上下文切换

线程与进程最大的区别在于，线程是操作系统调度的最小单位，而进程是操作系统分配资源的最小单位。所谓内核调度，实际上的调度对象是线程，而进程只是给线程提供了虚拟内存、全局变量等资源。对于线程和进程我们可以这么理解：

* 当进程只有一个线程时，可以认为进程就等于线程
* 当进程拥有多个线程时，这些线程会共享相同的虚拟内存和全局变量等资源。这些资源在上下文切换时是不需要修改的。
* 另外线程也有自己的私有数据，比如栈和寄存器等，这些在上下文切换时也时需要保存的。

其实线程的上下文切换可以分为两种情况：

* 前后两个线程属于不同进程。此时因为资源不共享，所以切换过程就跟进程上下文切换是一样的。
* 前后两个线程属于同一个进程。此时虚拟内存是共享的，上下文切换时，虚拟内存这些资源保持不动，只需要切换线程的私有数、寄存器等不共享的数据。

可以发现同进程内的线程切换，要比多进程间的切换消耗更少的资源，这也正是多线程代替多进程的一个优势。


## 中断上下文切换

为了快速响应硬件的事件，中断处理会打断进程的正常调度和执行，转而调用中断处理程序，响应设备事件。而在打断其它进程时，就需要将进程当前的状态保存下来，这样在中断结束后，进程仍然可以从原来的状态恢复运行。

* 跟进程上下文不同，中断上下文切换并不涉及到进程的用户态。所以即便中断过程打断了一个正在用户态的进程，也不需要保存和恢复这个进程的虚拟内存、全局变量等用户态资源。中断上下文其实只包括内核态中断服务程序执行所必需的状态，包括 CPU 寄存器、内核堆栈、硬件中断参数等。

* 对同一个 CPU 来说，中断处理比进程拥有更高的优先级，由于中断会打断正常进程的调度和执行，所以大部分中断处理程序都短小精悍，以便尽可能快的执行结束。

* 跟进程上下文切换一样，中断上下文切换也需要消耗 CPU，当发现中断次数过多时，就需要注意去排查它是否会给你的系统带来严重的性能问题。


## 概念小结

总结一下，不管是哪种场景导致的上下文切换，你都应该知道：

* CPU 上下文切换是保证 Linux 系统正常工作的核心功能之一，一般情况下我们无需特别关注。
* 过多的上下文切换，会把 CPU 时间消耗在寄存器、内核栈、虚拟内存等数据的保存和恢复上，从而缩短进程真正运行的时间，导致系统的整体性能大幅下降。


## 如何查看系统的上下文切换

我们可以通过 vmstat 工具来查看系统的上下文切换情况。vmstat 主要用来分析系统内存使用情况，也常用来分析 CPU 上下文切换和中断的次数。
```bash
# 每隔 5 秒输出 1 组数据
$ vmstat 5
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b  swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 0  0    0 7005360  91564 818900    0    0     0     0   25   33  0  0 100  0  0
```
我们需要重点关注下列四项内容：

* cs(context switch) 是每秒上下文切换的次数。
* in(interrupt) 是每秒中断的次数。
* r(Running or Runnable) 是就绪队列的长度，也就是正在运行和等待 CPU 的进程数。
* b(Blocked) 是处于不可中断睡眠状态的进程数。


想要查看每个进程的详细情况，需要使用 pidstat，给它加上 `-w` 选项，就可以查看每个进程上下文切换的情况。
```bash
# 每隔 5 秒输出 1 组数据
$ pidstat -w 5
Linux 4.15.0 (ubuntu)  09/23/18  _x86_64_  (2 CPU)
08:18:26      UID       PID   cswch/s nvcswch/s  Command
08:18:31        0         1      0.20      0.00  systemd
08:18:31        0         8      5.40      0.00  rcu_sched
```
上述结果有两列是我们重点关注的对象，一个是 cswch，表示每秒自愿上下文切换的次数；另一个是 nvcswch，表示每秒非自愿上下文切换的次数。

* 自愿上下文切换，是指进程无法获取所需资源，导致的上下文切换。比如，IO、内存等系统资源不足时，就会发生自愿上下文切换。
* 非资源上下文切换，则是指进程由于时间片已到等原因，被系统强制调度，进而发生的上下文切换。比如说，大量进程都在抢占 CPU 时，就容易发生非自愿上下文切换。


## 案例分析

**准备环境**

sysbench 是一个多线程的基准测试工具，一般用来评估不同系统参数下的数据库负载情况，本次案例把它当作一个异常进程来看，作用是模拟上下文切换过多的问题。

```bash
# 预先安装 sysbench
$ yum install sysbench -y
```

**操作和分析**

首先在第一个终端里运行 sysbench，模拟系统多线程调度的瓶颈：
```bash
# 以 10 个线程运行 5 分钟的基准测试，模拟多线程切换的问题
$ sysbench --threads=10 --max-time=300 threads run
```

接着在第二个终端运行 vmstat，观察上下文切换情况：
```bash
# 每隔 1 秒输出 1 组数据（需要 Ctrl+C 才结束）
$ vmstat 1
procs --------memory-------- ---swap-- -----io---- -system-- ------cpu-----
 r  b swpd   free   buff  cache  si so bi bo   in   cs us sy id wa st
 6  0  0 6487428 118240 1292772    0   0  0  0 9019 1398830 16 84  0  0  0
 8  0  0 6487428 118240 1292772    0   0  0  0 10191 1392312 16 84  0  0  0
```

可以发现，cs 列的上下文切换次数从之前的 35 上升到了 139 万，观察其他几个指标：
* r 列：就绪队列长度为 8，远大于 CPU 个数，所以会有大量的 CPU 竞争
* us 和 sys 列：这两列加一起上升到 100%，sys 列高达 84%，说明 CPU 主要是被内核占用了。
* in 列：中断次数为 1 万左右，说明中断也是个潜在的问题。

综合分析，由于系统的就绪队列过长，也就是正在运行和等待 CPU 的进程数过多，导致了大量的上下文切换，而上下文切换又导致了系统 CPU 的占用率升高。


我们可以使用 pidstat 继续分析到底是哪个进程导致了这些问题？
```bash
# 每隔 1 秒输出 1 组数据（需要 Ctrl+C 才结束）
# -w 参数表示输出进程切换指标，而 -u 参数则表示输出 CPU 使用指标
$ pidstat -w -u 1
08:06:33      UID       PID    %usr %system  %guest   %wait    %CPU   CPU  Command
08:06:34        0     10488   30.00  100.00    0.00    0.00  100.00     0  sysbench
08:06:34        0     26326    0.00    1.00    0.00    0.00    1.00     0  kworker/u4:2

08:06:33      UID       PID   cswch/s nvcswch/s  Command
08:06:34        0         8     11.00      0.00  rcu_sched
08:06:34        0        16      1.00      0.00  ksoftirqd/1
08:06:34        0       471      1.00      0.00  hv_balloon
08:06:34        0      1230      1.00      0.00  iscsid
08:06:34        0      4089      1.00      0.00  kworker/1:5
08:06:34        0      4333      1.00      0.00  kworker/0:3
08:06:34        0     10499      1.00    224.00  pidstat
08:06:34        0     26326    236.00      0.00  kworker/u4:2
08:06:34     1000     26784    223.00      0.00  sshd
```

可以发现，CPU 使用率的升高是 sysbench 导致的，但上下文切换则来自其他进程，包括非自愿上下文切换频率最高的 pidstat，以及自愿上下文切换频率最高的内核线程 kworker 和 sshd。


默认 pidstat 显示进程的指标数据，加上 `-t` 参数后，才会输出线程的指标
```bash
# 每隔 1 秒输出一组数据（需要 Ctrl+C 才结束）
# -wt 参数表示输出线程的上下文切换指标
$ pidstat -wt 1
08:14:05      UID      TGID       TID   cswch/s nvcswch/s  Command
...
08:14:05        0     10551         -      6.00      0.00  sysbench
08:14:05        0         -     10551      6.00      0.00  |__sysbench
08:14:05        0         -     10552  18911.00 103740.00  |__sysbench
08:14:05        0         -     10553  18915.00 100955.00  |__sysbench
08:14:05        0         -     10554  18827.00 103954.00  |__sysbench
...
```

虽然 sysbench 进程的上下文切换次数不多，但它的子线程的上下文切换次数非常多，可以判定上下文切换罪魁祸首的是 sysbench 进程。还没完，记得我们通过 vmstat 看到的中断次数到了 1 万，到底是什么类型的中断上升了呢？


我们可以通过 /proc/interrupts 来读取中断的使用情况，通过运行下面的命令：
```bash
# -d 参数表示高亮显示变化的区域
$ watch -d cat /proc/interrupts
           CPU0       CPU1
...
RES:    2450431    5279697   Rescheduling interrupts
...
```

可以发现，变化速度最快的是`重调度中断(RES)`，表示唤醒空闲状态的 CPU 来调度新的任务运行。这是多处理器系统(SMP)中，调度器用来分散任务队列到不同 CPU 的机制，通常也被称为`处理器间中断`。根本原因还是因为过多任务的调度问题，跟前边分析结果是一致的。


## 每秒上下文切换多少次算正常

这个数值其实取决于系统本身的 CPU 性能。如果系统的上下文切换次数比较稳定，从数百到一万以内，都应该算是正常的。如果当上下文切换次数超过一万次，或者切换次数出现数量级增长时，很可能已经出现了性能问题。

这时，你还需要根据上下文切换的类型，再做具体分析，比方说：

* 自愿上下文切换变多了，说明进程都在等待资源，有可能发生了 IO 等其他问题
* 非自愿上下文切换变多了，说明进程都在被强制调度，也就是都在争抢 CPU，说明 CPU 的确成了瓶颈。
* 中断次数变多了，说明 CPU 被中断处理程序占用，还需要通过查看 `/proc/interrupts` 文件来分析具体的中断类型。
