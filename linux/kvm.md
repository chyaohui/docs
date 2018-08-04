> KVM 是虚拟化技术的一个典型实现，功能非常强大，使用很方便。KVM 本身主要实现对 CPU 的虚拟化，内存和 IO 的虚拟化使用了开源软件 qemu，qemu 是纯软件层面的虚拟化，其实就是个模拟器。KVM 要求 CPU 必须支持硬件虚拟化。

## 零、libvirt 程序
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
