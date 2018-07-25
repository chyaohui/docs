目录
---
* [入门篇](#一入门篇)
  - [零基础启蒙](#1-零基础启蒙)
  - [正式入门](#2-正式入门)
* [修养篇](#二修养篇)
* [专业基础篇](#三专业基础篇)
  - [编程语言](#1-编程语言)
  - [理论学科](#2-理论学科)
  - [系统知识](#3-系统知识)
* [软件设计篇](#四软件设计篇)
* [高手成长篇](#五高手成长篇)


> 程序员练级攻略。


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

> 学习软件设计的方法、理念、范式和模式，是让你从一个程序员通向工程师的必备技能。Linus 说过，这世界程序员之所有高下之分，最大的区别就是程序员的「品味」不一样。而软件设计这件事情，需要自己用实践、用时间、用错误、用教训、用痛苦才能真正体会其中的精髓。除了理论知识外，还需要大量的工程实践，需要有足够的耐心和恒心。

### 编程范式
学习编程范式可以让你明白编程的本质和各种语言的编程方式。
* 陈皓(左耳朵耗子)在极客时间写的《编程范式游记》系列文章，目录如下：
  - [编程范式游记(1) - 起源](https://time.geekbang.org/column/article/301)
  - [编程范式游记(2) - 泛型编程](https://time.geekbang.org/column/article/303)
  - [编程范式游记(3) - 类型系统和泛型的本质](https://time.geekbang.org/column/article/2017)
  - [编程范式游记(4) - 函数式编程](https://time.geekbang.org/column/article/2711)
  - [编程范式游记(5) - 修饰器模式](https://time.geekbang.org/column/article/2723)
  - [编程范式游记(6) - 面向对象编程](https://time.geekbang.org/column/article/2729)
  - [编程范式游记(7) - 基于原型的编程范式](https://time.geekbang.org/column/article/2741)
  - [编程范式游记(8) - Go 语言的委托模式](https://time.geekbang.org/column/article/2748)
  - [编程范式游记(9) - 编程的本质](https://time.geekbang.org/column/article/2751)
  - [编程范式游记(10) - 逻辑编程范式](https://time.geekbang.org/column/article/2752)
  - [编程范式游记(11) - 程序世界里的编程范式](https://time.geekbang.org/column/article/2754)
* [Wikipedia: Programming paradigm](https://en.wikipedia.org/wiki/Programming_paradigm)，顺着页面看下去，很多有用的编程相关知识
* [六个编程范型将改变你对编程的看法](https://my.oschina.net/editorial-story/blog/890965) - ([英文版](https://www.ybrikman.com/writing/2014/04/09/six-programming-paradigms-that-will/))，文章讲了 6 种不太常见的编程范式，并结合一些没怎么听过的语言来分别进行描述
  - 默认支持并发 (Concurrent by default)
  - 依赖类型 (Dependent types)
  - 连接性语言 (Concatenative languages)
  - 声明式编程 (Declarative programming)
  - 符号式编程 (Symbolic programming)
  - 基于知识的编程 (Knowledge-based programming)
* [Programming Paradigms for Dummies: What Every Programmer Should Know](https://www.info.ucl.ac.be/~pvr/VanRoyChapter.pdf)
* [斯坦福大学公开课：编程范式](http://open.163.com/special/opencourse/paradigms.html)

### 一些软件设计的相关原则
* [Don't Repeat Yourself](http://en.wikipedia.org/wiki/Don%27t_repeat_yourself)，DRY 是一个最简单的法则，也最容易被理解的，但也可能是最难被应用的(因为要做这样，我们需要在范型设计上相当努力，这并不是一件容易的事)。它意味着，当在两个或多个地方发现一些相似的代码的时候，我们需要把它们的共性抽象出来形成一个唯一的新方法，并且改变现有地方的代码让它们以一些合适的参数调用这个新的方法。
* [Keep It Simple, Stupid(KISS)](http://en.wikipedia.org/wiki/KISS_principle)
* Program to an interface, not an implementation。
  - 这是设计模式中最根本的哲学，注重接口，而不是实现，依赖接口，而不是实现。
  - 接口是抽象是稳定的，实现则是多种多样的。
  - 在面向对象的 `S.O.L.I.D` 原则中会提到我们的依赖倒置原则，就是这个原则的另一种样子。
  - 还有一条原则叫 Composition over inheritance (喜欢组合而不是继承)
* [You Ain't Gonna Need It(YAGNI)](http://en.wikipedia.org/wiki/You_Ain%27t_Gonna_Need_It)
  - 这个原则简而言之为 -- 只考虑和设计必须的功能，避免过度设计。
  - 只实现目前需要的功能，在以后你需要更多功能时，可以再进行添加。
  - 如无必要，勿增复杂性。软件开发是一场 trade-off 的博弈。
* [Law of Demeter](http://en.wikipedia.org/wiki/Principle_of_Least_Knowledge)，迪米特法则，又称 "最少知识原则" ，对于对象 "O" 中一个方法 "M" ，"M" 应该只能够访问以下对象中的方法：
  - 对象 "O"
  - 与 "O" 直接相关的 Component Object
  - 由方法 "M" 创建或实例化的对象
  - 作为方法 "M" 的参数的对象
* [面向对象的 S.O.L.I.D 原则](https://en.wikipedia.org/wiki/Solid_objectoriented_design)
  - SRP(Single Responsibility Principle)职责单一原则。核心思想是：一个类，只做一件事，并把这件事做好，其只有一个引起它变化的原因。职责过多，可能引起它变化的原因就越多，这将导致职责依赖，相互之间就产生影响，从而极大地损伤其内聚性和耦合度。
  - OCP(Open/Closed Principle) - 开闭原则。其核心思想是：模块是可扩展的，而不可修改的。也就是说，对扩展是开放的，而对修改是封闭的。对扩展开放，意味着有新的需求或变化时，可以对现有代码进行扩展，以适应新的情况。对修改封闭，意味着类一旦设计完成，就k可以独立完成其工作，而不要对类进行任何修改。
  - LSP(Liskov substitution principle) - 里氏代换原则。子类必须能够替换成它们的基类，即子类应该可以替换任何基类能够出现的地方，且经过替换后，代码还能正常工作。另，不应该在代码中出现 `if/else` 之类对子类类型进行判断的条件。正是由于子类型的可替换性才使得父类型的模块在无需修改的情况下就可以扩展
  - ISP(Interface Segregation Principle) - 接口隔离原则。接口隔离原则的意思是把功能实现在接口中，而不是类中，使用多个专门的接口比使用单一的总接口要好。
  - DIP(Dependency Inversion Principle) - 依赖倒置原则。高层模块不应该依赖于底层模块的实现，而是依赖于高层抽象。
* [CCP](http://c2.com/cgi/wiki?CommonClosurePrinciple)(Common Closure Principle) - 共同封闭原则。
* [CRP](http://c2.com/cgi/wiki?CommonReusePrinciple)(Common Reuse Principle) - 共同重用原则。
* [好莱坞原则](http://en.wikipedia.org/wiki/Hollywood_Principle) - Hollywood Principle
* [高内聚，低耦合](http://en.wikipedia.org/wiki/Coupling_computer_science)。把模块间的耦合降到最低，而努力让一个模块做到精益求精。内聚，指一个模块内各个元素彼此结合的紧密程度；耦合指一个软件结构内不同模块之间互连程度的度量。内聚意味着重用和独立，耦合意味着多米诺效应--牵一发动全身。
* [CoC](http://en.wikipedia.org/wiki/Convention_over_Configuration) - 惯例优于配置原则。简单点说就是将一些公认的配置方式和信息作为内部缺省的规则来使用。
* [SoC](http://sulong.me/archives/99) - 关注点分离。就是在软件开发中，通过各种手段，将问题的各个关注点分开。如果一个问题能分解为独立且较小的问题，就是相对较易解决的。
* [Dbc](http://en.wikipedia.org/wiki/Design_by_contract) - 契约式设计。
* [ADP](http://c2.com/cgi/wiki?AcyclicDependenciesPrinciple) - 无环依赖原则。包(或服务)之间的依赖结构必须是一个直接的无环图形，也就是说不允许出现循环依赖。

### 一些软件设计的读物
* 《[领域驱动设计](https://book.douban.com/subject/26819666/)》
* 《[UNIX 编程艺术](https://book.douban.com/subject/1467587/)》
* 《[Clean Architecture](https://8thlight.com/blog/uncle-bob/2012/08/13/the-clean-architecture.html)》
* [The Twelve-Factor App](https://12factor.net/)，[中文版](https://12factor.net/zh_cn/) - 架构师必读
* [Avoid Over Engineering](https://medium.com/@rdsubhas/10-modern-software-engineering-mistakes-bc67fbef4fc8) - 避免过度设计系统
* [Instagram Engineering’s 3 rules to a scalable cloud application architecture](https://medium.com/@DataStax/instagram-engineerings-3-rules-to-a-scalable-cloud-application-architecture-c44afed31406)。Instagram 工程的三个黄金法则：
  - 使用稳定可靠的技术(迎接新的结束)
  - 不要重新发明轮子
  - Keep it very simple
  - 其实，Amazon 也有两条工程法则，一个是自动化，一个是简化
* [How To Design A Good API and Why it Matters - Joshua Bloch](https://www.infoq.com/presentations/effective-api-design)，Google 的一个分享，关于如何设计好一个 API。
* 关于 Restful API 的设计，可以学习并借鉴下面这些文章：
  - [Best Practices for Designing a Pragmatic RESTful API](https://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api)
  - [Ideal REST API design](https://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api)
  - [HTTP API Design Guide](https://github.com/interagent/http-api-design)
  - [Microsoft REST API Guidelines](https://github.com/Microsoft/api-guidelines/blob/vNext/Guidelines.md)
  - [IBM Watson REST API Guidelines](https://github.com/watson-developer-cloud/api-guidelines)
  - [Zalando RESTful API and Event Scheme Guidelines](https://opensource.zalando.com/restful-api-guidelines/)
* [The Problem With Logging](https://blog.codinghorror.com/the-problem-with-logging/)。关于程序打日志的短文，可以让你知道一些可能以往不知道的打日志需要注意的问题。
* [Concurrent Programming for Scalable Web Architectures](http://berb.github.io/diploma-thesis/community/index.html)，这是一本在线的免费书，
教你如何架构一个可扩展的高性能的网站。其中谈到了一些不错的设计方法和知识。


<a id='gaoshouchengzhangpian'/>

# 五、高手成长篇
## 1、Linux 系统、内存和网络

### 1.1 Linux 系统相关
* [RedHat Enterprise Linux 文档](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/?version=7)
* [Linux Insides](https://github.com/0xAX/linux-insides)。Github 上的一个开源电子书，讲述了 Linux 内核是怎样启动、初始化以及进行管理的。
* [LWN's kernel page](https://github.com/0xAX/linux-insides)。上面很多文章来解释 Linux 内核的一些东西。
* [Learn Linux Kernel from Android Perspective](http://learnlinuxconcepts.blogspot.com/2014/10/this-blog-is-to-help-those-students-and.html)。从 Android 角度来学习 Linux 内核。
* [Linux Kernel Doc](https://www.kernel.org/doc/)。Linux 的内核文档也可以浏览一下。
* [Kernel Planet](http://planet.kernel.org/)。Linux 内核开发者的 Blog，有很多很不错的文章和想法。
* [Linux Performance and Tuning Guidelines](https://lenovopress.com/redp4285.pdf)，IBM 出的红皮书。
* [TLK: The Linux Kernel](http://tldp.org/LDP/tlk/tlk.html)
* [Linux Performance](http://www.brendangregg.com/linuxperf.html)，提供了和 Linux 系统性能相关的各种工具和文章收集。
* [Optimizing web servers for high throughput and low latency](http://www.brendangregg.com/linuxperf.html)，非常底层的系统调优的文章。


### 1.2 内存相关
首先，LWN.net 系列文章 "What every programmer should know about memory" 阅读，下面是系列文章的网页版列表(完整的[PDF 文档](http://futuretech.blinkenlights.nl/misc/cpumemory.pdf))：
* [Part 1: (Introduction)](https://lwn.net/Articles/250967/) ，中译版为 [每个程序员都应该了解的内存知识【第一部分】](https://www.oschina.net/translate/what-every-programmer-should-know-about-memory-part1)
* [Part 2: (CPU caches)](https://lwn.net/Articles/252125/)
* [Part 3 (Virtual memory)](http://lwn.net/Articles/253361/)
* [Part 4 (NUMA systems)](http://lwn.net/Articles/254445/)
* [Part 5 (What programmers can do - cache optimization)](http://lwn.net/Articles/255364/)
* [Part 6 (What programmers can do - multi-threaded optimizations)](http://lwn.net/Articles/256433/)
* [Part 7 (Memory performance tools)](http://lwn.net/Articles/257209/)
* [Part 8 (Future technologies)](https://lwn.net/Articles/258154/)
* [Part 9 (Appendices and bibliography)](https://lwn.net/Articles/258188/)

然后后是几篇和内存相关的论文，对于程序的性能优化方面有帮助。
* [Memory Barriers: a Hardware View for Software Hackers](http://irl.cs.ucla.edu/~yingdi/web/paperreading/whymb.2010.06.07c.pdf)。内存的读写屏障是线程并发访问共享的内存数据时，从程序本身、编译器到 CPU 都必须遵循的一个规范。有了这个规范，才能保证访问共享的内存数据时，一个线程对该数据的更新能被另一个线程以正确的顺序感知到。在 SMP（对称多处理）这种类型的多处理器系统（包括多核系统）上，这种读写屏障还包含了复杂的缓存一致性策略。这篇文章做了详细解释。
* [A Tutorial Introduction to the ARM and POWER Relaxed Memory Models](http://www.cl.cam.ac.uk/~pes20/ppc-supplemental/test7.pdf)，对 ARM 和 POWER 的宽松内存模型的一个教程式的简介。
* [x86-TSO: A Rigorous and Usable Programmer’s Model for x86 Multiprocessors](http://www.cl.cam.ac.uk/~pes20/weakmemory/cacm.pdf)，介绍 x86 的多处理器内存并发访问的一致性模型 TSO。

接下来是内存管理方面的 lib 库，通常来说，我们有三种内存分配管理模块，就目前而言，BSD 的 jemalloc 有很大的影响力。
* [ptmalloc](http://www.malloc.de/en/) 是 glibc 的内存分配管理。
* [tcmalloc](https://github.com/gperftools/gperftools) 是 Google 的内存分配管理模块，全称是 Thread-Caching malloc，基本上来说比 glibc 的 ptmalloc 快两倍以上。
* [jemalloc](http://jemalloc.net/) 是 BSD 提供的内存分配管理。其论文为 [A Scalable Concurrent malloc(3) Implementation for FreeBSD](https://people.freebsd.org/~jasone/jemalloc/bsdcan2006/jemalloc.pdf)，这是一个可以并行处理的内存分配管理器。
* 关于 C 的这些内存分配器，你可以参看 Wikipedia 的 [C Dynamic Memory Allocation](https://en.wikipedia.org/wiki/C_dynamic_memory_allocation#Thread-caching_malloc_(tcmalloc)) 这个词条。

感觉一下上面那三种内存分配器的一些比较和工程实践：
* [ptmalloc，tcmalloc 和 jemalloc 内存分配策略研究](https://owent.net/2013/867.html)
* [内存优化总结：ptmalloc、tcmalloc 和 jemalloc](http://www.cnhalo.net/2016/06/13/memory-optimize/)
* [Scalable memory allocation using jemalloc](https://www.facebook.com/notes/facebook-engineering/scalable-memory-allocation-using-jemalloc/480222803919)
* [Decreasing RAM Usage by 40% Using jemalloc with Python & Celery](https://zapier.com/engineering/celery-python-jemalloc/)


### 1.3 计算机网络
**网络学习**
* 推荐一本书，《[计算机网络(第5版)](https://book.douban.com/subject/10510747/)》
* 渥汰华大学的一个课程讲议你也可以一看 [Computer Network Design](http://www.site.uottawa.ca/~shervin/courses/ceg4185/lectures/) 。
* GeeksforGeeks 上也有一个简单的 [Computer Network Tutorials](http://www.site.uottawa.ca/~shervin/courses/ceg4185/lectures/) 。


**网络调优**，推荐一些非常实用的可以操作的技术：
* 《Linux 的高级路由和流量控制 HowTo》（[Linux Advanced Routing & Traffic Control HOWTO](http://lartc.org/) ），这是一个非常容易上手的关于 iproute2、流量整形和一点 netfilter 的指南。
* 关于网络调优，你可以看一下这个文档 [Red Hat Enterprise Linux Network Performance Tuning Guide](https://access.redhat.com/sites/default/files/attachments/20150325_network_performance_tuning.pdf)。
* 还有一些网络工具能够帮上你的大忙，这里有一个网络工具的 Awesome 列表 [Awesome Pcap Tools](https://github.com/caesar0301/awesome-pcaptools) ，其中罗列了各种网络工具，能够让你更从容地调试网络相关的程序。
* [Making Linux TCP Fast](https://netdevconf.org/1.2/papers/bbr-netdev-1.2.new.new.pdf) ，一篇非常不错的 TCP 调优的论文。
* 下面是在 PackageCloud 上的两篇关于 Linux 网络栈相关的底层文章，非常值得一读。
  - [Monitoring and Tuning the Linux Networking Stack: Receiving Data](https://blog.packagecloud.io/eng/2016/06/22/monitoring-tuning-linux-networking-stack-receiving-data/)
  - [Monitoring and Tuning the Linux Networking Stack: Sending Data](https://blog.packagecloud.io/eng/2017/02/06/monitoring-tuning-linux-networking-stack-sending-data/)


**网络协议**
> 学习网络协议最好的方式就是学习通讯相关的 RFC，读 RFC 有几个好处，一方面可以学习技术，另一方面可以通过 RFC 学习到一个好的技术文档是怎么写的，还能看到各种解决问题的方案和思路。

对于第 2 层链路层，可以需要了解一下 ARP：
* [RFC 826 - An Ethernet Address Resolution Protocol](https://tools.ietf.org/html/rfc826)

Tunnel 相关协议：
* [RFC 1853 - IP in IP Tunneling](https://tools.ietf.org/html/rfc1853)
* [RFC 2784 - Generic Routing Encapsulation (GRE)](https://tools.ietf.org/html/rfc2784)
* [RFC 2661 - Layer Two Tunneling Protocol “L2TP”](https://tools.ietf.org/html/rfc2661)
* [RFC 2637 - Point-to-Point Tunneling Protocol (PPTP)](https://tools.ietf.org/html/rfc2637)

---
对于第 4 层，最需要了解的是 TCP/IP ，下边的 RFC 在 CoolShell 上的《[TCP 的那些事儿(上)](https://coolshell.cn/articles/11564.html)》《[TCP 的那些事儿(下)](https://coolshell.cn/articles/11609.html)》两篇文章中都有引用。
* [RFC 793 - Transmission Control Protocol](https://tools.ietf.org/html/rfc793) - 最初的 TCP 标准定义，但不包括 TCP 相关细节。
* [RFC 813 - Window and Acknowledgement Strategy in TCP](https://tools.ietf.org/html/rfc813) - TCP 窗口与确认策略，并讨论了在使用该机制时可能遇到的问题及解决方法。
* [RFC 879 - The TCP Maximum Segment Size and Related Topics](https://tools.ietf.org/html/rfc879) - 讨论 MSS 参数对控制 TCP 分组大小的重要性，以及该参数与 IP 分段大小的关系等。
* [RFC 896 - Congestion Control in IP/TCP Internetworks](https://tools.ietf.org/html/rfc896) - 讨论拥塞问题和 TCP 如何控制拥塞。
* [RFC 2581 - TCP Congestion Control](https://tools.ietf.org/html/rfc2581) - 描述用于拥塞控制的四种机制：慢启动、拥塞防御、快重传和快恢复。后面这个 RFC 被 [RFC 5681](https://tools.ietf.org/html/rfc5681) 所更新。还有 [RFC 6582 - The NewReno Modification to TCP’s Fast Recovery Algorithm](https://tools.ietf.org/html/rfc6582) 中一个改进的快速恢复算法。
* [RFC 2018 - TCP Selective Acknowledgment Options](https://tools.ietf.org/html/rfc2018) - TCP 的选择确认。
* [RFC 2883 - An Extension to the Selective Acknowledgement (SACK) Option for TCP](https://tools.ietf.org/html/rfc2883) - 对于 RFC 2018 的改进。
* [RFC 2988 - Computing TCP’s Retransmission Timer](https://tools.ietf.org/html/rfc2988) - 讨论与 TCP 重传计时器设置相关的话题，重传计时器控制报文在重传前应等待多长时间。也就是经典的 TCP Karn/Partridge 重传算法。
* [RFC 6298 - Computing TCP’s Retransmission Timer](https://tools.ietf.org/html/rfc6298) - TCP Jacobson/Karels Algorithm 重传算法。
* [Congestion Avoidance and Control](http://ee.lbl.gov/papers/congavoid.pdf) - 个人觉得 TCP 最牛的不是不丢包，而是拥塞控制，可以读一下经典论文
* [TCP 的 man page](http://man7.org/linux/man-pages/man7/tcp.7.html) - 关于 Linux 下的 TCP 参数，你需要仔仔细细地读一下

---
对于第 7 层协议，HTTP 协议是重点要学习的。
* 推荐书籍《[HTTP 权威指南](https://book.douban.com/subject/10746113/)》
* HTTP 1.1 的原始 RFC 是 1999 年 6 月的 [RFC 2616](https://tools.ietf.org/html/rfc2616)，但其在 2014 后很快被下面这些 RFC 给取代了。
* [RFC 7230 - Hypertext Transfer Protocol (HTTP/1.1): Message Syntax and Routing](https://tools.ietf.org/html/rfc7230)
* [RFC 7231 - Hypertext Transfer Protocol (HTTP/1.1): Semantics and Content](https://tools.ietf.org/html/rfc7231)
* [RFC 7232 - Hypertext Transfer Protocol (HTTP/1.1): Conditional Requests](https://tools.ietf.org/html/rfc7232)
* [RFC 7233 - Hypertext Transfer Protocol (HTTP/1.1): Range Requests](https://tools.ietf.org/html/rfc7233)
* [RFC 7234 - Hypertext Transfer Protocol (HTTP/1.1): Caching](https://tools.ietf.org/html/rfc7234)
* [RFC 7235 - Hypertext Transfer Protocol (HTTP/1.1): Authentication](https://tools.ietf.org/html/rfc7235)

关于 HTTP/2 它于 2015 年被批准通过，现在基本上所有的主流浏览器都默认启用这个协议
* [Gitbook - HTTP/2 详解](https://legacy.gitbook.com/book/ye11ow/http2-explained/details)
* [http2 explained](http://daniel.haxx.se/http2/)[（中译版）](https://www.gitbook.com/book/ye11ow/http2-explained/details)
* [HTTP/2 for a Faster Web](https://cascadingmedia.com/insites/2015/03/http-2.html)
* [Nginx HTTP/2 白皮书](https://www.nginx.com/wp-content/uploads/2015/09/NGINX_HTTP2_White_Paper_v4.pdf)
* HTTP/2 的两个 RFC：
  - [RFC 7540 - Hypertext Transfer Protocol Version 2 (HTTP/2)](https://httpwg.org/specs/rfc7540.html) ，HTTP/2 的协议本身
  - [RFC 7541 - HPACK: Header Compression for HTTP/2](https://httpwg.org/specs/rfc7541.html) ，HTTP/2 的压缩算法




学习建议
---
* 一定要坚持，要保持长时间学习，甚至终生学习的态度。
* 一定要动手，不管例子多么简单，建议至少自己手动敲一遍看看是否理解里边的细枝末节。
* 一定要学会思考，思考为什么要这样，而不是那样，还要会举一反三地思考。
* 不要乱买书，不要乱追新技术新名词，基础的东西经过很长时间积累，会在未来至少 10 年通用。


