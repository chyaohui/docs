> 平时工作或学习过程中工具使用总结

# 一、工具类
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

## 4)Chrome浏览器牛x插件
- `vimium` 支持在浏览网页时以vim的方式进行各种翻页、点击等快捷操作
- `octotree` 在浏览github网站上的代码时能够以IDE的方式来展示代码目录树
- `Markdown Preview Plus` 可以通过chrome预览markdown文件html格式的插件
- `谷歌访问助手` 提供简单科学上网(google google_email等)

# 二、知识类
## 1) 中国省份信息

4个直辖市:
```python
'北京' '上海' '天津' '重庆'
```
5个自治区:
```python
'宁夏' '广西' '新疆' '西藏' '内蒙古'
```
2个特别行政区:
```python
'香港' '澳门'
```
23个省:
```python
'浙江' '福建' '江苏' '河北' '河南' '湖南' '湖北' '山东' '山西' '陕西' '辽宁' '吉林' '黑龙江' '甘肃' '江西' '安徽' '四川' '贵州' '广东' '青海' '云南' '海南' '台湾'
```

