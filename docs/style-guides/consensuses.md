# Consensuses


Something does not in the official style guides but has been a consensus in the technology field.

一些虽然没写在规范里，但是长久以来被业界广泛接受的有效方法。


## 作用域

> 警惕内存泄漏！

1. 尽可能少的使用全局变量；
2. 小心循环引用（GC 友好）；
3. 不要相信任何外部输入（eval is evil）。


## 编码

> 小心，这是个深坑！

1. Python2.x 的编码是随操作系统的，Python3.x 的编码是固定的；
2. 使用 Python2.x 时最好手动固定编码；
   1. Python2.x 的官方解决办法是 `from __future__ import unicode_literals`;
3. 在程序和头脑里清楚的划分系统的边界以及数据的流动
   1. 任何进入的数据（bytes）都要经过 decode；
   2. 任何流出的数据（unicode）都要经过 encode；
4. 一般使用 utf8/unicode。

Demo:

```py
#!/usr/bin/env python
# -*- coding: utf-8 -*-

# 固定程序内部的数据编码为 unicode
from __future__ import unicode_literals

# 打开文件时要指定解码器
import codecs


# 相当于 open('file', 'rb').read().decode('utf8)
unicode_data = codecs.open('file', 'r', 'utf8').read()

# 相当于 open('file', 'wb').write(unicode_data.encode('utf8'))
codecs.open('file', 'w', 'utf8').write(unicode_data)
```

一个稍微复杂点的例子：

```py
unicode_data = '123'
utf8_bytes = unicode_data.encode('utf8')
gbk_bytes = unicode_data.encode('gbk')

print(type(unicode_data))
print(type(utf8_bytes))
print(type(gbk_bytes))

# <class 'str'>
# <class 'bytes'>
# <class 'bytes'>

import tempfile

with tempfile.TemporaryFile(mode='wb+') as utf_fp, \
        tempfile.TemporaryFile(mode='wb+') as gbk_fp:

    utf_fp.write(utf8_bytes)
    gbk_fp.write(gbk_bytes)

    utf_fp.seek(0, 0)
    gbk_fp.seek(0, 0)


    utf8_bytes_2 = utf_fp.read()
    gbk_bytes_2 = gbk_fp.read()

    assert utf8_bytes_2 == utf8_bytes
    assert gbk_bytes_2 == gbk_bytes

    assert utf8_bytes_2.decode('utf8') == gbk_bytes_2.decode('gbk')

```

*（注意 Python 2 和 3 中对 string 的定义是不同的，2 的 string 相当于 3 的 bytes。）*

*（我们这种纯英文的使用场景，其实绕过了编码的问题。）*


## 时区

`datetime.datetime` 分为 naive 和 aware，可以简单的理解为包含时区和不包含时区。

建议所有的 datetime 都包含正确的时区，如果不愿意处理时区，那就请统一使用 UTC，而仅在客户端呈现时根据用户所在时区进行转换显示。


## 函数

> Do not Repeat Yourself!

1. 每一个函数最好只有单一功能；
2. 每一个函数的长度最好不要超过 50 行；
3. 函数最好是无状态的（幂等性）；
4. 函数最好有简短的注释描述其功能、参数、返回值和异常；

Demo:

```py
def simple_func(arg1, arg2):
    """
    This is a simple function demo.

    Args:
        arg1: xxxxx
        arg2: xxxx

    Return:
        None

    Exceptions:
        raise IOError as pleasure
    """

    return
```

*（在 Python3.4 更进一步引入了 Type Hint，以实现更严格的接口规范。）*


## 类

> Keep It Simple and Stupid!

1. 类应该是某一种对象的抽象或封装（而不是用来收集函数的！）；
2. 控制类的属性，一般认为不应超过 6 个；
3. 复杂类应该善用类继承和 mixin；
4. 尽量不要用 metaclass、new 等方法，除非你明白你在干什么；
5. 类应该有类注释和方法注释；

*（善用 Design Pattern for Object Oriented Programming。）*

Demo:

```py
class DemoClass(object):
    """
    Class Description
    """

    def __init__(self):
        """
        Method Description with the format like function Description
        """
        pass
```


## 项目文件夹

1. 相似功能的类或函数放到一个子目录里（比如 MVC 模型）；
2. 被广泛依赖的工具方法应该单独放到一个 utils 或 libs 文件夹中。

常见的目录结构一般为：

```
.
├── setup.py
├── README.md
├── CHANGELOG
└── project_name/
    ├── libs/
    ├── settings/
    ├── utils/
    ├── models/
    ├── controllors/
    ├── __init__.py
    ├── __main__.py
    └── app.py

```

更好的文件结构有助于更好的管理代码。

在代码中使用 `import`  时使用绝对路径：

```python
# 假如项目名是 xxx
from xxx.yyy import zzz

# 对于子包，引用旁边的包时，可以使用
# 但是，仅针对于子包，且仅针对于子模块的方法，而不是通用方法
from . import zzz


```


## 版本管理

我们一般依赖以下两个标准定义版本号：

* [Semantic Versioning 2.0.0](http://semver.org/#semantic-versioning-200);
* [PEP 440 -- Version Identification and Dependency Specification](https://www.python.org/dev/peps/pep-0440/)；


你可以简单粗暴的这么记：`<major>.<features>.<bugfixes>`。

版本号一般放在根路径的 `__init__.py` 里，以 `__version__` 变量的形式定义。

*（OpenStack 定义了另外一套 pbr 的自动化版本管理机制。）*


## 包依赖

1. pip7 以后支持 git 协议，所以已经没什么理由不使用 package 了；
2. 打包可以实现很多东西，如生成可执行文件、cffi 依赖，etc；
3. 打包时要有良好的版本号机制；


建议所有的 python 项目都打包为一个 package，其他项目需要依赖时，可以直接通过 pip 进行安装。

一个简单的设置 package 的例子：<https://github.com/Laisky/kipp/blob/master/setup.py>

对于内部 VCS（如 Git） 上的项目，可以直接在 `requirements.txt` 里这么写 `-e git+https://<username>:<password>@<internal git url>/<group name>/<project name>.git@<branch name>#egg=<package name>`。

最好不要简单粗暴的用 `pip freeze` 来生成 `requirements`。



