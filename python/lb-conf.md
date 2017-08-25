# 负载均衡配置解析文档

## 需求：
1. 为负载均衡的使用方提供接口查询自己业务的vip的配置以及member  
2. 为负载均衡的运维和操作人员提供接口快速的查询vip的配置以及归属，和操作设备的登录ip，已经每组设备的配置情况  
3. 为sip提供接口进行配置的查询，或者直接使用数据库进行查询  
4. 将线上的配置解析成k-v对应的接口持久化存储到数据库，为负载均衡的数据分析做准备  
5. 后期可以将配置解析的数据库与域名进行关联  


## 技术方向
rsync:同步主机配置  
mysql:结合zabbix进行主机存活检测  
mongodb:持久化存储配置解析的解析结果  
python:配置解析的主要逻辑实现，以及接口的实现  


## 实现逻辑
通过rsync和crontab定期将备份到中心控制的配置文件同步到配置解析的工具上  
通过扫描文件夹的配置和配置文件是否存在进行第一次过滤，找出活跃的在线的主机  
通过查询zabbix的数据库进一步过滤在线的设备，形成在线的设备的列表和主机组  
由于负载均衡的配置文件都是整组同步的配置一组设备保持一致的配置，所以根据在线的主机组查询主机组中其中一台设备进行配置解析  
根据不通的角色进行不同的配置文件解析组合  
入库解析完的配置  
根据已经解析出来的配置进行进一步分析，生成vip，port归属的lvs主机组，ha的主机组和ssl的主机组的对应关系并入库，同时生成了所有snat使用的vip的表  
定义数据库的模型，用python实现查询逻辑  
使用flask-resuful结合上面的查询逻辑提供接口。
## 入库的逻辑
ha:解析haproxy配置文件和backend.d里的所有配置文件  
hl:解析haproxy配置文件和backend.d里的所有配置文件，vip配置文件，lvs配置文件  
lvs:解析lvs配置文件和vip的配置文件  
ssl:解析nginx的配置文件  
主机在线:有对应角色必须存在的配置文件，如：lvs需要有vip.conf，主机在zabbix中属于存活状态  
入库配置:每次初始化程序先删除之前入库的配置，即每次都清空数据库，重新全部入库,增量更新时,对更新的数据进行update，新增数据进行添加，删除的数据进行删除  
更新周期:通过对文件目录的监控，在文件发生改变时进行解析。  

## 使用方法（各个程序功能）

groups.py:生成组和hosts的数据表  
task.py:生成所有的在线配置数据表  
haselfip.py:生成所有的ha的selfip关系表  
snatgetwayip.py:生成所有的snat使用的vip的表  
allServiceVip.py:生成所有业务使用的vip表，包含模式归属组等数据  
启动守护程序
```bash
cd /$path/watchfile  
python3 deamon.py --ops=start
```
关闭守护程序:  
`python3 deamon.py --ops=stop`

## 后期可以改进的方向  
1. 由于负载均衡配置的特殊性，现在没有一个单一的唯一主键，后期可以通过字符串拼接的模式形成唯一主键。
2. 对现有的数据表进行改进，对需要经常进行查询的字段加索引。
3. 配置解析可能不是瞬间完成，可能会出现查询是正在进行该vip的插入或删除的情况，会导致查询数据缺失，想办法如何解决此问题。（对此vip，组临时加锁或者忽略？）
4. 由mongodb切换至mysql？
5. 根据业务的新的需求添加新的接口
6. 通过zabbix和已经上传的配置文件进行反向解析，查找出没有同步配置文件到中心机的设备。  


