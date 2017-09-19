# pktgen-dpdk

## 一、介绍说明
pktgen-dpdk是一个基于dpdk的高性能的收发包工具。

### 关于版本
* dpdk版本
```DPDK 16.11.0```
* pktgen版本
```Pktgen Ver: 3.0.16```


##　二、DPDK环境部署及设置
### 1) dpdk的编译安装
... ...

### 2) dpdk的环境设置
1. 配置大内存页(手动配置)
```bash
$ echo 2048 > /sys/kernel/mm/hugepages/hugepages-2048kB/nr_hugepages
$ mkdir /mnt/huge
$ mount -t hugetlbfs nodev /mnt/huge/
```
注: 参考pktgen/setup.sh脚本文件, 脚本内容有其它设置方法

2. 安装igb_uio驱动
```bash
$ cd dpdk
$ modprobe uio
$ rmmod igb_uio
$ insmod x86_64-native-linuxapp-gcc/kmod/igb_uio.ko
```

3. 绑定网卡到igb_uio驱动
```bash
$ lspci | grep -i ethernet
$ ./tools/dpdk-devbind.py -b igb_uio 00:09.0 00:0a.0 00:0b.0
```


### 命令行运行pktgen-dpdk
```bash
$ cd pktgen
$ ./app/app/x86_64-native-linuxapp-gcc/pktgen -c 0xf -n 1 \
        --proc-type auto --socket-mem 1024 --huge-dir /mnt/huge \
        --file-prefix pg -- -P -T -m '1.0,2.1,3.2'
```
