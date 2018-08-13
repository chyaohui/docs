> ipvsadm 使用手册

## 一、ipvsadm常用命令
1、在LVS上新增service
---
命令格式：
```
ipvsadm -A -t <vip>:<vport> -s <schedule: rr|wrr|lc|wlc|lblc|lblcr|dh|sh|sed|nq>
```
示例：
```
# 在 LVS 中为 http 协议添加一个 VIP 为 1.1.1.1 的 service, 并设置调度方式为 Round Robin
ipvsadm -A -t 1.1.1.1:80 -s rr
```
## 二、ipvsadm命令参数详解

