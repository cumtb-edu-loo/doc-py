# `importlib.resources` \-- 包资源的读取、打开和访问¶

**源代码:** [Lib/importlib/resources/__init__.py](https://github.com/python/cpython/tree/3.12/Lib/importlib/resources/__init__.py)

* * *

在 3.7 版本加入.

此模块调整了 Python 的导入系统以便提供对 _包_ 内部的 _资源_ 的访问。

“资源”是指 Python 中与模块或包相关联的文件类资源。 资源可以直接包含在某个包中，包含在某个包的子目录中，或是与某个包外部的模块相邻。 资源可以是文本或二进制数据。 因此，从技术上说 Python 包的模块源代码文件 (.py) 和编译结果文件 (pycache) 就是包实际所包含的资源。 但是，在实践中，资源主要是指包作者专门公开的非 Python 文件。

资源可以使用二进制或文本模式打开。

资源大致相当于目录内的文件，不过需要记住这只是一个比喻。 资源和包 **不是** 必须如文件系统上的物理文件和目录那样存在的：例如，一个包及其资源可使用 [`zipimport`](zipimport.md#module-zipimport "zipimport: Support for importing Python modules from ZIP archives.") 从一个 ZIP 文件导入。

备注

本模块提供了类似于 [pkg_resources](https://setuptools.readthedocs.io/en/latest/pkg_resources.md) [Basic Resource Access](https://setuptools.readthedocs.io/en/latest/pkg_resources.md#basic-resource-access) 的功能而没有那样高的性能开销。 这使得读取包中的资源更为容易，并具有更为稳定和一致的语义。

此模块的独立向下移植版本在 [using importlib.resources](https://importlib-resources.readthedocs.io/en/latest/using.md) 和 [migrating from pkg_resources to importlib.resources](https://importlib-resources.readthedocs.io/en/latest/migration.md) 中提供了更多信息。

想要支持资源读取的 [`加载器`](importlib.md#importlib.abc.Loader "importlib.abc.Loader") 应当实现 [`importlib.resources.abc.ResourceReader`](importlib.resources.abc.md#importlib.resources.abc.ResourceReader "importlib.resources.abc.ResourceReader") 中规定的 `get_resource_reader(fullname)` 方法。

importlib.resources.Anchor¶

    

~~~
代表资源的锚点，可以是一个 [`模块对象`](types.md#types.ModuleType "types.ModuleType") 或字符串形式的模块名称不。 定义为 `Union[str, ModuleType]`。

importlib.resources.files( _anchor : Anchor | [None](constants.md#None "None") = None_)¶
~~~
    

~~~
返回一个代表资源容器（相当于目录）及其资源（相当于文件）的 [`Traversable`](importlib.resources.abc.md#importlib.resources.abc.Traversable "importlib.resources.abc.Traversable") 对象。 Traversable 可以包含其他容器（相当于子目录）。

_anchor_ 是一个可选的 `Anchor`。 如果 anchor 是一个包，则会从这个包获取资源。 如果是一个模块，则会从这个模块的相邻位置获取资源（在同一个包或包的根目录中）。 如果省略了 anchor，则会使用调用方的模块。

在 3.9 版本加入.

在 3.12 版本发生变更: "package" 形参被重命名为 "anchor"。 "anchor" 现在可以是一个不为包的模块，如果被省略则默认为调用方的模块。 为保持兼容性 "package" 仍然被接受但会引发 DeprecationWarning。 请考虑以位置参数方式传入 anchor 或使用 `importlib_resources >= 5.10` 作为针对旧版 Python 的兼容接口。

importlib.resources.as_file( _traversable_ )¶
~~~
    

~~~
