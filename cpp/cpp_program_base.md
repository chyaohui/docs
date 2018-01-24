> C++编程基础知识点

- [x] 基本介绍
- [ ] 第一个C++程序
- [ ] 名字空间
- [ ] 结构、联合、枚举
- [ ] 布尔类型
- [ ] 操作符别名
- [ ] 重载
- [ ] 缺省参数和哑元
- [ ] 内联
- [ ] 动态内存分配
- [ ] 引用
- [ ] 显式类型转换
- [ ] 来自C++社区的建议

# 一、基本介绍
## 1.C++的发展
- 80年代，贝尔实验室发明C with Classes   --- 本贾尼
- 1983年，正式命名为C++
- 1987年，GNU 推出C++的标准
- 1992年，微软、IBM推出推出他们的标准
- 1998年，ISO推出C++98标准
- 2003年，ISO推出C++03标准
- 2011年，ISO推出C++0x标准

## 2.C++和C语言的联系和区别
- C++包含整个C，C++是建立在C语言基础上的
- C++是强类型语言，比C语言对类型检查更加严格
- C++语言比C语言更加丰富
- C++支持面向对象(C++在宏观上面向对象，微观上是面向过程)
- C++支持运算符重载
- C++支持异常处理
- C++支持泛型编程(类型通用编程)

# 二、第一个C++程序
## 1.编译器g++
也可以用gcc，但要加上-lstdc++，g++其实就是gcc，gcc是一组程序，包括g++
```bash
$ gcc first.cpp -lstdc++
```
## 2.扩展名
常用扩展名：.cpp | .cc | .C | .cxx 推荐使用.cpp。也可以用.c，但是要加上-x c++    
```bash
$ gcc first.c -x c++
```
## 3.头文件
- 头文件位置: /usr/include/c++/4.x
- C++标准的头文件没有.h(与C的头文件没有本质区别): <iostream>
- C的头文件在C++中使用: <cstdio> <cstring> <ctime>
- 使用系统的头文件(非标C头文件): <pthread.h> <sys/types.h>

## 4.流操作
```cpp
cout << "Hello World.";
cin >> a;
// 也可以使用 scanf 和 printf
```
## 5.使用std空间下的相关数据
所有标准类型、对象和函数都位于`std`命名空间中
```cpp
std::cout       /* 标准输出对象 */
std::endl       /* 标准结束行的对象 */
std::cin        /* 标准的输入对象 */
std::cout << "hello world" << std::endl;
```
前面加空间名，是最根本的使用方式

## 6.使用声明的方式
使用声明的方式，能简化一个对象的使用
```cpp
using std::cout;
using std::endl;
cout << "hello world" << endl;
```
## 7.使用指令的方式
使用指令的方式，能简化多个对象的使用
```cpp
using namespace std;
cout<<"hello world"<<endl;
```