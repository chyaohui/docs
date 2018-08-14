> ipvsadm 使用手册

## 一、ipvsadm常用命令
### 1、在LVS上新增service
命令格式：
```
ipvsadm -A -t <vip>:<vport> -s <schedule: rr|wrr|lc|wlc|lblc|lblcr|dh|sh|sed|nq>
```
示例：
```sh
# 在 LVS 中为 http 协议添加一个 VIP 为 1.1.1.1 的 service, 并设置调度方式为 Round Robin
$ ipvsadm -A -t 1.1.1.1:80 -s rr
```

### 2、在LVS上修改存在的Service
命令格式：
```
ipvsadm -E -t <vip>:<vport> -s <schedule> -j <synproxy> =p <timeout> -M <netmask>
-j: <synproxy : disable | enable>
```

示例：
```sh
# 修改 vip 为 1.1.1.1 的 LVS 中 http 服务的调度算法为 Round Robin
$ ipvsadm -E -t 1.1.1.1:80 -s rr
$ ipvsadm -E -f 2 -s rr    # firewall mark type

# 修改 vip 为 2.2.2.2 的 FTP 服务的 TIMEOUT 为 60s，且打开 synproxy
$ ipvsadm -E -t 2.2.2.2:21 -p 60 -j enable
```

注：命令 -E 的选项支持全部指明或部分指明。


### 3、在LVS上删除一个service
命令格式：
```
ipvsadm -D -t <vip>:<vport>
ipvsadm -D -f fwmark
```
示例：
```sh
# 删除 vip 为 1.1.1.1 的 service
$ ipvsadm -D -t 1.1.1.1:80
```


## 二、ipvsadm命令参数详解