## 接口实现使用方法（post）
### vip查询member  
*根据vip的模式不同会有不同的返回*  
ha：  
request:  `curl -H "Content-Type: application/json" -X POST -d '{"vip":"111.13.89.246","port":"80"}' http://172.16.105.191:8003/vipgetmember`  
response:   
```json
{
  "data": {
    "content": [
      {
        "bename": "be-YHG-news-mobile-app",
        "fename": "fe-YHG-news-mobile-app",
        "member": [
          "10.13.216.53:80:32",
          "10.13.216.54:80:32",
          "10.13.216.55:80:32",
          "10.13.216.56:80:32",
          "10.13.216.57:80:32",
          "10.13.216.58:80:32",
          "10.13.216.59:80:32",
          "10.13.216.60:80:32",
          "10.35.8.60:80:32",
          "10.35.8.61:80:32",
          "10.35.8.134:80:32",
          "10.35.8.135:80:32",
          "10.35.8.136:80:32",
          "10.35.8.236:80:12",
          "10.35.8.237:80:12",
          "10.35.8.238:80:12",
          "10.35.8.121:80:32",
          "10.35.8.122:80:32",
          "10.35.8.47:80:32",
          "10.35.8.48:80:32"
        ]
      }
    ],
    "viptype": "HA"
  },
  "status": 200
}
```
ssl:  
request:  `curl -H "Content-Type: application/json" -X POST -d '{"vip":"111.13.89.246","port":"443"}' http://172.16.105.191:8003/vipgetmember`  
response: 
```json
{
  "data": {
    "content": [
      {
        "bename": "be-YHG-news-mobile-app-ssl",
        "fename": "fe-YHG-news-mobile-app-ssl",
        "member": [
          "10.13.216.53:80:32",
          "10.13.216.54:80:32",
          "10.13.216.55:80:32",
          "10.13.216.56:80:32",
          "10.13.216.57:80:32",
          "10.13.216.58:80:32",
          "10.13.216.59:80:32",
          "10.13.216.60:80:32",
          "10.35.8.60:80:32",
          "10.35.8.61:80:32",
          "10.35.8.134:80:32",
          "10.35.8.135:80:32",
          "10.35.8.136:80:32",
          "10.35.8.236:80:12",
          "10.35.8.237:80:12",
          "10.35.8.238:80:12",
          "10.35.8.121:80:32",
          "10.35.8.122:80:32",
          "10.35.8.47:80:32",
          "10.35.8.48:80:32"
        ]
      }
    ],
    "proxyip": "172.16.35.240",
    "viptype": "SSL-HA"
  },
  "status": 200
}
```

lvs:  
request:  `curl -H "Content-Type: application/json" -X POST -d '{"vip":"112.90.152.6","port":"80"}' http://172.16.105.191:8003/vipgetmember`  
response: 
```json
{
  "data": {
    "content": [
      {
        "member": [
          "112.90.152.10:80:5000",
          "112.90.152.11:80:5000",
          "112.90.152.12:80:5000",
          "112.90.152.13:80:5000",
          "112.90.152.14:80:5000"
        ]
      }
    ],
    "viptype": "LVS"
  },
  "status": 200
}
```

### vip查询option  
**根据vip的模式不同会有不同的返回** 
ssl:  
request:  `curl -H "Content-Type: application/json" -X POST -d '{"vip":"111.13.89.246","port":"443"}' http://172.16.105.191:8003/vipgetoptions`  
response:
```json
{
  "data": {
    "content": [
      {
        "options": "access_log  off;\nproxy_set_header Host  $host;\nproxy_set_header x-Forwarded-For $remote_addr;\nproxy_set_header x-Forwarded-Port $remote_port;\nproxy_set_header X-Proto SSL;\nproxy_redirect off;",
        "server_name": "status-111.13.89.246-api.sina.cn api.sina.cn"
      }
    ],
    "viptype": "SSL"
  },
  "status": 200
}
```

ha:  
request:  `curl -H "Content-Type: application/json" -X POST -d '{"vip":"111.13.89.246","port":"80"}' http://172.16.105.191:8003/vipgetoptions`  
response:  
```json
{
  "data": {
    "content": [
      {
        "bename": "be-YHG-news-mobile-app",
        "fename": "fe-YHG-news-mobile-app",
        "options": "fe:maxconn 200000\nreqidel ^X-Forwarded-For:.*\nreqidel ^X-Forwarded-Port:.*\nlog 10.13.32.126:514 local7 info\noption httplog;be:option forwardfor if-always\noption forwardport if-always\nbalance roundrobin\noption server_from_file\nserver_file be-YHG-news-mobile-app"
      }
    ],
    "viptype": "HA"
  },
  "status": 200
}
```

