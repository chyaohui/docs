> 平时工作或学习过程中工具使用总结

# 1.工具类
## 1)SQLite数据库的可视化管理工具(SQLite Manager)
- 依赖：Firefox浏览器（以浏览器插件的形式）
- Firefox浏览器插件管理器中搜索"SQLite", 安装即可
- 打开使用：菜单栏 --> 工具 --> 'SQLite Manager' --> 即可打开工具 --> 打开对应文件

## 2)Ubuntu安装SecureCRT
下载软件包
```bash
$ wget scrt-sfx-7.3.4-839.ubuntu13-64.x86_64.deb
$ wget securecrt_linux_crack.pl
```
安装SecureCRT
```bash
$ sudo dpkg -i scrt-sfx-7.3.4-839.ubuntu13-64.x86_64.deb
$ sudo perl securecrt_linux_crack.pl /usr/bin/SecureCRT    #执行破解
```
破解完成之后，会提供一些注册信息. enter license data按照破解信息依次输入即可.

## 3)安装kvm环境
```bash
$ sudo apt-get install qemu-kvm qemu-system libvirt-bin virt-manager bridge-utils vlan
```


# 2.知识类
