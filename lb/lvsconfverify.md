> 配置文件导入 SLB 前后，一致性校验方法的说明

说明：
------
为了把线上原有的 lvs.conf 配置文件导入 SLB 系统，需要将原始格式转换为 SLB 支持的 vip-port.conf 文件格式。现在需要做的是校验两种格式在参数有效性上，经过转换后是否一致。下边描述一致性校验的方法：

* 使用 conf2json 工具将 lvs.conf 转换为 origin.json 格式
* 使用 conf2json 工具将 vip-vport.conf 多个文件使用 include 方式转换为 slb.json 格式
* 编写脚本对比 origin.json 和 slb.json 格式内容
  - 使用 python 脚本 load json 数据
  - 分析数据格式：字典和列表
  - 字典数据直接 "==" 方式对比
  - 列表数据(有序数据结构)需要针对各个字段进行对比


简单代码如下：
```python
#!/usr/bin/env python3                                                                                  

# sed -i '/weight/d' file
# ignore weight param

# opslog_view.csv 是新旧 VIP 的对比关系表

import json
 
ip_dict = {}

def ip_map():
    ffff = open("opslog_view.csv", 'r')
    for line in ffff:
        line_list = line.split(',')
        first_ip = line_list[1].strip('"')
        sec_ip = line_list[2].strip('"')
        ip_dict[first_ip] = sec_ip
    ffff.close()
    return ip_dict
 
ip_map()

fd1 = open("final_cfg.cfg", "r")
fd2 = open("render_cfg.cfg", "r")

all = json.load(fd1)
all2 = json.load(fd2)

vss = all['virtual_server']
vss2 = all2['virtual_server']
 
newvs = {}
for vs in vss:
    vip = vs['vip']
    vport = vs['vport']
    newvs[(ip_dict[vip], vport)] = vs
    vs['vip'] = ip_dict[vip]
 
 
newvs2 = {}
for vs2 in vss2:
    vip = vs2['vip']
    vport = vs2['vport']
    newvs2[(vip, vport)] = vs2

def diff_rs_list(rs1, rs2):
    dict1 = {}
    dict2 = {}
    for e in rs1:
        dict1[e['rip']] = e
 
    for e in rs2:
        dict2[e['rip']] = e
    
    count1 = len(dict1.keys())
    count2 = len(dict2.keys())
    if count1 != count2:
        return 0
    for k,v in dict1.items():
        if dict1[k] != dict2[k]:
            return 0
 
    return 1
 
 
for k,v in newvs.items():
    if k in newvs2.keys():
        v1 = newvs[k]
        v2 = newvs2[k]
        ret = v1['vip'] == v2['vip'] and \
              v1['vport'] == v2['vport'] and \
              v1['lb_algo'] == v2['lb_algo'] and \
              v1['persistence_timeout'] == v2['persistence_timeout'] and \
              v1['delay_loop'] == v2['delay_loop'] and \
              v1['est_timeout'] == v2['est_timeout'] and \
              v1['fin_timeout'] == v2['fin_timeout'] and \
              v1['protocol'] == v2['protocol'] and \
              v1['alpha'] == v2['alpha'] and \
              v1['omega'] == v2['omega'] and \
              v1['syn_proxy'] == v2['syn_proxy'] and \
              v1['quorum'] == v2['quorum'] and \
              v1['ha_suspend'] == v2['ha_suspend'] and \
              v1['lb_kind'] == v2['lb_kind'] and \
              v1['laddr_group_name'] == v2['laddr_group_name']
        rs1 = v1['real_server']
        rs2 = v2['real_server']
        if diff_rs_list(rs1, rs2) == 1 and ret:
            pass
        else:
            #print(newvs[k])
            #print('----------------')
            #print(newvs2[k])
            print('diff',k)
            pass
    else:
        print("not in", k)
        pass
```