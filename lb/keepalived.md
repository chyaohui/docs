> keepalived 的相关文档总结

## 环境说明
* 版本：keepalived-1.2.2_LVS
* 依赖：`openssl-devel`、`kernel`、`kernel-devel`、`popt`、`popt-devel`、`libnl`、`libnl-devel`
* 内核：2.6.32-642.15.1.sina11.2.el6.x86_64


## 打包 rpm 和 srpm

### 1、准备rpmbuild环境
```sh
$ cd /root
$ mkdir -p rpmbuild/{BUILD,BUILDROOT,RPMS,SOURCES,SPECS,SRPMS}
```

### 2、打包 keepalived 为 tgz 格式
修改 keepalived.spec 文件关于内核版本的参数
```sh
$ vim keepalived.spec
$ %define kernel `2.6.32-642.15.1.sina11.2.el6.x86_64`    # uname -r
```

打包并拷贝至 rpmbuild 目录
```sh
$ cd /root/
$ tar zcf keepalived-1.2.2.tar.gz keepalived-1.2.2
$ ls keepalived-1.2.2.tar.gz
  keepalived-1.2.2.tar.gz

$ mv keepalived-1.2.2.tar.gz /root/rpmbuild/SOURCES/
```

### 3、执行 rpmbuild 操作
```sh
$ rpmbuild -ta /root/rpmbuild/SOURCES/keepalived-1.2.2.tar.gz
Checking for unpackaged file(s): /usr/lib/rpm/check-files /root/rpmbuild/BUILDROOT/keepalived-1.2.2-101.x86_64
Wrote: /root/rpmbuild/SRPMS/keepalived-1.2.2-101.src.rpm
Wrote: /root/rpmbuild/RPMS/x86_64/keepalived-1.2.2-101.x86_64.rpm
Wrote: /root/rpmbuild/RPMS/x86_64/keepalived-debuginfo-1.2.2-101.x86_64.rpm
Executing(%clean): /bin/sh -e /var/tmp/rpm-tmp.MYoEiq
+ umask 022
+ cd /root/rpmbuild/BUILD
+ cd keepalived-1.2.2
+ /bin/rm -rf /root/rpmbuild/BUILDROOT/keepalived-1.2.2-101.x86_64
+ exit 0
# 看到上述信息说明 rpmbuild 成功编译
```

rpmbuild 参数说明：
* -ta: 编译 srpm 和 rpm，通过指定参数 xxx.tar.gz 压缩包
* -ba: 编译 srpm 和 rpm，通过指定参数 xxx.spec 文件


在 CentOS5 上打包 rpm：
* 主机："lvs.147.g1.se" - "172.16.194.147"
* 打包方式：rpm -ba /usr/src/redhat/SPECS/keepalived.spec
* 源码包存放位置：/usr/src/redhat/SOURCES/keepalived_LVS-1.2.2.tar.gz
* 内核指定："2.6.18-238.el5"

### 4、查看结果
```sh
$ cd /root/rpmbuild
$ tree RPMS  SOURCES  SPECS  SRPMS
RPMS
└── x86_64
    ├── keepalived-1.2.2-101.x86_64.rpm                 # 生成的 rpm 包
    └── keepalived-debuginfo-1.2.2-101.x86_64.rpm
SOURCES
└── keepalived-1.2.2.tar.gz
SPECS
SRPMS
└── keepalived-1.2.2-101.src.rpm                        # 生成的 srpm 包

1 directory, 4 files
```


## 使用说明
### 1、新增功能说明
* 新增 `-tf` 选项，校验 lvs.conf 配置的合法性
* 新增 `kill HUP` 配置文件加载时的校验功能，若配置有效，则加载生效；若配置有问题，则本次配置不生效，仍使用旧配置。
* conf2json 程序能将 lvs.conf 和 vip.conf 配置文件转换输出为 json 格式。


### 2、关于配置的特殊事项
* 配置行最右边的左大括号 `{` 与前边字符必须加 `空格`，最好不要将 `{` 另起一行。
* `TCP_CHECK` 类型的健康检查无参数 `nb_get_retry` 。
* `sorry_server` 只支持一个，如果写多个 `sorry_server`，只有最后一个的起作用。
* `connect_timeout` 参数支持最大值设置为：200