lvs:  
request:  `curl -H "Content-Type: application/json" -X POST -d '{"vip":"221.179.190.105","port":"80"}' http://172.16.105.191:8003/vipgetoptions`  
response:
```json
{
  "data": {
    "content": [
      {
        "options": "delay_loop:15\nlb_algo:wrr\nlb_kind:DR\npersistence_timeout:1800\nprotocol:TCP"
      }
    ],
    "viptype": "LVS"
  },
  "status": 200
}
```

### vip查询归属组查询  
**vip的归属组，包含七层（如果有）和四层的归属组**  
request:  `curl -H "Content-Type: application/json" -X POST -d '{"vip":"111.13.89.246","port":"80"}' http://172.16.105.191:8003/vipgetgroup`  
response: 
```json
{
  "data": {
    "content": {
      "l7group": "ha.g1.yhg",
      "vipgroup": "lvsx.sg4.yhg"
    }
  },
  "status": 200
}
```
  
### 主机组查询信息  
**查询单组主机的所有host，登录ip，如果是lvs角色可以查询组内的vip和vrrp__instance的信息**  
查询主机组的ip：  
request:  `curl -H "Content-Type: application/json" -X POST -d '{"group": "lvs1.g1.yhg","method":"loginip"}' http://172.16.105.191:8003/groupsearch`  
response:
```json
{
  "data": {
    "content": [
      {
        "hostname": "lvs1.199.g1.yhg.lb.sinanode.com",
        "mip": "10.35.249.199"
      },
      {
        "hostname": "lvs1.200.g1.yhg.lb.sinanode.com",
        "mip": "10.35.249.200"
      }
    ]
  },
  "status": 200
}
```

查找主机组内所有主机的hostname：  
request:  `curl -H "Content-Type: application/json" -X POST -d '{"group": "lvs1.g1.yhg","method":"hosts"}' http://172.16.105.191:8003/groupsearch`  
response:
```json
{
  "data": {
    "content": [
      "lvs1.200.g1.yhg.lb.sinanode.com",
      "lvs1.199.g1.yhg.lb.sinanode.com"
    ]
  },
  "status": 200
}
```  

查找lvs组拥有的vip：  
request:  `curl -H "Content-Type: application/json" -X POST -d '{"group": "lvsxc.sg2.bhl","method":"vips"}' http://172.16.105.191:8003/groupsearch`  
reponse:  
```json
{
  "data": {
    "content": [
      "61.158.251.246",
      "61.158.251.247",
      "61.158.251.248",
      "61.158.251.249",
      "61.158.251.250",
      "61.158.251.251",
      "61.158.251.252",
      "61.158.251.253",
      "61.158.251.243",
      "61.158.251.242",
      "61.158.251.244"
    ]
  },
  "status": 200
}
```

查找lvs组的vip配置:  
request:  `curl -H "Content-Type: application/json" -X POST -d '{"group": "lvsxc.sg2.bhl","method":"infos"}' http://172.16.105.191:8003/groupsearch`  
response:  
```json
{
  "data": {
    "content": [
      {
        "instancename": "VI_bond1.251_1",
        "interface": "bond1.251",
        "routeid": "72"
      }
    ]
  },
  "status": 200
}
``` 

