# 分享 apache 服务器的实战经验

> Apache 是一款使用量排名第一的 web 服务器，平时我们说的 LAMP 中的 A 指的就是它，由于其开源、稳定、安全等特性而被广泛使用。Apache 是 LAMP 的核心，文中记录了做基础运维时关于 Apache 经常使用的功能。

计划主要分享内容目录如下：
* apache 的几种工作模式
* apache 的用户认证
* 设置默认虚拟主机
* 域名 301 跳转
* apache 日志切割
* 不记录指定文件类型的日志
* apache 配置静态缓存
* apache 配置防盗链
* apache 访问控制
* 禁止解析 PHP
* 禁止指定 user_agent
* 限制某个目录

## Apache 的三种工作模式
apache 一共有 3 种稳定的模式，它们分别是 prefork、worker、event。http-2.2 版本的 httpd 默认的是 mpm 工作模式为 prefork，2.4 版本的 httpd 默认版本是 event 工作模式，可以通过 httpd -V 来看，编译的时候，可以通过 configure 的参数来指定。
```bash
$ httpd -V | grep -i "server mpm"
Server MPM:     Prefork

$ configure --with-mpm=prefork | worker | event
```

### prefork 工作模式
Apache 在启动之初，就预先 fork 一些子进程，然后等待请求进来。之所以这样做，是为了减少频繁创建和销毁进程的开销。每个子进程只有一个线程，在一个时间点内，只能处理一个请求。
* 优点：成熟稳定，兼容所有新老模块。同时，不需要担心线程安全的问题。
* 缺点：一个进程相对占用更多的系统资源，消耗更多的内存。而且，它并不擅长处理高并发请求。

### worker 工作模式
使用了多进程和多线程的混合模式。它也预先 fork 了几个子进程(数量比较少)，然后每个子进程创建一些线程，同时包括一个监听线程。每个请求过来，会被分配到 1 个线程来服务。线程比起进程会更轻量，因为线程通常会共享父进程的内存空间，因此，内存的占用会减少一些。在高并发的场景下，因为比起 prefork 有更多的可用线程，表现会更优秀一些。
* 优点：占据更少的内存，高并发下表现更优秀。
* 缺点：必须考虑线程安全的问题。

### event 工作模式
它和 worker 模式很像，最大的区别在于，它解决了 keep-alive 场景下，长期被占用的线程的资源浪费问题。event MPM 中，会有一个专门的线程来管理这些 keep-alive 类型的线程，当有真实请求过来的时候，将请求传递给服务线程，执行完毕后，又允许它释放。这样增强了高并发场景下的请求处理能力。

HTTP 采用 keepalive 方式减少 TCP 连接数量，但是由于需要与服务器线程或进程进行绑定，导致一个繁忙的服务器会消耗完所有的线程。Event MPM 是解决这个问题的一种新模型，它把服务进程从连接中分离出来。在服务器处理速度很快，同时具有非常高的点击率时，可用的线程数量就是关键的资源限 制，此时 Event MPM 方式是最有效的，但不能在HTTPS访问下工作。

## Apache 的用户认证
有时候，我们需要给一些特殊的访问设置一个用户认证机制，增加安全。比如我们的个人网站，一般都是有一个管理后台的，虽然管理后台本身就有密码，但我们为了更加安全，可以再设置一层用户认证。

### 编辑配置文件
在对应的虚拟主机配置中加入如下配置：
```xml
$ vim /usr/local/apache2/conf/extra/httpd-vhosts.conf

<VirtualHost *:80>
    DocumentRoot "/usr/local/apache2/htdocs"
    ServerName www.123.com
    ServerAlias www.abc.com
    <Directory /usr/local/apache2/htdocs/admin.php>
        AllowOverride AuthConfig
        AuthName "Please input you acount."
        AuthType Basic
        AuthUserFile /usr/local/apache2/htdocs/.htpasswd
        require valid-user
    </Directory>
</VirtualHost>
```

说明：首先指定要对哪个目录进行验证，AuthName自定义，AuthUserFile指定用户密码文件在哪里。

### 创建加密用的用户名和密码文件
创建第一个用户时-c选项创建.htpasswd文件，-m选项增加用户，根据提示输入密码。

```bash
$ htpasswd -c htdocs/.htpasswd liwei
$ htpasswd -m htdocs/.htpasswd admin
```

### 重启 apache 服务
先检查配置是否正确，然后用 graceful 相当于是 reload 配置，不用重启 apache 服务，效果一样。测试，通过浏览器输入 www.123.com/admin.php 提示输入密码。
```bash
$ apachectl -t
$ apachectl graceful
```





































