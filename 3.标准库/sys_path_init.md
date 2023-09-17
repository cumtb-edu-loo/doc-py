# [`sys.path`](3.标准库/sys.md#sys.path "sys.path") 模块搜索路径的初始化¶

模块搜索路径是在 Python 启动时被初始化的。 这个模块搜索路径可通过 [`sys.path`](3.标准库/sys.md#sys.path "sys.path") 来访问。

模块搜索路径的第一个条目是包含输入脚本的目录，如果存在输入脚本的话。 否则，第一个条目将是当前目录，当执行交互式 shell, [`-c`](1.%20命令行与环境.md#cmdoption-c) 命令, 或 [`-m`](1.%20命令行与环境.md#cmdoption-m) 模块时都属于这种情况。

[`PYTHONPATH`](1.%20命令行与环境.md#envvar-PYTHONPATH) 环境变量经常被用于将目录添加到搜索路径。 如果发现了该环境变量则其内容将被添加到模块搜索路径中。

备注

[`PYTHONPATH`](1.%20命令行与环境.md#envvar-PYTHONPATH) 将影响所有已安装的 Python 版本/环境。 在你的 shell 用户配置或全局环境变量中设置它时需要小心谨慎。 [`site`](site.md#module-site "site: Module responsible for site-specific configuration.") 模块提供了下文所述的更细微的技巧。

随后加入的条目是包含标准 Python 模块以及这些模块所依赖的任何 [extension module](../glossary.md#term-extension-module) 的目录。 扩展模块在 Windows 上为 `.pyd` 文件而在其他平台上则为 `.so` 文件。 独立于平台的 Python 模块的目录称为 `prefix`。 扩展模块的目录称为 `exec_prefix`。

[`PYTHONHOME`](1.%20命令行与环境.md#envvar-PYTHONHOME) 环境变量可以被用于设置 `prefix` 和 `exec_prefix` 的位置。 在其他情况下这些目录将使用 Python 可执行文件作为起始点来确定然后再查找几处 '地标' 文件和目录。 请注意任何符号链接也会被引入以便使用实际的 Python 可执行文件位置作为搜索起始点。 这个 Python 可执行文件位置被称为 `home`。

一旦确定了 `home`，则 `prefix` 目录将通过首先查找 `python _majorversion_ _minorversion_.zip` (`python311.zip`) 来找到。 在 Windows 上将会到 `home` 中搜索 zip 归档而在 Unix 上则会到 `lib` 中搜索它。 请注意预期的 zip 归档位置即使在此归档不存在时仍然会被添加到模块搜索路径。 如果未找到归档，在 Windows 上 Python 将继续通过查找 `Lib\os.py` 来搜索 `prefix`。 在 Unix 上 Python 将查找 `lib/python _majorversion_. _minorversion_ /os.py` (`lib/python3.11/os.py`)。 在 Windows 上 `prefix` 和 `exec_prefix` 是相同的，但是在其他平台上则会搜索 `lib/python _majorversion_. _minorversion_ /lib-dynload` (`lib/python3.11/lib-dynload`) 并将其用作 `exec_prefix` 的锚点。 在某些平台上 `lib` 可能为 `lib64` 或其他值，请参阅 [`sys.platlibdir`](3.标准库/sys.md#sys.platlibdir "sys.platlibdir") 和 [`PYTHONPLATLIBDIR`](1.%20命令行与环境.md#envvar-PYTHONPLATLIBDIR)。

一旦找到，`prefix` 和 `exec_prefix` 将分别在 [`sys.prefix`](3.标准库/sys.md#sys.prefix "sys.prefix") 和 [`sys.exec_prefix`](3.标准库/sys.md#sys.exec_prefix "sys.exec_prefix") 上可用。

最后，将会处理 [`site`](site.md#module-site "site: Module responsible for site-specific configuration.") 模块并将 `site-packages` 目录添加到模块搜索路径。 自定义搜索路径的一个常用方式是创建 `sitecustomize` 或 `usercustomize` 模块，如 [`site`](site.md#module-site "site: Module responsible for site-specific configuration.") 模块文档所描述的那样。

备注

特定的命令行选项可能对路径计算造成额外的影响。 请参阅 [`-E`](1.%20命令行与环境.md#cmdoption-E), [`-I`](1.%20命令行与环境.md#cmdoption-I), [`-s`](1.%20命令行与环境.md#cmdoption-s) 和 [`-S`](1.%20命令行与环境.md#cmdoption-S) 了解更多细节。

## 从虚拟环境¶

如果 Python 运行在虚拟环境中（如 [虚拟环境和包](12.%20虚拟环境和包.md#tut-venv) 所描述）则 `prefix` 和 `exec_prefix` 都将是该虚拟环境专属的。

如果在主可执行文件的相同位置，或者在可执行文件的上一级目录中找到了 `pyvenv.cfg` 文件，则将应用以下变化形式:

  * 如果 `home` 是一个绝对路径并且未设置 [`PYTHONHOME`](1.%20命令行与环境.md#envvar-PYTHONHOME)，则在推断 `prefix` 和 `exec_prefix` 时将使用此路径而不是主可执行文件的路径。

## _pth 文件¶

若要完全覆盖 [`sys.path`](3.标准库/sys.md#sys.path "sys.path") 则请创建一个与共享库或可执行文件 (`python._pth` 或 `python311._pth`) 同名的 `._pth` 文件。 共享库路径在 Windows 是始终是已知的，但这在其他平台上也许会不可用。 请在 `._pth` 文件中为添加到 [`sys.path`](3.标准库/sys.md#sys.path "sys.path") 的每个路径指定对应的一行。 基于共享库名称的文件会覆盖基于可执行文件的对应文件，这允许在必要时为任何加载运行时的程序限制路径。

当文件存在时，将忽略所有注册表和环境变量，启用隔离模式，并且：除非文件中的一行指定 `import site` ，否则不会导入 [`site`](site.md#module-site "site: Module responsible for site-specific configuration.") 。以 `＃` 开头的空白路径和行将被忽略。每个路径可以是绝对的或相对于文件的位置。不允许使用除 `site` 以外的导入语句，并且不能指定任意代码。

请注意，当指定 `import site` 时， `.pth` 文件（没有前导下划线）将由 [`site`](site.md#module-site "site: Module responsible for site-specific configuration.") 模块正常处理。

## 嵌入式 Python¶

If Python is embedded within another application [`Py_InitializeFromConfig()`](init_config.md#c.Py_InitializeFromConfig "Py_InitializeFromConfig") and the [`PyConfig`](init_config.md#c.PyConfig "PyConfig") structure can be used to initialize Python. The path specific details are described at [Python 路径配置](init_config.md#init-path-config).

参见

  * [查找模块](4.%20在Windows上使用%20Python.md#windows-finding-modules) 了解更多有关 Windows 的细节说明。

  * [在类Unix环境下使用Python](2.%20在类Unix环境下使用Python.md#using-on-unix) 了解 Unix 的相关细节。

