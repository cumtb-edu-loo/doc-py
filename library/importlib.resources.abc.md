# `importlib.resources.abc` \-- 针对资源的抽象基类¶

**源代码:** [Lib/importlib/resources/abc.py](https://github.com/python/cpython/tree/3.12/Lib/importlib/resources/abc.py)

* * *

在 3.11 版本加入.

_class _importlib.resources.abc.ResourceReader¶

    

~~~
_被 TraversableResources_ 取代

提供读取 _resources_ 能力的一个 [abstract base class](../glossary.md#term-abstract-base-class) 。

从这个 ABC 的视角出发， _resource_ 指一个包附带的二进制文件。常见的如在包的 `__init__.py` 文件旁的数据文件。这个类存在的目的是为了将对数据文件的访问进行抽象，这样包就和其数据文件的存储方式无关了。不论这些文件是存放在一个 zip 文件里还是直接在文件系统内。

对于该类中的任一方法， _resource_ 参数的值都需要是一个在概念上表示文件名称的 [path-like object](../glossary.md#term-path-like-object)。 这意味着任何子目录的路径都不该出现在 _resouce_ 参数值内。 因为对于阅读器而言，包的位置就代表着「目录」。 因此目录和文件名就分别对应于包和资源。 这也是该类的实例都需要和一个包直接关联（而不是潜在指代很多包或者一整个模块）的原因。

想支持资源读取的加载器需要提供一个返回实现了此 ABC 的接口的 `get_resource_reader(fullname)` 方法。如果通过全名指定的模块不是一个包，这个方法应该返回 [`None`](constants.md#None "None")。 当指定的模块是一个包时，应该只返回一个与这个抽象类ABC兼容的对象。

从 3.12 版起不建议使用，将在 3.14 版中移除: 使用 `importlib.resources.abc.TraversableResources` 代替。

_abstractmethod _open_resource( _resource_ )¶
~~~
    

~~~
返回一个打开的 [file-like object](../glossary.md#term-file-like-object) 用于 _resource_ 的二进制读取。

如果无法找到资源，将会引发 [`FileNotFoundError`](exceptions.md#FileNotFoundError "FileNotFoundError")。

_abstractmethod _resource_path( _resource_ )¶
~~~
    

~~~
返回 _resource_ 的文件系统路径。

如果资源并不实际存在于文件系统中，将会引发 [`FileNotFoundError`](exceptions.md#FileNotFoundError "FileNotFoundError")。

_abstractmethod _is_resource( _name_ )¶
~~~
    

~~~
如果 _name_ 被视作资源，则返回True。如果 _name_ 不存在，则引发 [`FileNotFoundError`](exceptions.md#FileNotFoundError "FileNotFoundError") 异常。

_abstractmethod _contents()¶
~~~
    

~~~
反回由字符串组成的 [iterable](../glossary.md#term-iterable)，表示这个包的所有内容。 请注意并不要求迭代器返回的所有名称都是实际的资源，例如返回 `is_resource()` 为假值的名称也是可接受的。

允许非资源名字被返回是为了允许存储的一个包和它的资源的方式是已知先验的并且非资源名字会有用的情况。比如，允许返回子目录名字，目的是当得知包和资源存储在文件系统上面的时候，能够直接使用子目录的名字。

这个抽象方法返回了一个不包含任何内容的可迭代对象。

_class _importlib.resources.abc.Traversable¶
~~~
    

~~~
一个具有 [`pathlib.Path`](pathlib.md#pathlib.Path "pathlib.Path") 中方法的子集并适用于遍历目录和打开文件的对象。

对于该对象在文件系统中的表示形式，请使用 [`importlib.resources.as_file()`](importlib.resources.md#importlib.resources.as_file "importlib.resources.as_file")。

name¶
~~~
    

~~~
抽象属性。 此对象的不带任何父引用的基本名称。

_abstractmethod _iterdir()¶
~~~
    

~~~
产出自身内部的可遍历对象。

_abstractmethod _is_dir()¶
~~~
    

~~~
如果自身是一个目录则返回 True。

_abstractmethod _is_file()¶
~~~
    

~~~
如果自身是一个文件则返回 True。

_abstractmethod _joinpath( _child_ )¶
~~~
    

~~~
返回自身中可遍历的子对象。

_abstractmethod ___truediv__( _child_ )¶
~~~
    

~~~
返回自身中可遍历的子对象。

_abstractmethod _open( _mode ='r'_, _* args_, _** kwargs_)¶
~~~
    

~~~
_mode_ 可以为 'r' 或 'rb' 即以文本或二进制模式打开。 返回一个适用于读取的句柄（与 [`pathlib.Path.open`](pathlib.md#pathlib.Path.open "pathlib.Path.open") 样同）。

当以文件模式打开时，接受与 [`io.TextIOWrapper`](io.md#io.TextIOWrapper "io.TextIOWrapper") 所接受的相同编码格式形参。

read_bytes()¶
~~~
    

~~~
以字节串形式读取自身的内容。

read_text( _encoding =None_)¶
~~~
    

~~~
以文本形式读取自身的内容。

_class _importlib.resources.abc.TraversableResources¶
~~~
    

~~~
针对能够为 [`importlib.resources.files()`](importlib.resources.md#importlib.resources.files "importlib.resources.files") 接口提供服务的资源读取器的抽象基类。 子类化 `ResourceReader` 并为 `ResourceReader` 的抽象方法提供具体实现。 因此 ，任何提供了 `TraversableResources` 的加载器也会提供 `ResourceReader`。

需要支持资源读取的加载器应实现此接口。

_abstractmethod _files()¶
~~~
    

~~~
