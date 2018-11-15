> Nginx 配置文件中常用参数的含义总结。

## 全局配置

* user nobody;

  定义运行 nginx 服务进程的用户，还可以加上组，如：user nobody nobody;
* worker_processes 1;
  
  定义 worker 进程的数量，一般设置和 CPU 核数保持一致。还可以设置为 auto，表示让系统自动调整。
* error_log logs/error.log;

  定义错误日志路径，也可以定义到 http、server、location 里。
* error_log logs/error.log notice;

  定义错误日志级别，级别越高记录信息越少，默认是error，常见的错误日志级别有：
  ```
  [debug|info|notice|warn|error|crit|alert|emerg]
  ```
* pid logs/nginx.pid;

  定义nginx进程pid文件所在路径。
* worker_rlimit_nofile 100000;

  定义nginx最多打开文件数限制。如果没设置的话，这个值为操作系统(ulimit -n)的限制保持一致。把这个值设高，nginx就不会有 "too many open files" 问题了。


## events 配置部分

* worker_connections 1024;

  定义每个 work_process 同时开启的最大连接数，即允许最多只能有这么多连接。
* accept_mutex on;

  