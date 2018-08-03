
### Netfilter 对 IPV4 的 HOOK
> Netfilter 定义了 5 个关于 IPV4 的 HOOK，这些符号的声明可以在 linux/netfilter_ipv4.h 中找到

|HOOK 点|调用时机|
|-----|-----|
|NF_IP_PRE_ROUTING|刚进入网络层的数据包通过此点，刚刚进行完版本号、校验和等检测，目的地址转换在此点进行|
|NF_IP_LOCAL_IN|经路由查找后，送往本机的通过此检查点，INPUT包过滤在此点进行|
|NF_IP_FORWARD|要转发的包通过此检测点，FORWORD包过滤在此点进行|
|NF_IP_LOCAL_OUT|本机进程发出的包通过此检测点，OUTPUT包过滤在此点进行|
|NF_IP_POST_ROUTING|所有马上要通过网络设备出去的包通过此检测点，内置的源地址转换功能在此点进行|


### Netfilter 的返回值
* **NF_DROP**。丢弃该数据包，这个返回值的含义是该数据包将被完全的丢弃，所有为它分配的资源都应当被释放。
* **NF_ACCEPT**。保留该数据包，这个返回值告诉Netfilter：到目前为止，该数据包还是被接受的并且该数据包应当被递交到网络堆栈的下一个阶段。
* **NF_STOLEN**。忘掉该数据包，因为它告诉Netfilter，“忘掉”这个数据包。这里告诉Netfilter的是：该hook函数将从此开始对数据包的处理，并且Netfilter应当放弃对该数据    包做任何的处理。但是，这并不意味着该数据包的资源已经被释放。这个数据包以及它独自的sk_buff数据结构仍然有效，只是hook函数从Netfilter获取了该数据包的所有权。
* **NF_QUEUE**。将该数据包插入到用户空间，将数据包排入队列，通常是将数据包发送给用户进程空间处理
* **NF_REPEAT**。再次调用该hook函数，请求Netfilter再次调用这个hook函数。显然，使用者应当谨慎使用NF_REPEAT这个返回值，以免造成死循环。

