> CentOS 6 系统默认 Python 版本是：2.6.6 平时在使用中遇到很多的库要求是 2.7.x 版本的库，比如使用 ConfigParser 库，在 2.6 版本库就不支持没有 value 值的配置项，需要升级到 2.7 以上的库才行，这次就尝试升级一下 Python 到 2.7.x 版本，记录于此。

## 升级 Python 2.7.10 版本
1、**准备安装包**

下载安装依赖的相关包
```sh
$ yum install vim gcc make wget -y
$ yum install openssl-devel zlib-devel readline-devel sqlite-devel -y
```

下载解压
```sh
$ cd /usr/local/src
$ wget https://www.python.org/ftp/python/2.7.10/Python-2.7.10.tgz
$ tar -zxvf Python-2.7.10.tgz
$ ls
Python-2.7.10  Python-2.7.10.tgz
```

2、**编译安装**
```sh
$ cd Python-2.7.10
$ ./configure --enable-shared --enable-loadable-sqlite-extensions \
    --prefix=/usr/local/python27 --with-zlib --with-ssl
$ vim ./Modules/Setup    # 找到下边这一行内容，去掉注释
#zlib zlibmodule.c -I$(prefix)/include -L$(exec_prefix)/lib -lz
$ make && make install
```

3、**查看 python 版本信息**

```sh
$ python -V
Python 2.6.6
# 版本依旧是 2.6.6
```

4、**用 python2.7 为默认版本**
```sh
$ cd /usr/bin/
$ ls python* -l   # 旧 python 版本信息
-rwxr-xr-x. 2 root root 4864 2月  22 2013  python
lrwxrwxrwx. 1 root root    6 10月 22 18:38 python2 -> python
-rwxr-xr-x. 2 root root 4864 2月  22 2013  python2.6
```
设置软连接
```sh
$ mv /usr/bin/python /usr/bin/python2.6.6
$ ln -s /usr/local/python27/bin/python2.7 /usr/bin/python
$ ls python* -l
lrwxrwxrwx. 1 root root   33 10月 23 00:01 python -> /usr/local/python27/bin/python2.7
lrwxrwxrwx. 1 root root    6 10月 22 18:38 python2 -> python
-rwxr-xr-x. 2 root root 4864 2月  22 2013 python2.6
-rwxr-xr-x. 2 root root 4864 2月  22 2013 python2.6.6
```

5、**重新验证 Python 版本信息**

```sh
$ python -V
Python 2.7.10
```

可以看到，系统识别的 python 版本已经是 python 2.7.10。执行 python -V 遇到的问题：
```sh
python: error while loading shared libraries: libpython2.7.so.1.0: \
        cannot open shared object file: No such file or directory
# 原因：linux系统默认没有把/usr/local/python27/lib路径加入动态库搜索路径
```

解决问题：
```sh
$ vim /etc/ld.so.conf
# 添加如下一行内容
/usr/local/python27/lib
$ ldconfig  # 使新添加的路径生效
```

## 解决 yum 兼容性问题
因为 yum 是不兼容 Python 2.7 的，所以 yum 不能正常工作，我们需要指定 yum 的 Python 为 2.6。

1、**升级 python 后 yum 出现的问题**
```sh
$ yum 
There was a problem importing one of the Python modules
required to run yum. The error leading to this problem was:
 No module named yum
... ... ... ...
```

2、**编辑 yum 配置文件**
```sh
$ vim /usr/bin/yum
#!/usr/bin/python
# 第一行修改为 python2.6.6
#!/usr/bin/python2.6.6
```

3、**验证 yum 问题解决**
```sh
$ yum repolist
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
... ... ... ...
```

## 安装升级 pip 工具
1、**下载安装**
```sh
$ wget https://bootstrap.pypa.io/get-pip.py
$ python get-pip.py
```
2、**设置软连接**
```sh
$ ln -s /usr/local/python27/bin/pip2.7 /usr/bin/pip
```

## 安装 ipython
```sh
$ pip install ipython==1.2.1
$ ln -s /usr/local/python27/bin/ipython /usr/bin/ipython
```
