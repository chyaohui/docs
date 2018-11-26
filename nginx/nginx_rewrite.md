> rewrite 模块即 `ngx_http_rewrite_moodule` 模块，主要功能是改写请求 URI，是 Nginx 默认安装的模块。rewrite 模块会根据 PCRE 正则匹配重写 URI，然后发起内部跳转再匹配 location，或者直接做 30x 重定向返回客户端。




## rewrite指令的工作原理

rewrite 模块的指令有 break、if、return、rewrite、set 等，rewrite 指令所执行的顺序如下：

* 首先在 server 上下文中依照顺序执行 rewrite 模块指令；
* 如果 server 中写了 rewrite，那么以新 URI 发起内部跳转，直接匹配 location，不会再执行 server 里的 rewrite 指令，然后：
    - 新 URI 直接匹配 location
    - 如果匹配上某个 location，那么其中的 rewrite 模块指令同样依照顺序执行
    - 如果再次导致 URI 的 rewrite，那么再一次进行内部跳转去匹配 location，但跳转的总次数不超过 50 次。


## if 指令

Nginx 配置的 if 指令只是 rewrite 模块的一部分而已，它的使用是很有局限的，能在 if 块中使用的指令只有 rewrite 模块中的那些和一些 proxy_pass 之类的特殊指令。

* if 指令的格式

    格式：`if (条件判断) {rewrite指令}`

* 条件判断语句由 Nginx 内置变量、逻辑判断符号和目标字符串组成
* 逻辑判断符号有：`=`、`~`、`~*`、`!~`、`!~*`
* 示例1

    ```bash
    if ($request_method = POST)
    {
        return 405;
    }
    ```
* 示例2

    ```bash
    if ($http_user_agent ~ MSIE)
    {
        return 403;
    }
    # 同时限制多个 UA，可以这样写
    if ($http_user_agent ~ "MSIE|firefox|spider")
    {
        return 403;
    }
    ```

* 示例3

    ```bash
    if (!-f $request_filename)
    {
        rewrite xxx;
    }
    ```
* 示例4

    ```bash
    if ($request_uri ~* 'gid=\d{9,12}/')
    {
        rewrite xxx;
    }
    ```


## 