## ip 命令
> ip 命令用来显示或设置 Linux 主机的路由、网络设备、策略路由和隧道，是 Linux 下较新的功能强大的网络配置工具。

* 命令使用格式：
```
ip [ OPTIONS ] OBJECT { COMMAND | help }
```

* OBJECT 操作对象：
```
{ link | addr | addrlabel | route | rule | neigh | tunnel | maddr | mroute | monitor }
```

* 选项
```sh
-V[ersion]：显示指令版本信息；
-s[tatistics]：输出更详细的信息；
-f[amily]：强制使用指定的协议族；值：`inet | inet6 | ipx | dnet | link`;
-o[neline]：输出信息每条记录输出一行，即使内容较多也不换行显示；
-r[esolve]：显示主机时，不使用IP地址，而使用主机的域名。
```

1、**链路 link 类常用命令**
```sh
ip link set eth0 up/down            # 设置 eth0 网卡为 Down/Up 状态
ip link set eth0 arp on/off         # 设置 eth0 网卡的 NOARP 选项
ip link set eth0 promisc on/off     # 设置 eth0 网卡的混杂模式
ip link set eth0 mtu 1500           # 设置 eth0 网卡的 MTU 值
ip link show eth0                   # 显示 eth0 网卡的 link 信息
```


2、**地址 addr 类常用命令**


ip addr
ip link
ip route
ip rule



