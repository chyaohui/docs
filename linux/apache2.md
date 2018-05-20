> 上周分享了 apache 的几种工作模式和用户认证两大部分内容，这篇文章继续上次的分享完成如下内容：

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

## 设置默认虚拟主机
默认虚拟主机就是配置文件里的第一个虚拟主机。关于默认虚拟主机有个特点，凡是解析到这台服务器的域名，不管是什么域名，只要在配置文件中没有配置，那么都会访问到这个主机上来。

如果我们直接用 IP 访问，会访问到这个站点上来。为了避免别人乱解析，所以应该把默认也就是第一个虚拟主机给禁止掉。我们使用 allow，deny 语句，已经禁止掉了。

### 配置默认虚拟主机
添加一个虚拟主机的记录：
```sh
$ vim conf/extra/httpd-vhosts.conf

<VirtualHost *:80>
    DocumentRoot "/var/123"
    ServerName xxxxx.com.cn
    <Directory /var/123>
        Order allow,deny
        Deny from all
    </Directory>
</VirtualHost>
```
创建 /var/123 目录，并且设置 600 权限，daemon 用户无法访问：
```sh
$ mkdir /var/123
$ chmod -R 600 /var/123
```
### 重启apache服务器
```sh
$ apachectl -t
$ apachectl graceful
```
如果用 IP 或其它解析的域名访问，发现提示：
```sh
Forbidden
You don't have permission to access / on this server.
```

## 域名 301 跳转
一个站点难免会有多个域名，而多个域名总得有一个主次，比如我的网站可以用两个域名访问：www.itepub.cn 和 www.linuxblogs.cn 但大家发现不管我用哪个域名访问，最终都会跳转到 www.linuxblogs.cn 上来。

这个行为就叫做域名跳转，这里的 301 只是一个状态码，跳转除了 301 还有 302，301 是永久跳转，302 是临时跳转，网站上一定要设置为 301，这样对搜索引擎是比较友好的。

### 配置域名跳转:
```sh
$ vim conf/extra/httpd-vhosts.conf
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{HTTP_HOST} ^www.abc.com$
    RewriteRule ^/(.*)$ http://www.123.com/$1 [R=301,L]
</IfModule>
```
配置为：当访问aaa时，跳转到123的网站。

### 配置多个域名跳转:
```sh
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{HTTP_HOST} ^www.abc.com$ [OR]
    RewriteCond %{HTTP_HOST} ^www.abcd.com$
    RewriteRule ^/(.*)$ http://www.123.com/$1 [R=301,L]
</IfModule>
```

### 重启服务器并测试:
```sh
$ apachectl -t
$ apachectl graceful
```

测试1：
```sh
$ curl -x192.168.0.8:80  www.abc.com -I
HTTP/1.1 301 Moved Permanently
Date: Tue, 25 Oct 2016 15:48:10 GMT
Server: Apache/2.2.31 (Unix) PHP/5.5.38
Location: http://www.123.com/
Content-Type: text/html; charset=iso-8859-1
```

测试2：
```sh
$ curl -x192.168.0.8:80  www.abcd.com -I
HTTP/1.1 301 Moved Permanently
Date: Tue, 25 Oct 2016 15:48:49 GMT
Server: Apache/2.2.31 (Unix) PHP/5.5.38
Location: http://www.123.com/
Content-Type: text/html; charset=iso-8859-1
```
通过上述测试，发现无论是 abc 或 abcd 都可以跳转到 www.123.com 域名上来，通过浏览器访问也一样。

## apache 日志切割
我们每访问一次网站，那么就会记录若干条日志。当然前提是已经设置了日志，日志不去管理，时间长了日志文件会越来越大，如何避免产生这么大的日志文件？其实 apache 有相关的配置，使日志按照我们的需求进行归档，比如每天一个新日志，或者每小时一个新的日志。

### 设置日志的路径名称
编辑添加内容如下：
```sh
$ vim /usr/local/apache2/conf/extra/httpd-vhosts.conf
ErrorLog "logs/error.log"
CustomLog "logs/access.log" combined
```
指定了日志存放在 /usr/local/apache2/logs 目录下分别为 error.log 和 access.log，combined 为日志显示的格式，日志格式可以参考配置文件 httpd.conf 中格式的指定，如下：
```sh
LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined
LogFormat "%h %l %u %t \"%r\" %>s %b" common
```

### 设置apache日志分割
同样编辑配置文件httpd-vhosts.conf
```sh
ErrorLog "|/usr/local/apache2/bin/rotatelogs -l /usr/local/apache2/logs/aaa-error_%Y%m%d.log 86400"
CustomLog "|/usr/local/apache2/bin/rotatelogs -l /usr/local/apache2/logs/aaa-access_%Y%m%d.log 86400" combined
```
ErrorLog 是错误日志，CustomLog 是访问日志。| 就是管道符，意思是把产生的日志交给 rotatelog 这个工具，而这个工具就是 apache 自带的切割日志的工具。-l 的作用是校准时区为 UTC，也就是北京时间。86400，单位是秒，正好是一天，那么日志会每天切割一次。而最后面的 combined 为日志的格式，在 httpd.conf 中有定义。 

