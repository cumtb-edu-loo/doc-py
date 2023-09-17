# `pkgutil` \--- 包扩展工具¶

**源代码:** [Lib/pkgutil.py](https://github.com/python/cpython/tree/3.12/Lib/pkgutil.py)

* * *

该模块为导入系统提供了工具，尤其是在包支持方面。

_class _pkgutil.ModuleInfo( _module_finder_ , _name_ , _ispkg_ )¶

    

~~~
一个包含模块信息的简短摘要的命名元组。

在 3.6 版本加入.

pkgutil.extend_path( _path_ , _name_ )¶
~~~
    

~~~
扩展组成包的模块的搜索路径。 预期用途是将以下代码放到包的 `__init__.py` 中:
~~~
    
    
~~~
from pkgutil import extend_path
__path__ = extend_path(__path__, __name__)
~~~

对于 [`sys.path`](3.标准库/sys.md#sys.path "sys.path") 中每个具有与该包名称相匹配的子目录的目录，将该子目录添加到包的 [`__path__`](5.%20导入系统.md#path__ "__path__")。 这在需要将单个逻辑包的不同部分拆分为多个目录的情况下很有用处。

它还会查找开头部分 `*` 与 _name_ 参数相匹配的 `*.pkg` 文件。 此特性与 `*.pth` 文件类似（请参阅 [`site`](site.md#module-site "site: Module responsible for site-specific configuration.") 模块了解更多信息），区别在于它不会对以 `import` 开头的行做特别对待。 将按外在值对 `*.pkg` 文件添加信任：除了检查重复项，，所有在 `*.pkg` 文件中找到的条目都会被添加到路径中，不管它们是否存在于文件系统中。 （这是特性而非缺陷。）

如果输入路径不是一个列表（已冻结包就是这种情况）则它将被原样返回。 输入路径不会被修改；将返回一个扩展的副本。 条目将被添加到副本的末尾。

[`sys.path`](3.标准库/sys.md#sys.path "sys.path") 会被假定为一个序列。 [`sys.path`](3.标准库/sys.md#sys.path "sys.path") 中的条目如果不是指向现有目录的字符串则会被忽略。 [`sys.path`](3.标准库/sys.md#sys.path "sys.path") 上当用作文件名时会导致错误的 Unicode 条目可以会使得此函数引发异常（与 [`os.path.isdir()`](os.path.md#os.path.isdir "os.path.isdir") 的行为一致）。

pkgutil.find_loader( _fullname_ )¶

    

~~~
为给定的 _fullname_ 获取一个模块 [loader](../glossary.md#term-loader)。

这是针对 [`importlib.util.find_spec()`](importlib.md#importlib.util.find_spec "importlib.util.find_spec") 的向下兼容包装器，它将大多数失败转换为 [`ImportError`](exceptions.md#ImportError "ImportError") 并且只返回加载器而不是完整的 [`importlib.machinery.ModuleSpec`](importlib.md#importlib.machinery.ModuleSpec "importlib.machinery.ModuleSpec")。

在 3.3 版本发生变更: 更新为直接基于 [`importlib`](importlib.md#module-importlib "importlib: The implementation of the import machinery.") 而不是依赖于包内部的 [**PEP 302**](https://peps.python.org/pep-0302/) 导入模拟。

在 3.4 版本发生变更: 更新为基于 [**PEP 451**](https://peps.python.org/pep-0451/)

从 3.12 版起不建议使用，将在 3.14 版中移除: 使用 [`importlib.util.find_spec()`](importlib.md#importlib.util.find_spec "importlib.util.find_spec") 来代替。

pkgutil.get_importer( _path_item_ )¶
~~~
    

~~~
为给定的 _path_item_ 获取一个 [finder](../glossary.md#term-finder)。

返回的查找器如果是由一个路径钩子新建的则会被缓存至 [`sys.path_importer_cache`](sys.md#sys.path_importer_cache "sys.path_importer_cache")。

如果需要重新扫描 [`sys.path_hooks`](sys.md#sys.path_hooks "sys.path_hooks") 则缓存（或其一部分）可以被手动清空。

在 3.3 版本发生变更: 更新为直接基于 [`importlib`](importlib.md#module-importlib "importlib: The implementation of the import machinery.") 而不是依赖于包内部的 [**PEP 302**](https://peps.python.org/pep-0302/) 导入模拟。

pkgutil.get_loader( _module_or_name_ )¶
~~~
    

~~~
为 _module_or_name_ 获取一个 [loader](../glossary.md#term-loader)。

如果模块或包可通过正常导入机制来访问，则会返回该机制相关部分的包装器。 如果模块无法找到或导入则返回 `None`。 如果指定的模块尚未被导入，则包含它的包（如果存在）会被导入，以便建立包 `__path__`。

在 3.3 版本发生变更: 更新为直接基于 [`importlib`](importlib.md#module-importlib "importlib: The implementation of the import machinery.") 而不是依赖于包内部的 [**PEP 302**](https://peps.python.org/pep-0302/) 导入模拟。

在 3.4 版本发生变更: 更新为基于 [**PEP 451**](https://peps.python.org/pep-0451/)

从 3.12 版起不建议使用，将在 3.14 版中移除: 使用 [`importlib.util.find_spec()`](importlib.md#importlib.util.find_spec "importlib.util.find_spec") 来代替。

pkgutil.iter_importers( _fullname =''_)¶
~~~
    

~~~
为给定的模块名称产生 [finder](../glossary.md#term-finder) 对象。

如果完整名称包含一个 `'.'`，查找器将针对包含该完整名称的包，否则它们将被注册为最高层级查找器（即同时用于 [`sys.meta_path`](sys.md#sys.meta_path "sys.meta_path") 和 [`sys.path_hooks`](sys.md#sys.path_hooks "sys.path_hooks"))。

如果指定的模块位于一个包内，则该包会作为发起调用此函数的附带影响被导入。

如果未指定模块名称，则会产生所有的最高层级查找器。

在 3.3 版本发生变更: 更新为直接基于 [`importlib`](importlib.md#module-importlib "importlib: The implementation of the import machinery.") 而不是依赖于包内部的 [**PEP 302**](https://peps.python.org/pep-0302/) 导入模拟。

pkgutil.iter_modules( _path =None_, _prefix =''_)¶
~~~
    

~~~
为 _path_ 上的所有子模块产生 `ModuleInfo`，或者如果 _path_ 为 `None`，则为 [`sys.path`](sys.md#sys.path "sys.path") 上的所有最高层级模块产生。

_path_ 应当为 `None` 或一个作为查找模块目标的路径的列表。

_prefix_ 是要在输出时输出到每个模块名称之前的字符串。

备注

只适用于定义了 `iter_modules()` 方法的 [finder](../glossary.md#term-finder)。 该接口是非标准的，因此本模块还提供了针对 [`importlib.machinery.FileFinder`](importlib.md#importlib.machinery.FileFinder "importlib.machinery.FileFinder") 和 [`zipimport.zipimporter`](zipimport.md#zipimport.zipimporter "zipimport.zipimporter") 的实现。

在 3.3 版本发生变更: 更新为直接基于 [`importlib`](importlib.md#module-importlib "importlib: The implementation of the import machinery.") 而不是依赖于包内部的 [**PEP 302**](https://peps.python.org/pep-0302/) 导入模拟。

pkgutil.walk_packages( _path =None_, _prefix =''_, _onerror =None_)¶
~~~
    

~~~
在 _path_ 上递归地为所有模块产生 `ModuleInfo`，或者如果 _path_ 为 `None`，则为所有可访问的模块产生。

_path_ 应当为 `None` 或一个作为查找模块目标的路径的列表。

_prefix_ 是要在输出时输出到每个模块名称之前的字符串。

请注意此函数必须导入给定 _path_ 上所有的 _packages_ ( _而不是_ 所有的模块！)，以便能访问 `__path__` 属性来查找子模块。

_onerror_ 是在当试图导入包如果发生任何异常则将附带一个参数（被导入的包的名称）被调用的函数。 如果没有提供 _onerror_ 函数，则 [`ImportError`](exceptions.md#ImportError "ImportError") 会被捕获并被忽略，而其他异常则会被传播，导致模块搜索的终结。

示例:
~~~
    
    
~~~
# list all modules python can access
walk_packages()

# list all submodules of ctypes
walk_packages(ctypes.__path__, ctypes.__name__ + '.')
~~~

备注

只适用于定义了 `iter_modules()` 方法的 [finder](../glossary.md#term-finder)。 该接口是非标准的，因此本模块还提供了针对 [`importlib.machinery.FileFinder`](importlib.md#importlib.machinery.FileFinder "importlib.machinery.FileFinder") 和 [`zipimport.zipimporter`](zipimport.md#zipimport.zipimporter "zipimport.zipimporter") 的实现。

在 3.3 版本发生变更: 更新为直接基于 [`importlib`](importlib.md#module-importlib "importlib: The implementation of the import machinery.") 而不是依赖于包内部的 [**PEP 302**](https://peps.python.org/pep-0302/) 导入模拟。

pkgutil.get_data( _package_ , _resource_ )¶

    

~~~
从包中获取一个资源。

这是一个针对 [loader](../glossary.md#term-loader) [`get_data`](importlib.md#importlib.abc.ResourceLoader.get_data "importlib.abc.ResourceLoader.get_data") API 的包装器。 _package_ 参数应为一个标准模块格式 (`foo.bar`) 的包名称。 _resource_ 参数应为相对路径文件名的形式，使用 `/` 作为路径分隔符。 父目录名 `..`，以及根目录名 (以 `/` 打头) 均不允许使用。

返回指定资源内容的二进制串。

对于位于文件系统中，已经被导入的包来说，这大致等价于:
~~~
    
    
~~~
d = os.path.dirname(sys.modules[package].__file__)
data = open(os.path.join(d, resource), 'rb').read()
~~~

如果指定的包无法被定位或加载，或者如果它使用了不支持 [`get_data`](importlib.md#importlib.abc.ResourceLoader.get_data "importlib.abc.ResourceLoader.get_data") 的 [loader](../glossary.md#term-loader)，则将返回 `None`。 特别地，针对 [命名空间包](../glossary.md#term-namespace-package) 的 [loader](../glossary.md#term-loader) 不支持 [`get_data`](importlib.md#importlib.abc.ResourceLoader.get_data "importlib.abc.ResourceLoader.get_data")。

pkgutil.resolve_name( _name_ )¶

    

~~~
