> 总结 Python 中关于模块与包的一些相关概念和如何使用的一些经验。

## Python模块

**1、什么是模块**

在 Python 中，一个 PY 文件就是一个模块，如：foo.py，模块 foo。模块中一般会定义一些函数、变量等。

**2、模块的作用**

如果把所有的代码都放到一个文件中，会导致代码文件很大，很不容易维护。为了编写可维护的代码，我们可以将相似功能的函数和变量分组，分别写到不同的文件中，有如下好处：

* 极大的提高代码的可维护性。
* 提高代码的重用性，不必所有代码重新开始写，引用现有的模块即可
* 还可以避免函数名和变量名的冲突。

**3、使用模块**

定义了模块后，在其他文件中需要该功能时，通过 `import` 导入这个模块，就可以重用这些函数和变量，不必重新造轮子。

```python
import foo  # foo.func_name() foo.var_name
from foo import func_name  # func_name()
from foo import var_name   # var_name
```
> 注：永远不要使用 `from module import *`，因为这样可能导入了不确定的符号，有可能意外的覆盖了自己定义的东西，发生不可预知的错误。

**4、查看模块的符号表**

可以通过内置函数 `dir()` 查看某个模块中定义了哪些函数、变量等符号。
```python
dir(foo)
['__name__', '__package__', '...', 'bar', 'foo']
# 不加参数 dir() 会列出当前模块中的符号表，包括内置的。
```

**5、搜索路径**

当执行 `import foo` 语句时，Python 解释器会首先搜索名称为 foo 的内置模块。如果找不到，将在变量 `sys.path` 给出的目录列表中搜索名为 foo.py 的文件，`sys.path` 包含下列目录位置：

* 包含输入脚本的所在目录
* `PYTHONPATH` 环境变量所指定的目录，与 shell 变量 PATH 语法相同
* 与安装相关的默认目录路径
```python
print(sys.path)
[
    '', '/usr/local/python352/lib/python35.zip', 
    '/usr/local/python352/lib/python3.5', 
    '/usr/local/python352/lib/python3.5/plat-linux', 
    '/usr/local/python352/lib/python3.5/lib-dynload', 
    '/usr/local/python352/lib/python3.5/site-packages'
]
```

**6、添加模块搜索路径**

实际项目中有时需要添加模块的搜索路径，可以使用如下几种方式：

* **动态增加路径。**临时生效，对于不经常使用的模块，这通常是最好的方式，因为不必用所有次要的模块的路径来污染 `PYTHONPATH`。
  ```python
  sys.path.append('/home/lee/workspace')
  ```
* **修改 `PYTHONPATH` 变量。**永久生效，对于在许多程序中都使用的模块，可以采用这种方式。这将改变所有 Python 应用的搜索路径，因为启动 Python 时，它会读取这个变量，甚至不同版本的 Python 都会受影响。
  ```bash
  $ vim ~/.bashrc
  export PYTHONPATH=$PYTHONPATH:/home/lee/workspace
  $ source ~/.bashrc
  ```
* **增加 `.pth` 文件。**永久生效，这是最简单的、也是推荐的方式。Python 在遍历已知的库文件目录过程中，如果遇到 `.pth` 文件，便会将其中的路径加入到 `sys.path` 中，于是 `.pth` 中所指定的路径就可以被 Python 运行环境找到了。
  ```bash
  $ vim /usr/local/lib/python3.5/site-packages/lvs.pth
  /home/lee/workspace
  # 在上述目录下添加一个扩展名为 .pth 文件
  ```

**7、import时执行语句**

* 模块可以包含一些可执行语句，这些语句通常用来进行模块的初始化工作。这些语句只在模块第一次被导入时被执行。
* 模块在被导入执行时，Python 解释器为加快程序的启动速度，会在与模块文件同一目录下生成 `.pyc` 文件，而 `.pyc` 是经过编译后的字节码，这一工作会自动完成。


