> KVM 是虚拟化技术的一个典型实现，功能非常强大，使用很方便。KVM 本身主要实现对 CPU 的虚拟化，内存和 IO 的虚拟化使用了开源软件 qemu，qemu 是纯软件层面的虚拟化，其实就是个模拟器。KVM 要求 CPU 必须支持硬件虚拟化。

## 0、关于 libvirt 程序
说到 kvm 必须提及 libvirt 程序集，它是用来管理 kvm 虚拟机的，当然其实也可以管理 xen 等其它虚拟化的虚拟机。libvirt 包括三部分：
* libvirtd 是后台服务程序；
* libvirt 是管理虚拟机的 API 接口，可以通过 python c java 等语言来编写程序管理虚拟机，比较典型的 virt-manager 就是使用 python 写的可视化工具；
* virsh 等命令行管理工具。

## 一、环境准备
1、**实验环境**
* 操作系统：CentOS 6.4 x86_64 mini
* 宿主机：vmware workstation 虚拟机

2、**检查宿主机处理器是否支持虚拟化**
```sh
[root@kvm ~]$ egrep -o 'vmx | svm' /proc/cpuinfo | wc -l
```
如果显示数值是 0，则表示该 CPU 不支持虚拟化。

3、**配置或设置宿主机**
* CPU：2-4 core  开启 cpu 虚拟化(bios 设置 或 vmware 设置)
* 内存：4-8 GB
* 硬盘：100 GB

4、**关闭 iptables 和 selinux**

关闭 iptables 服务：
```sh
[root@kvm ~]$ service iptables stop
[root@kvm ~]$ chkconfig iptables off
```
关闭 selinux：
```sh
[root@kvm ~]$ setenforce 0
[root@kvm ~]$ vi /etc/selinux/config
SELINUX=disabled
```

## 二、安装和配置 KVM 环境
1、**安装 kvm 虚拟化相关软件包**
```sh
[root@kvm ~]$ yum install -y kvm virt-* libvirt bridge-utils qemu-img
```

2、**查看 KVM 模块是否加载到内核**
```sh
[root@kvm ~]$ lsmod | grep kvm_intel
kvm_intel              53484  0
kvm                   316506  1 kvm_intel
```
如果没有加载，可以尝试执行命令：`modprobe kvm_intel` ，不行的话，试试重启宿主机。

3、**设置相关网络**
* 设置方式一：网桥模式。
```sh
[root@kvm ~]$ cd /etc/sysconfig/network-scripts/
[root@kvm ~]$ cp ifcfg-eth0 ifcfg-br0
[root@kvm ~]$ vi ifcfg-eth0
DEVICE=eth0
TYPE=Ethernet
ONBOOT=yes
BOOTPROTO=none
BRIDGE=br0

[root@kvm ~]$ vi ifcfg-br0
DEVICE=br0
TYPE=Bridge
ONBOOT=yes
BOOTPROTO=static
IPADDR=172.16.1.8
PREFIX=24
GATEWAY=172.16.1.1
DNS1=114.114.114.114
NAME=br0

# 重启网络
[root@kvm ~]$ service network restart
```

* 设置方式二：NAT 模式。
  
  暂略。

4、**启动 libvirtd 相关服务**
```sh
[root@kvm ~]$ /etc/init.d/libvirtd start
[root@kvm ~]$ /etc/init.d/messagebus restart
```
遇到错误：
```sh
[root@vip ~]$ /etc/init.d/libvirtd start
libvirtd: relocation error: libvirtd: ... version ... libdevmapper.so.1.02 [失败]
```
解决：
```sh
[root@kvm ~]$ yum upgrade device-mapper-libs
```
结果：
```sh
[root@kvm ~]# brctl show
bridge name    bridge id           STP enabled    interfaces
br0         8000.000c29181c75   no          eth0
virbr0      8000.525400c207c7   yes         virbr0-nic
```