### 查询对应角色的主机组  
**查询对应角色拥有的主机组**  
requese:  `curl -H "Content-Type: application/json" -X POST -d '{"role": "hl"}' http://172.16.105.191:8003/rolesearch`  
reponse:  
```json
{
  "data": {
    "content": [
      "hl.sg2.ft",
      "hl.sg1.shuoh",
      "hl.sg3.tg",
      "hl.sg1.xd",
      "hl.sg4.xd",
      "hl.sg2.tg",
      "hl.sg1.yq",
      "hl.sg3.ft",
      "hl.sg1.zsl",
      "hl.sg1.qxg",
      "hl.sg5.ja",
      "hl.sg1.csk",
      "hl.sg2.jxq",
      "hl.sg3.qxg",
      "hl.g1.hy",
      "hl.sg1.shx",
      "hl.sg2.yhg",
      "hl.sg1.yz",
      "hl.sg1.ja",
      "hl.sg1.tc",
      "hl.sg1.yhg",
      "hl.sg1.bx",
      "hl.sg2.yf"
    ]
  },
  "status": 200
}
```


### vip查询拥有的vport  
**查询vip拥有的vport**  
request:  `curl -H "Content-Type: application/json" -X POST -d '{"vip": "111.13.89.246"}' http://172.16.105.191:8003/vipgetvport`  
response:  
```json
{
  "data": {
    "content": [
      "80",
      "443"
    ]
  },
  "status": 200
}
```  
### member反查归属vip  
**根据member查出归属的vip，根据lvs和ha进行分类**  
requese:  `curl -H "Content-Type: application/json" -X POST -d '{"member": "218.9.147.225"}' http://172.16.105.191:8003/membersearch`  
response:  
```json
{
  "data": {
    "content": {
      "havip": [
        "202.97.199.16:80",
        "202.97.199.71:80"
      ],
      "lvsvip": [
        "125.211.213.126:80",
        "125.211.213.126:1935"
      ]
    }
  },
  "status": 200
}
```  
 
### 根据机房，机房和角色查询对应的主机查找对应机房的主机，对应机房相关角色的主机  
查找机房的所有对应角色的主机：  
request:  `curl -H "Content-Type: application/json" -X POST -d '{  "idc": "tc","role":"ssl"}' http://172.16.105.191:8003/idcsearch`  
response:  
```json
{
  "data": {
    "content": [
      "ssl.51.sinag1.tc.lb.sinanode.com",
      "ssl.51.sinag1.tc.lb.sinanode.com",
      "ssl.60.g1.tc.lb.sinanode.com",
      "ssl.69.g1.tc.lb.sinanode.com",
      "ssl.61.g1.tc.lb.sinanode.com",
      "ssl.27.g1.tc.lb.sinanode.com",
      "ssl.59.g1.tc.lb.sinanode.com",
      "ssl.147.g1.tc.lb.sinanode.com",
      "ssl.62.g1.tc.lb.sinanode.com",
      "ssl.52.sinag1.tc.lb.sinanode.com",
      "ssl.29.g1.tc.lb.sinanode.com",
      "ssl.28.g1.tc.lb.sinanode.com",
      "ssl.31.g1.tc.lb.sinanode.com",
      "ssl.26.g1.tc.lb.sinanode.com",
      "ssl.32.g1.tc.lb.sinanode.com"
    ]
  },
  "status": 200
}
```

查找机房内的所有主机:   
request: `curl -H "Content-Type: application/json" -X POST -d '{ "idc": "yfxj"}' http://172.16.105.191:8003/idcsearch`  
response:  
```json
{
  "data": {
    "content": [
      "lvsxc.26.sg3.yfxj.lb.sinanode.com",
      "lvsxc.27.sg3.yfxj.lb.sinanode.com"
    ]
  },
  "status": 200
}
```  
### 主机名查询登录ip
**根据主机的hostname查找登录ip**  
request：
`curl -H "Content-Type: application/json" -X POST -d '{"host": "lvsx.196.sg5.qxg"}' http://172.16.105.191:8003/hostsearch`  
response:  
```json
{
  "data": {
    "content": [
      {
        "hostname": "lvsx.196.sg5.qxg.lb.sinanode.com",
        "mip": "10.71.249.196"
      }
    ]
  },
  "status": 200
}
```

