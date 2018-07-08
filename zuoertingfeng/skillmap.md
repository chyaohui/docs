目录
---
* [入门篇](#rumenpian)
* [修养篇](#xiuyangpian)
* [专业基础篇](#zhuanyejichupian)
* [软件设计篇](#ruanjianshejipian)
* [高手成长篇](#gaoshouchengzhangpian)


> 程序员练级攻略


<a id='rumenpian'/>

# 一、入门篇
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



## 2. 正式入门



<a id='xiuyangpian'/>

# 二、修养篇



<a id='zhuanyejichupian'/>

# 三、专业基础篇



<a id='ruanjianshejipian'/>

# 四、软件设计篇



<a id='gaoshouchengzhangpian'/>

# 五、高手成长篇


# 学习建议
* 一定要坚持，要保持长时间学习，甚至终生学习的态度。
* 一定要动手，不管例子多么简单，建议至少自己手动敲一遍看看是否理解里边的细枝末节。
* 一定要学会思考，思考为什么要这样，而不是那样，还要会举一反三地思考。
* 不要乱买书，不要乱追新技术新名词，基础的东西经过很长时间积累，会在未来至少 10 年通用。


