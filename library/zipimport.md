# `zipimport` \--- 从 Zip 存档中导入模块¶

**源代码：** [Lib/zipimport.py](https://github.com/python/cpython/tree/3.12/Lib/zipimport.py)

* * *

此模块添加了从 ZIP 格式档案中导入 Python 模块（ `*.py` ， `*.pyc` ）和包的能力。通常不需要明确地使用 `zipimport` 模块，内置的 [`import`](../reference/simple_stmts.md#import) 机制会自动将此模块用于 ZIP 档案路径的 [`sys.path`](sys.md#sys.path "sys.path") 项目上。

通常， [`sys.path`](sys.md#sys.path "sys.path") 是字符串的目录名称列表。此模块同样允许 [`sys.path`](sys.md#sys.path "sys.path") 的一项成为命名 ZIP 文件档案的字符串。 ZIP 档案可以容纳子目录结构去支持包的导入，并且可以将归档文件中的路径指定为仅从子目录导入。比如说，路径 `example.zip/lib/` 将只会从档案中的 `lib/` 子目录导入。

任何文件都可以放到 ZIP 档案中，但只有 `.py` 和 `.pyc` 文件会触发导入器操作。 动态模块 (`.pyd`, `.so`) 的 ZIP 导入是不被允许的。 请注意如果一个档案只包含有 `.py` 文件，那么 Python 将不会尝试通过添加对应的 `.pyc` 文件来修改档案，这意味着如果一个 ZIP 档案不包含 `.pyc` 文件，则导入速度可能会相当慢。

在 3.8 版本发生变更: 以前，不支持带有档案注释的 ZIP 档案。

参见

[PKZIP Application Note](https://pkware.cachefly.net/webdocs/casestudies/APPNOTE.TXT)

    

~~~
Phil Katz 编写的 ZIP 文件格式文档，此格式和使用的算法的创建者。

[**PEP 273**](https://peps.python.org/pep-0273/) \- 从ZIP压缩包导入模块
~~~
    

~~~
由 James C. Ahlstrom 编写，他也提供了实现。 Python 2.3 遵循 [**PEP 273**](https://peps.python.org/pep-0273/) 的规范，但是使用 Just van Rossum 编写的使用了 [**PEP 302**](https://peps.python.org/pep-0302/) 中描述的导入钩的实现。

[`importlib`](importlib.md#module-importlib "importlib: The implementation of the import machinery.") \- 导入机制的实现
~~~
    

~~~
为所有导入器的实现提供相关协议的包。

此模块定义了一个异常：

_exception _zipimport.ZipImportError¶
~~~
    

~~~
异常由 zipimporter 对象引发。这是 [`ImportError`](exceptions.md#ImportError "ImportError") 的子类，因此，也可以捕获为 [`ImportError`](exceptions.md#ImportError "ImportError") 。

## zipimporter 对象¶

`zipimporter` 是用于导入 ZIP 文件的类。

_class _zipimport.zipimporter( _archivepath_ )¶
~~~
    

~~~
创建新的 zipimporter 实例。 _archivepath_ 必须是指向 ZIP 文件的路径，或者 ZIP 文件中的特定路径。例如， `foo/bar.zip/lib` 的 _archivepath_ 将在 ZIP 文件 `foo/bar.zip` 中的 `lib` 目录中查找模块（只要它存在）。

如果 _archivepath_ 没有指向一个有效的 ZIP 档案，引发 `ZipImportError` 。

在 3.12 版本发生变更: 在 3.10 中已弃用的 `find_loader()` 和 `find_module()` 方法现在已被移除。 请改用 `find_spec()`。

create_module( _spec_ )¶
~~~
    

~~~
返回 [`None`](constants.md#None "None") 来显式地请求默认语义的 [`importlib.abc.Loader.create_module()`](importlib.md#importlib.abc.Loader.create_module "importlib.abc.Loader.create_module") 实现。

在 3.10 版本加入.

exec_module( _module_ )¶
~~~
    

~~~
[`importlib.abc.Loader.exec_module()`](importlib.md#importlib.abc.Loader.exec_module "importlib.abc.Loader.exec_module") 的实现。

在 3.10 版本加入.

find_spec( _fullname_ , _target =None_)¶
~~~
    

~~~
[`importlib.abc.PathEntryFinder.find_spec()`](importlib.md#importlib.abc.PathEntryFinder.find_spec "importlib.abc.PathEntryFinder.find_spec") 的实现。

在 3.10 版本加入.

get_code( _fullname_ )¶
~~~
    

~~~
返回指定模块的代码对象。 如果模块无法被导入则引发 `ZipImportError`。

get_data( _pathname_ )¶
~~~
    

~~~
返回与 _pathname_ 相关联的数据。如果不能找到文件则引发 [`OSError`](exceptions.md#OSError "OSError") 错误。

在 3.3 版本发生变更: 曾经是 [`IOError`](exceptions.md#IOError "IOError") 被引发而不是 [`OSError`](exceptions.md#OSError "OSError") 。

get_filename( _fullname_ )¶
~~~
    

~~~
返回如果指定模块被导入则应当要设置的 `__file__` 值。 如果模块无法被导入则引发 `ZipImportError`。

在 3.1 版本加入.

get_source( _fullname_ )¶
~~~
    

~~~
返回指定模块的源代码。如果没有找到模块则引发 `ZipImportError` ，如果档案包含模块但是没有源代码，返回 [`None`](constants.md#None "None") 。

is_package( _fullname_ )¶
~~~
    

~~~
如果由 _fullname_ 指定的模块是一个包则返回 `True` 。如果不能找到模块则引发 `ZipImportError` 错误。

load_module( _fullname_ )¶
~~~
    

~~~
导入由 _fullname_ 所指定的模块。 _fullname_ 必须为（带点号的）完整限定名称。 成功时返回导入的模块，失败时引发 `ZipImportError`。

自 3.10 版本弃用: 使用 `exec_module()` 来代替。

invalidate_caches()¶
~~~
    

~~~
清除在 ZIP 归档文件中找到的相关文件信息的内部缓存。

在 3.10 版本加入.

archive¶
~~~
    

~~~
导入器关联的 ZIP 文件的文件名，没有可能的子路径。

prefix¶
~~~
    

~~~
ZIP 文件中搜索的模块的子路径。这是一个指向 ZIP 文件根目录的 zipimporter 对象的空字符串。

当与斜杠结合使用时， `archive` 和 `prefix` 属性等价于赋予 `zipimporter` 构造器的原始 _archivepath_ 参数。

## 例子¶

这是一个从 ZIP 档案中导入模块的例子 - 请注意 `zipimport` 模块不需要明确地使用。
~~~
    
    
~~~
$ unzip -l example.zip
Archive:  example.zip
  Length     Date   Time    Name
 --------    ----   ----    ----
     8467  11-26-02 22:30   jwzthreading.py
 --------                   -------
     8467                   1 file
$ ./python
Python 2.3 (#1, Aug 1 2003, 19:54:32)
>>> import sys
>>> sys.path.insert(0, 'example.zip')  # Add .zip file to front of path
>>> import jwzthreading
>>> jwzthreading.__file__
'example.zip/jwzthreading.py'
~~~

