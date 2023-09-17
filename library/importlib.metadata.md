# `importlib.metadata` \-- 访问软件包元数据¶

在 3.8 版本加入.

在 3.10 版本发生变更: `importlib.metadata` 不再是暂定的。

**源代码:** [Lib/importlib/metadata/__init__.py](https://github.com/python/cpython/tree/3.12/Lib/importlib/metadata/__init__.py)

`importlib.metadata` 是一个提供对已安装的 [分发包](https://packaging.python.org/en/latest/glossary/#term-Distribution-Package) 的元数据的访问的库，如其入口点或其最高层级名称 ([导入包](https://packaging.python.org/en/latest/glossary/#term-Import-Package), 模块等，如果存在的话）。 这个库部分构建于 Python 的导入系统之上，其目标是取代 `pkg_resources` 的中的 [entry point API](https://setuptools.readthedocs.io/en/latest/pkg_resources.md#entry-points) 和 [metadata API](https://setuptools.readthedocs.io/en/latest/pkg_resources.md#metadata-api)。 配合 [`importlib.resources`](importlib.resources.md#module-importlib.resources "importlib.resources: Package resource reading, opening, and access") ，这个包可以消除使用较老旧且低效的 `pkg_resources` 包的必要性。

`importlib.metadata` 对通过 [pip](https://pypi.org/project/pip/) 之类的工具安装到 Python 的 `site-packages` 目录的第三方 _分发包_ 进行操作。 具体来说，它适用于带有可发现 `dist-info` 或 `egg-info` 目录，以及由 [核心元数据规范说明](https://packaging.python.org/en/latest/specifications/core-metadata/#core-metadata) 所定义的元数据的分发包。

重要

这些 _并不_ 必须等同于或 1:1 对应于可在 Python 代码中导入的最高层级 _导入包_ 名称。 一个 _分发包_ 可以包含多个 _导入包_ (和单独模块)，而一个最高层级 _导入包_ 如果是命名空间包则可以映射到多个 _分发包_ 。 你可以使用 package_distributions() 来获取它们之间的映射关系。

在默认情况下，分发包元数据可以存在于 [`sys.path`](sys.md#sys.path "sys.path") 下的文件系统或 zip 归档文件中。 通过一个扩展机制，元数据可以存在于几乎任何地方。

参见

<https://importlib-metadata.readthedocs.io/>

    

~~~
`importlib_metadata` 的文档，它提供了对 `importlib.metadata` 的向下移植。 这包含该模块的类和函数的 [API 引用](https://importlib-metadata.readthedocs.io/en/latest/api.md)，以及针对 `pkg_resources` 现有用户的 [迁移指南](https://importlib-metadata.readthedocs.io/en/latest/migration.md)。

## 概述¶

让我们假设你想要获取你使用 `pip` 安装的某个 [分发包](https://packaging.python.org/en/latest/glossary/#term-Distribution-Package) 的版本字符串。 我们首先创建一个虚拟环境并在其中安装一些软件包:
~~~
    
    
~~~
$ python -m venv example
$ source example/bin/activate
(example) $ python -m pip install wheel
~~~

你可以通过运行以下代码得到 `wheel` 的版本字符串：

    
    
~~~
(example) $ python
>>> from importlib.metadata import version  
>>> version('wheel')  
'0.32.3'
~~~

你还能够获得可通过 EntryPoint 的特征属性 (通常为 'group' 或 'name') 来选择的入口点多项集，比如 `console_scripts`, `distutils.commands` 等等。 每个 group 包含一个由 EntryPoint 对象组成的多项集。

你可以获得 分发的元数据：

    
    
~~~shell
>>> list(metadata('wheel'))  
['Metadata-Version', 'Name', 'Version', 'Summary', 'Home-page', 'Author', 'Author-email', 'Maintainer', 'Maintainer-email', 'License', 'Project-URL', 'Project-URL', 'Project-URL', 'Keywords', 'Platform', 'Classifier', 'Classifier', 'Classifier', 'Classifier', 'Classifier', 'Classifier', 'Classifier', 'Classifier', 'Classifier', 'Classifier', 'Classifier', 'Classifier', 'Requires-Python', 'Provides-Extra', 'Requires-Dist', 'Requires-Dist']
~~~

你也可以获得 分发的版本号，列出它的 构成文件，并且得到分发的 分发的依赖 列表。

## 功能性 API¶

这个包通过其公共 API 提供了以下功能。

### 入口点¶

`entry_points()` 函数返回入口点的字典。入口点表现为 `EntryPoint` 的实例；每个 `EntryPoint` 对象都有 `.name` ，`.group` 与 `.value` 属性，用于解析值的 `.load()` 方法， `.module` ，`.attr` 与 `.extras` 属性是 `.value` 属性的对应部分。

查询所有的入口点：

    
    
~~~shell
>>> eps = entry_points()  
~~~

`entry_points()` 函数返回一个 `EntryPoints` 对象，即由带有 `names` 和 `groups` 属性的全部 `EntryPoint` 对象组成的多项集以方便使用:

    
    
~~~shell
>>> sorted(eps.groups)  
['console_scripts', 'distutils.commands', 'distutils.setup_keywords', 'egg_info.writers', 'setuptools.installation']
~~~

`EntryPoints` 的 `select` 方法用于选择匹配特性的入口点。要选择 `console_scripts` 组中的入口点：

    
    
~~~shell
>>> scripts = eps.select(group='console_scripts')  
~~~

你也可以向 `entry_points` 传递关键字参数 "group" 以实现相同的效果:

    
    
~~~shell
>>> scripts = entry_points(group='console_scripts')  
~~~

选出命名为 “wheel” 的特定脚本（可以在 wheel 项目中找到）：

    
    
~~~shell
>>> 'wheel' in scripts.names  
True
>>> wheel = scripts['wheel']  
~~~

等价地，在选择过程中查询对应的入口点：

    
    
~~~shell
>>> (wheel,) = entry_points(group='console_scripts', name='wheel')  
>>> (wheel,) = entry_points().select(group='console_scripts', name='wheel')  
~~~

检查解析得到的入口点：

    
    
~~~shell
>>> wheel  
EntryPoint(name='wheel', value='wheel.cli:main', group='console_scripts')
>>> wheel.module  
'wheel.cli'
>>> wheel.attr  
'main'
>>> wheel.extras  
[]
>>> main = wheel.load()  
>>> main  
<function main at 0x103528488>
~~~

`group` 和 `name` 是由包作者定义的任意值并且通常来说客户端会想要解析特定 group 的所有入口点。 请参阅 [the setuptools docs](https://setuptools.pypa.io/en/latest/userguide/entry_point.md) 了解有关入口点，其定义和用法的更多信息。

_兼容性说明_

"selectable" 入口点是在 `importlib_metadata` 3.6 和 Python 3.10 中引入的。 在这项改变之前，`entry_points` 不接受任何形参并且总是返回一个由入口点组成的字典，字典的键为分组名。 在 `importlib_metadata` 5.0 和 Python 3.12 中，`entry_points` 总是返回一个 `EntryPoints` 对象。 请参阅 [backports.entry_points_selectable](https://pypi.org/project/backports.entry-points-selectable) 了解相关兼容性选项。

### 分发的元数据¶

每个 [分发包](https://packaging.python.org/en/latest/glossary/#term-Distribution-Package) 都包括一些元数据，你可以使用 `metadata()` 函数来获取:

    
    
~~~shell
>>> wheel_metadata = metadata('wheel')  
~~~

返回的数据架构 `PackageMetadata` 的键代表元数据的关键字，而值从分发的元数据中不被解析地返回：

    
    
~~~shell
>>> wheel_metadata['Requires-Python']  
'>=2.7, !=3.0.*, !=3.1.*, !=3.2.*, !=3.3.*'
~~~

`PackageMetadata` 也提供了按照 [**PEP 566**](https://peps.python.org/pep-0566/) 将所有元数据以 JSON 兼容的方式返回的 `json` 属性：

    
    
~~~shell
>>> wheel_metadata.json['requires_python']
'>=2.7, !=3.0.*, !=3.1.*, !=3.2.*, !=3.3.*'
~~~

备注

`metadata()` 所返回的对象的实际类型是一个实现细节并且应当只能通过 [PackageMetadata 协议](https://importlib-metadata.readthedocs.io/en/latest/api.md#importlib_metadata.PackageMetadata) 所描述的接口来访问。

在 3.10 版本发生变更: 当有效载荷中包含时，`Description` 以去除续行符的形式被包含于元数据中。

在 3.10 版本加入: 添加了 `json` 属性。

### 分发的版本¶

`version()` 函数是获取一个 [分发包](https://packaging.python.org/en/latest/glossary/#term-Distribution-Package) 的版本号的最快捷方式，其形式为一个字符串:

    
    
~~~shell
>>> version('wheel')  
'0.32.3'
~~~

### 分发的文件¶

你还可以获取包含在分发包内的全部文件的集合。 `files()` 函数接受一个 [分发包](https://packaging.python.org/en/latest/glossary/#term-Distribution-Package) 名称并返回此分发包所安装的全部文件。 每个返回的文件对象都是一个 `PackagePath`，即带有由元数据指明的额外 `dist`, `size` 和 `hash` 特征属性的派生自 [`pathlib.PurePath`](pathlib.md#pathlib.PurePath "pathlib.PurePath") 的对象。 例如:

    
    
~~~shell
>>> util = [p for p in files('wheel') if 'util.py' in str(p)][0]  
>>> util  
PackagePath('wheel/util.py')
>>> util.size  
859
>>> util.dist  
<importlib.metadata._hooks.PathDistribution object at 0x101e0cef0>
>>> util.hash  
<FileHash mode: sha256 value: bYkw5oMccfazVCoYQwKkkemoVyMAFoR34mmKBx8R1NI>
~~~

当你获得了文件对象，你可以读取其内容：

    
    
~~~shell
>>> print(util.read_text())  
import base64
import sys
...
def as_bytes(s):
    if isinstance(s, text_type):
        return s.encode('utf-8')
    return s
~~~

你也可以使用 `locate` 方法来获得文件的绝对路径：

    
    
~~~shell
>>> util.locate()  
PosixPath('/home/gustav/example/lib/site-packages/wheel/util.py')
~~~

当列出包含文件的元数据文件（RECORD 或 SOURCES.txt）不存在时， `files()` 函数将返回 `None` 。调用者可能会想要将对 `files()` 的调用封装在 [always_iterable](https://more-itertools.readthedocs.io/en/stable/api.md#more_itertools.always_iterable) 中，或者用其他方法来应对目标分发元数据存在性未知的情况。

### 分发的依赖¶

要获取一个 [分发包](https://packaging.python.org/en/latest/glossary/#term-Distribution-Package) 的完整需求集合，请使用 `requires()` 函数:

    
    
~~~shell
>>> requires('wheel')  
["pytest (>=3.0.0) ; extra == 'test'", "pytest-cov ; extra == 'test'"]
~~~

### 将导入映射到分发包¶

用于提供解析每个可导入的最高层级模块或 [导入包](https://packaging.python.org/en/latest/glossary/#term-Import-Package) 的A convenience method to resolve the [分发包](https://packaging.python.org/en/latest/glossary/#term-Distribution-Package) 名称（或者对于命名空间包来说是名称集合）的快捷方法:

    
    
~~~shell
>>> packages_distributions()
{'importlib_metadata': ['importlib-metadata'], 'yaml': ['PyYAML'], 'jaraco': ['jaraco.classes', 'jaraco.functools'], ...}
~~~

某些可编辑的安装 [没有提供最高层级名称](https://github.com/pypa/packaging-problems/issues/609)，因而此函数不适用于这样的安装。

在 3.10 版本加入.

## 分发¶

以上 API 是最常见且便捷的用法，但你也可以通过 `Distribution` 类来获得所有信息。 `Distribution` 是一个代表 Python [分发包](https://packaging.python.org/en/latest/glossary/#term-Distribution-Package) 元数据的抽象对象。 你可以这样获取 `Distribution` 实例:

    
    
~~~shell
>>> from importlib.metadata import distribution  
>>> dist = distribution('wheel')  
~~~

因此，可以通过 `Distribution` 实例获得版本号：

    
    
~~~shell
>>> dist.version  
'0.32.3'
~~~

`Distribution` 实例具有所有可用的附加元数据：

    
    
~~~shell
>>> dist.metadata['Requires-Python']  
'>=2.7, !=3.0.*, !=3.1.*, !=3.2.*, !=3.3.*'
>>> dist.metadata['License']  
'MIT'
~~~

可用元数据的完整集合并未在此描述。 请参阅 [核心元数据规格说明](https://packaging.python.org/en/latest/specifications/core-metadata/#core-metadata) 了解更多细节。

## 分发包的发现¶

在默认情况下，这个包针对文件系统和 zip 文件 [分发包](https://packaging.python.org/en/latest/glossary/#term-Distribution-Package) 的元数据发现提供了内置支持。 这个元数据查找器的搜索目标默认为 `sys.path`，但它对来自其他导入机制行为方式的解读会略有变化。 特别地:

  * `importlib.metadata` 不会识别 `sys.path` 上的 [`bytes`](stdtypes.md#bytes "bytes") 对象。

  * `importlib.metadata` 将顺带识别 `sys.path` 上的 [`pathlib.Path`](pathlib.md#pathlib.Path "pathlib.Path") 对象，即使这些值会被导入操作所忽略。

## 扩展搜索算法¶

因为 [分发包](https://packaging.python.org/en/latest/glossary/#term-Distribution-Package) 元数据不能通过 [`sys.path`](sys.md#sys.path "sys.path") 搜索，或是通过包加载器直接获得，一个分发包的元数据是通过导入系统的 [查找器](../reference/import.md#finders-and-loaders) 找到的。 要找到分发包的元数据，`importlib.metadata` 将在 [`sys.meta_path`](sys.md#sys.meta_path "sys.meta_path") 上查询 [元路径查找器](../glossary.md#term-meta-path-finder) 的列表。

在默认情况下 `importlib.metadata` 会安装在文件系统中找到的分发包的查找器。 这个查找器无法真正找出任何 _分发包_ ，但它能找到它们的元数据。

抽象基类 [`importlib.abc.MetaPathFinder`](importlib.md#importlib.abc.MetaPathFinder "importlib.abc.MetaPathFinder") 定义了 Python 导入系统期望的查找器接口。 `importlib.metadata` 通过寻找 [`sys.meta_path`](sys.md#sys.meta_path "sys.meta_path") 上查找器可选的 `find_distributions` 可调用的属性扩展这个协议，并将这个扩展接口作为 `DistributionFinder` 抽象基类提供，它定义了这个抽象方法：

    
    
~~~
@abc.abstractmethod
def find_distributions(context=DistributionFinder.Context()):
    """Return an iterable of all Distribution instances capable of
    loading the metadata for packages for the indicated ``context``.
    """
~~~

`DistributionFinder.Context` 对象提供了指示搜索路径和匹配名称的属性 `.path` 和 `.name` ，也可能提供其他相关的上下文。

这在实践中意味着要支持在文件系统外的其他位置查找分发包的元数据，你需要子类化 `Distribution` 并实现抽象方法，之后从一个自定义查找器的 `find_distributions()` 方法返回这个派生的 `Distribution` 实例。

