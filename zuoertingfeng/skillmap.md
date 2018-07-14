目录
---
* [入门篇](#rumenpian)
  - [零基础启蒙](#lingjichurumen)
  - [正式入门](#zhengshirumen)
* [修养篇](#xiuyangpian)
* [专业基础篇](#zhuanyejichupian)
  - [编程语言](#bianchengyuyan)
  - [理论学科](#lilunxueke)
  - [系统知识](#xitongzhishi)
* [软件设计篇](#ruanjianshejipian)
  - 软件设计
* [高手成长篇](#gaoshouchengzhangpian)


> 程序员练级攻略


<a id='rumenpian'/>

# 一、入门篇

<a id='lingjichuqimeng'/>

## 1. 零基础启蒙

### 1.1 产生兴趣
* 书籍《[与孩子一起学编程](https://book.douban.com/subject/5338024/)》。这是一本老少咸宜的编程书。
* 在线教程《[MDN的web开发入门](https://developer.mozilla.org/zh-CN/docs/learn)》可以认为是web方面的官方技术网站。

### 1.2 入门语言Python

* 书籍《[Python编程快速上手](https://book.douban.com/subject/26836700/)》
* 书籍《[Python编程：从入门到实践](https://book.douban.com/subject/26829016/)》
* 书籍《[Python Cookbook 中文版](https://book.douban.com/subject/26381341/)》
* 书籍《[流畅的Python](https://book.douban.com/subject/27028517/)》

### 1.3 入门语言JavaScript
* 在线教程《[MDN JavaScript教程](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript)》。可以认为这是最权威的JavaScript官方教程，非常全。
* 在线教程《[W3School JavaScript教程](http://www.w3school.com.cn/js/)》
* 在线教程《[JavaScript全栈教程（廖雪峰）](https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000)》

### 1.4 操作系统入门
* Linux

### 1.5 编程工具
* Visual Studio Code
* [中文教程文档(翻译)](https://legacy.gitbook.com/book/jeasonstudio/vscode-cn-doc/details)

### 1.6 Web编程入门
> 在第三次工业革命的信息化浪潮中，Web 互联网是其中最大的发明，所以这是任何一个程序员都不能错过的。

**学习方向：**
* 前端基础：CSS、HTML、JavaScript
  - [CSS 文档](https://developer.mozilla.org/zh-CN/docs/Web/CSS)
  - [HTML 文档](https://developer.mozilla.org/zh-CN/docs/Web/HTML)
  - 注：并非学习文档所有内容，而是了解CSS和HTML是怎么相互作用来展示数据
  - 简单学习使用JavaScript操纵HTML元件。
  - [理解DOM和动态网页](http://www.w3school.com.cn/htmldom/index.asp)
* 后端基础
  - Python
  - Node.js

**学习要点：**
* 学习 HTML 基本语法
* 学习 CSS 如何选中 HTML 元素并应用一些基本样式
* 学会 Firefox + Firebug 或 Chrome 查看你觉得很炫的网页结构，并动态修改
* 在一台 Linux 机器上配置 LNMP - Ubuntu/Nginx/PHP/MySQL
* 学习 PHP，让后台 PHP 和前台 HTML 进行数据交互，对服务器响应浏览器请求形成初步认识。
* 实现一个表单提交和反显的功能
* 把 PHP 连接本地或远程数据库 MySQL


### 1.7 实践项目
无论使用 Python，还是 Node.js，还是 PHP，最好能做一个非常简单的 Blog 系统，或 BBS 系统，需要支持如下功能：
* 用户登录和注册(不需要密码找回)
* 用户发帖(不需要支持富文本，只需要支持纯文本)
* 用户评论(不需要支持富文本，只需要支持纯文本)
* 用户登录时密码不应该保存为明文，应该用 MD5 + Salt 来保存
* 用户登录后，对于用户自己的帖子可以有`重新编辑`或`删除`的功能，但是无权编辑或删除其它用户的帖子
* 数据库的设计，需要三张表：用户表、文章表和评论表，他们之间是怎么关联的？
* 图片验证码
* 上传图片
* 阻止用户在发文章或评论时输入带 HTML 或 JavaScript 的内容
* 防范 SQL 注入，参看[PHP官方文档](http://www.php.net/manual/zh/security.database.sql-injection.php)或[微软官方文档](https://docs.microsoft.com/zh-cn/sql/relational-databases/security/sql-injection?view=sql-server-2017)

<a id='zhengshirumen'/>

## 2. 正式入门
**编程技能**

* 编程技巧方面，推荐书籍《[代码大全](https://book.douban.com/subject/1477390/)》。好书和不好的书最大的不一样是，好的书在你不同的阶段来读，你会有不同的收获，而且还会产生更多的深层次的思考。
* 操作系统方面，推荐书籍《[鸟哥的Linux私房菜](https://book.douban.com/subject/4889838/)》
* 网络协议，推荐在线教程《[MDN的HTTP文档](https://developer.mozilla.org/zh-CN/docs/Web/HTTP)》
  - HTTP头
  - HTTP的请求方法
  - HTTP的返回码
  - HTTP的Cookie、缓存、会话、连接管理等
* 数据库设计 - 推荐慕课网的在线课程《[数据库设计的那些事](https://www.imooc.com/learn/117)》；书籍推荐《[MySQL必知必会](https://book.douban.com/subject/3354490/)》
* 前端方面
  - 和JavaScript相关的[jQuery](https://jquery.com/)
  - 和CSS相关的[Bootstrap](https://getbootstrap.com/)
* 字符编码方面，推荐文章《[关于字符编码，你所需要知道的](http://www.imkevinyang.com/2010/06/%E5%85%B3%E4%BA%8E%E5%AD%97%E7%AC%A6%E7%BC%96%E7%A0%81%EF%BC%8C%E4%BD%A0%E6%89%80%E9%9C%80%E8%A6%81%E7%9F%A5%E9%81%93%E7%9A%84.html)》


**编程工具**

* 编程的IDE。传统的 Eclipse [教程](http://www.runoob.com/eclipse/eclipse-tutorial.html)；时髦的 Visual Studio Code [教程](https://www.gitbook.com/book/jeasonstudio/vscode-cn-doc/details)
* 版本管理工具。学习 Git 就好了，推荐教程[Pro Git 第二版](https://git-scm.com/book/zh/v2/)，备选[猴子都能懂得 Git 入门](https://backlog.com/git-tutorial/cn/)，然后学会使用 Github。关于 Git 环境安装和准备以及 Github 使用 [Github and Git 图文教程](https://github.com/JiapengLi/GitTutorial)或[Git 图文教程及详解](https://www.jianshu.com/p/1b65ed31da97)
* 调试前端程序。学会使用 Chrome 调试前端程序，推荐教程[超完整的 Chrome 浏览器客户端调试大全](http://www.igeekbar.com/igeekbar/post/156.htm)
* 数据库设计工具。需要学会使用 MySQL WorkBench，相关手册[官方文档](https://dev.mysql.com/doc/refman/5.7/en/)


**实践项目**

这次需要设计一个投票系统的项目，业务上的需求如下：
* 用户只有在登录后，才可以生成投票表单
* 投票项可以单选，可以多选
* 其它用户投票后显示当前投票结果(但不能刷票)
* 投票有相应的时间，页面上需要出现倒计时
* 投票结果需要用不同颜色不同长度的横条，并显示百分比和人数

技术上的需求如下：
* 这回要用 Java Spring Boot 来实现了，然后，后端不返回任何的 HTML，只返回 JSON 数据给前端
* 由前端的 JQuery 来处理并操作相关的 HTML 动态生成在前端展示的页面
* 前端的页面还要是响应式的，也就是可以在手机端和电脑端有不同的呈现，可以用 Bootstrap 来完成

如果有兴趣，还可以挑战以下这些功能：
* 在微信中，通过微信授权后记录用户信息，以防止刷票
* 可以不用刷页面，就可以动态地看到投票结果的变化
* Google 一些画图表的 JavaScript 库，然后把图片表得风骚一些


<a id='xiuyangpian'/>

# 二、修养篇

<a id='yingyunengli'/>

### 1. 英语能力
* 坚持 Google 英文关键词，而不是在 Google 里搜中文。
* 在 Github 上只用英文。
* 用英文写代码注释
* 用英文写 Code Commit 信息
* 用英文写 Issue 和 Pull Request
* 用英文写 Wiki
* 坚持到 YouTube 上每天看 5 分钟视频
* 坚持用英文词典而不是中文的
* 坚持用英文的教材而不是中文的
* 最后一个，提高问问题的能力

<a id='xiedaimadexiuyang'/>

### 2. 写代码的修养
除了《代码大全》外，还需要补充一些如何写好代码的知识，推荐如下：
* 推荐文章资料《[提问的智慧](http://doc.zengrong.net/smart-questions/cn.html)》
* 推荐书籍《[重构：改善既有代码的设计](https://book.douban.com/subject/4262627/)》
* 推荐书籍《[修改代码的艺术](https://book.douban.com/subject/2248759/)》
* 推荐书籍《[代码整洁之道](https://book.douban.com/subject/4199741/)》
* 推荐书籍《[程序员的职业素养](https://book.douban.com/subject/11614538/)》
* Code Review ()
* Unit Test，推荐《[完美软件：对软件测试的各种幻想](https://book.douban.com/subject/4187479/)》&《[Google 软件测试之道](https://book.douban.com/subject/27187576/)》


<a id='zhuanyejichupian'/>

# 三、专业基础篇

<a id='bianchengyuyan'/>

## 1. 编程语言
**Java语言：**

* 推荐书籍《[Java 核心技术：卷 1 基础知识](https://book.douban.com/subject/26880667/)》
* 推荐书籍《[Spring 实战](https://book.douban.com/subject/26767354/)》
* 推荐书籍《[Spring Boot 实战](https://book.douban.com/subject/26857423/)》
* 推荐书籍《[Java 并发编程实战](https://book.douban.com/subject/10484692/)》
* 推荐书籍《[Java 性能权威指南](https://book.douban.com/subject/26740520/)》
* 推荐书籍《[深入理解 Java 虚拟机](https://book.douban.com/subject/24722612/)》
* 推荐书籍《[Java 编程思想](https://book.douban.com/subject/2130190/)》
* 推荐书籍《[精通 Spring 4.x](https://book.douban.com/subject/26952826/)》
* 推荐书籍《[设计模式](https://book.douban.com/subject/1052241/)》


**C/C++ 语言：**
* 推荐书籍《[C 程序设计语言](https://book.douban.com/subject/1139336/)》
* 推荐书籍《[C 语言程序设计现代方法](https://book.douban.com/subject/2280547/)》
* 推荐书籍《[C 陷阱与缺陷](https://book.douban.com/subject/2778632/)》
* 推荐书籍《[C++ Primer 中文版](https://book.douban.com/subject/25708312/)》
* 推荐书籍《[深度探索 C++ 对象模型](https://book.douban.com/subject/10427315/)》
* 推荐文章《[C++ 虚函数表解析](https://coolshell.cn/articles/12165.html)》
* 推荐文章《[C++ 对象内存布局](https://coolshell.cn/articles/12176.html)》
* 推荐文章《[C++ 的坑真的多吗](https://coolshell.cn/articles/7992.html)》

**学习 Go 语言：**
* 推荐文章《[GO 语言简介（上）- 语法](https://coolshell.cn/articles/8460.html)》
* 推荐文章《[GO 语言简介（下）- 特性](https://coolshell.cn/articles/8489.html)》

<a id='lilunxueke'/>

## 2. 理论学科
**数据结构与算法：**
> 算法是比较难学习的，而且学习 `算法` 是需要智商的。数组、链表、哈希表、二叉树、排序算法等一些基础知识，对大多数人来说是没什么问题的。但是一旦进入到路径规划、背包问题、字符串匹配、动态规划、递归遍历等一些比较复杂的问题上，就会让很多人跟不上了，不但跟不上，而且还会非常痛苦。是的，解决算法问题的确是可以区分人类智商的一个比较好的方式，这也是为什么好些公司用算法题当面试题来找到智商比较高的程序员。
* 基础知识，推荐书籍《[算法](https://book.douban.com/subject/10432347/)》、《[算法图解](https://book.douban.com/subject/26979890/)》
* 理论加持，推荐书籍《[算法导论](https://book.douban.com/subject/20432061/)》
* 思维改善，推荐书籍《[编程珠玑](https://book.douban.com/subject/3227098/)》
* 基础算法题，推荐资料《[LeetCode](https://leetcode.com/)》
* 编程题，推荐资料《[陈皓的 Github](https://github.com/haoel/leetcode)》

**其它理论基础知识：**
* 推荐书籍《[数据结构与算法分析](https://book.douban.com/subject/1139426/)》
* 推荐书籍《[数据库系统概念](https://book.douban.com/subject/1929984/)》
* 推荐书籍《[现代操作系统](https://book.douban.com/subject/3852290/)》
* 推荐书籍《[计算机网络](https://book.douban.com/subject/1391207/)》
* 推荐书籍《[计算机程序的构造与解释](https://book.douban.com/subject/1148282/)》
* 推荐书籍《[编译原理](https://book.douban.com/subject/3296317/)》

<a id='xitongzhishi'/>

## 3. 系统知识

* 必读书籍《[深入理解计算机系统](https://book.douban.com/subject/5333562/)》
* ([Richard Stevens](https://zh.wikipedia.org/wiki/%E7%90%86%E6%9F%A5%E5%BE%B7%C2%B7%E5%8F%B2%E8%92%82%E6%96%87%E6%96%AF)) 三部经典书《[Unix 环境高级编程](https://book.douban.com/subject/1788421/)》
* ([Richard Stevens](https://zh.wikipedia.org/wiki/%E7%90%86%E6%9F%A5%E5%BE%B7%C2%B7%E5%8F%B2%E8%92%82%E6%96%87%E6%96%AF)) 三部经典书《Unix 网络编程》 [第 1 卷 套接口 API](https://book.douban.com/subject/1500149/)、[第 2 卷 进程间通信](https://book.douban.com/subject/4118577/)
* ([Richard Stevens](https://zh.wikipedia.org/wiki/%E7%90%86%E6%9F%A5%E5%BE%B7%C2%B7%E5%8F%B2%E8%92%82%E6%96%87%E6%96%AF)) 三部经典书《[TCP/IP 详解 卷1协议](https://book.douban.com/subject/1088054/)》 - 必读书籍

如果觉得上面这几本经典书比较难啃，可以试试下面这些通俗易懂的(可以先看下面几本)。
* 推荐书籍《[Linux C 编程一站式学习](https://book.douban.com/subject/4141733/)》，看过。
* 推荐书籍《[TCP/IP 网络编程](https://book.douban.com/subject/25911735/)》
* 推荐书籍《[图解 TCP/IP](https://book.douban.com/subject/24737674/)》，看过，有图有真相。
* 推荐书籍《[Wireshark 数据包分析实战](https://book.douban.com/subject/21691692/)》，已有书籍。
* 推荐书籍《[Linux/Unix 系统编程手册](https://book.douban.com/subject/25809330/)》
* 推荐书籍《[Linux 系统编程](https://book.douban.com/subject/25828773/)》

关于 TCP 的东西，还可以看看下面这一系列的文章：
* [Let's code a TCP/IP stack, 1: Ethernet & ARP](http://www.saminiir.com/lets-code-tcp-ip-stack-1-ethernet-arp/)
* [Let's code a TCP/IP stack, 2: IPv4 & ICMPv4](http://www.saminiir.com/lets-code-tcp-ip-stack-2-ipv4-icmpv4/)
* [Let's code a TCP/IP stack, 3: TCP Basics & Handshake](http://www.saminiir.com/lets-code-tcp-ip-stack-3-tcp-handshake/)
* [Let's code a TCP/IP stack, 4: TCP Data Flow & Socket API](http://www.saminiir.com/lets-code-tcp-ip-stack-4-tcp-data-flow-socket-api/)
* [Let's code a TCP/IP stack, 5: TCP Retransmission](http://www.saminiir.com/lets-code-tcp-ip-stack-5-tcp-retransmission/)

对于系统知识，主要有以下一些学习要点：
* 用这些系统知识操作一下文件系统，实现一个可以拷贝目录树的小程序。
* 用 fork / wait / waitpid 写一个多进程的程序，用 pthread 写一个多线程带同步或互斥的程序。比如，多进程购票的程序。
* 用 signal / kill / raise / alarm / pause / sigprocmask 实现一个多进程间的信号量通信的程序。
* 学会使用 gcc 和 gdb 来编程和调试程序（参看耗子叔的《用 gdb 调试程序》[一](https://blog.csdn.net/haoel/article/details/2879)、[二](https://blog.csdn.net/haoel/article/details/2880)、[三](https://blog.csdn.net/haoel/article/details/2881)、[四](https://blog.csdn.net/haoel/article/details/2882)、[五](https://blog.csdn.net/haoel/article/details/2883)、[六](https://blog.csdn.net/haoel/article/details/2884)、[七](https://blog.csdn.net/haoel/article/details/2885)）。
* 学会使用 makefile 来编译程序（参看《跟我一起写 makefile》[一](https://blog.csdn.net/haoel/article/details/2886)、[二](https://blog.csdn.net/haoel/article/details/2887)、[三](https://blog.csdn.net/haoel/article/details/2888)、[四](https://blog.csdn.net/haoel/article/details/2889)、[五](https://blog.csdn.net/haoel/article/details/2890)、[六](https://blog.csdn.net/haoel/article/details/2891)、[七](https://blog.csdn.net/haoel/article/details/2892)、[八](https://blog.csdn.net/haoel/article/details/2893)、[九](https://blog.csdn.net/haoel/article/details/2894)、[十](https://blog.csdn.net/haoel/article/details/2895)、[十一](https://blog.csdn.net/haoel/article/details/2896)、[十二](https://blog.csdn.net/haoel/article/details/2897)、[十三](https://blog.csdn.net/haoel/article/details/2898)、[十四](https://blog.csdn.net/haoel/article/details/2899)）。
* Socket 的进程间通信。用 C 语言写一个 1 对 1 的聊天小程序，或是一个简单的 HTTP 服务器。

**C10K 问题**
> 学习完《Unix 网络编程》后，一定(是时候)要研究一下`C10K`了，读一下 "[C10K Problem](http://www.kegel.com/c10k.html) ([中文翻译版](https://www.oschina.net/translate/c10k))"。C10K 问题本质上是操作系统处理大并发请求的问题。对于 Web 时代的操作系统而言，对于客户端过来的大量的并发请求，需要创建相应的服务进程或线程。这些进程或线程多了，导致数据拷贝频繁（缓存 I/O、内核将数据拷贝到用户进程空间、阻塞）， 进程 / 线程上下文切换消耗大，从而导致资源被耗尽而崩溃。这就是 C10K 问题的本质。了解这个问题，并了解操作系统是如何通过多路复用的技术来解决这个问题的，有助于你了解各
种 I/O 和异步模型。现在，整个世界都在解决`C10M`问题，推荐阅读 "[The Secret To 10 Million Concurrent Connections -The Kernel Is The Problem, Not The Solution](http://highscalability.com/blog/2013/5/13/the-secret-to-10-million-concurrent-connections-the-kernel-i.html)"

* 推荐文章资料：《[C10K问题系列文章](http://www.52im.net/thread-561-1-1.html)》


**实践项目**

A、实现一个 telnet 版本的聊天服务器，主要有以下需求：
* 每个客户端可以使用 telnet ip:port 的方式连接到服务器上
* 新连接需要用户名和密码登录，如果没有，则需要注册一个
* 然后可以选择一个聊天室加入聊天
* 管理员有权创建或删除聊天室，普通人员只有加入、退出、查询聊天室的权利
* 聊天室需要有人数限制，每个人发出来的话，其它所有的人都要能看得到

B、实现一个简单的 HTTP 服务器，主要有以下需求：
* 解释浏览器传来的 HTTP 协议，只需要处理 URL path
* 然后把所代理的目录列出来
* 在浏览器上可以浏览目录里的文件和下级目录
* 如果点击文件，则把文件打开传给浏览器(浏览器能够自动显示图片、PDF或HTML、CSS、JavaScript以及文本文件)
* 如果点击子目录，则进入到子目录中，并把子目录中的文件列出来

C、实现一个生产者 / 消费者消息队列服务，主要有以下需求：
* 消息队列采用一个 Ring-buffer 的数据结构
* 可以有多个 topic 供生产者写入消息及消费者取出消息
* 需要支持多个生产者并发写
* 需要支持多个消费者消费消息(只要有一个消费者成功处理消息就可以删除消息)
* 消息队列要做到不丢数据(要把消息持久化下来)
* 能做到性能很高

**给一些建议的方向**
* 底层方向：操作系统、文件系统、数据库、网络...
* 架构方向：分布式系统架构、微服务、DevOps、Cloud Native...
* 数据方向：大数据、机器学习、人工智能...
* 前端方向：你对用户体验或是交互更感兴趣，那么走前端的路吧
* 其它方向：安全开发、运维开发、嵌入式开发





<a id='ruanjianshejipian'/>

# 四、软件设计篇



<a id='gaoshouchengzhangpian'/>

# 五、高手成长篇


# 学习建议
* 一定要坚持，要保持长时间学习，甚至终生学习的态度。
* 一定要动手，不管例子多么简单，建议至少自己手动敲一遍看看是否理解里边的细枝末节。
* 一定要学会思考，思考为什么要这样，而不是那样，还要会举一反三地思考。
* 不要乱买书，不要乱追新技术新名词，基础的东西经过很长时间积累，会在未来至少 10 年通用。


