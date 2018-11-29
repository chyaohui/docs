> DNS 系统是互联网最重要的基础系统，它为我们的互联网访问带来了极大的便利，可以说现在如果没有 DNS 系统，我们就可能无法进行访问。其实 DNS 系统是整个互联网最大的分布式系统，没有之一。DNS 其实很容易理解，就是将 IP 地址翻译我们容易识记的名称而已。

## 什么是DNS


## DNS的使用场景


## 关于DNS服务器


## DNS服务器的分类


## DNS是如何访问的


## 简单使用DNS
通过上述内容，我们已经大概知道 DNS 的基本工作原理，我们来实际来感受一下 DNS 查询。在 Linux 系统下，可以通过 dig 或 nslookup 工具来进行查询。在 CentOS 系统中 bind-utils 工具包中包含了这两个工具。

我们直接使用新浪的权威 DNS 查询门户网站的域名
```bash
# 查询新浪门户的域名 www.sina.com.cn
$ dig www.sina.com.cn @ns1.sina.com.cn
... ...
;; ANSWER SECTION:
www.sina.com.cn.   3600  IN  CNAME  spool.grid.sinaedge.com.
# 省略了很多内容
```

同样可以指定互联网中的公共 DNS 来查询
```bash
$ dig weibo.cn @1.2.4.8
# weibo.cn 对应的 A 记录
;; ANSWER SECTION:
weibo.cn.               32      IN      A       180.149.139.248
weibo.cn.               32      IN      A       180.149.153.242
weibo.cn.               32      IN      A       180.149.153.187
weibo.cn.               32      IN      A       180.149.153.216

# 新浪的 4 台权威 DNS 服务器域名
;; AUTHORITY SECTION:
weibo.cn.               14117   IN      NS      ns3.sina.com.cn.
weibo.cn.               14117   IN      NS      ns1.sina.com.cn.
weibo.cn.               14117   IN      NS      ns4.sina.com.cn.
weibo.cn.               14117   IN      NS      ns2.sina.com.cn.

# 新浪 4 台权威 DNS 服务器对应的 IP 地址
;; ADDITIONAL SECTION:
ns1.sina.com.cn.        59877   IN      A       202.106.184.166
ns2.sina.com.cn.        73979   IN      A       61.172.201.254
ns3.sina.com.cn.        73979   IN      A       123.125.29.99
ns4.sina.com.cn.        65585   IN      A       121.14.1.22
```

默认 DNS 使用 UDP 协议进行查询的，也可以指定 TCP 协议
```bash
$ dig www.sina.com.cn +tcp
# 不指定 DNS 的话，默认会按系统 ／etc/resolv.conf 配置的 DNS 来查询
```
