# `sysconfig` —— 提供对 Python 配置信息的访问支持¶

在 3.2 版本加入.

**源代码：** [Lib/sysconfig.py](https://github.com/python/cpython/tree/3.12/Lib/sysconfig.py)

* * *

`sysconfig` 模块提供了对 Python 配置信息的访问支持，比如安装路径列表和有关当前平台的配置变量。

## 配置变量¶

一个包含 `Makefile` 和 `pyconfig.h` 头文件的 Python 分发版，这是构建 Python 二进制文件本身和用 `setuptools` 编译的第三方 C 扩展所必需的。

`sysconfig` 将这些文件中的所有变量放在一个字典对象中，可用 `get_config_vars()` 或 `get_config_var()` 访问。

请注意在 Windows 上，这是一个小得多的集合。

sysconfig.get_config_vars( _* args_)¶

    

~~~
不带参数时，返回一个与当前平台相关的所有配置变量的字典。

带参数时，返回一个由在配置变量字典中查找每个参数的结果的值组成的列表。

对于每个参数，如果未找到值，则返回 `None`。

sysconfig.get_config_var( _name_ )¶
~~~
    

~~~
返回单个变量 _name_ 的值。 等价于 `get_config_vars().get(name)`。

如果未找到 _name_ ，则返回 `None`。

用法示例:
~~~
    
    
~~~shell
>>> import sysconfig
>>> sysconfig.get_config_var('Py_ENABLE_SHARED')
0
>>> sysconfig.get_config_var('LIBDIR')
'/usr/local/lib'
>>> sysconfig.get_config_vars('AR', 'CXX')
['ar', 'g++']
~~~

## 安装路径¶

Python 会使用根据平台和安装选项区别处理的安装方案。 这些方案保存在 `sysconfig` 中基于 [`os.name`](os.md#os.name "os.name") 的返回值来确定的唯一标识符下。

每个使用 `distutils` 或基于 Distutils 的系统安装的新组件都将遵循同样的方案将其文件拷贝到正确的位置。

Python 目前支持九种方案:

  * _posix_prefix_ : 针对 POSIX 平台如 Linux 或 macOS 的方案。 这是在安装 Python 或者组件时的默认方案。

  * _posix_home_ : 当在安装时使用了 _home_ 选项时针对 POSIX 平台所使用的方案。 此方案会在通过 Distutils 附带指定的前缀安装组件时被使用。

  * _posix_user_ : 当通过 Distutils 安装组件并且使用了 _user_ 选项时针对 POSIX 平台所使用的方案。 此方案会在用户家目录下定义路径位置。

  * _posix_venv_ : 针对 POSIX 平台上 [`Python 虚拟环境`](3.标准库/venv.md#module-venv "venv: Creation of virtual environments.") 的方案；在默认情况下与 _posix_prefix_ 相同。

  * _nt_ : 针对 NT 平台如 Windows 的方案。

  * _nt_user_ : 针对 NT 平台，当使用了 _user_ 选项时的方案。

  * _nt_venv_ : 针对 NT 平台上 [`Python 虚拟环境`](3.标准库/venv.md#module-venv "venv: Creation of virtual environments.") 的方案；在默认情况下与 _nt_ 相同。

  * _venv_ : 根据 Python 运行所在平台的不同来设置 _posix_venv_ 或 _nt_venv_ 的值的方案。

  * _osx_framework_user_ : 针对 macOS，当使用了 _user_ 选项时的方案。

每个方案本身由一系列路径组成并且每个路径都有唯一的标识符。 Python 目前使用了八个路径:

  * _stdlib_ : 包含非平台专属的标准 Python 库文件的目录。

  * _platstdlib_ : 包含平台专属的标准 Python 库文件的目录。

  * _platlib_ : 用于站点专属、平台专属的文件的目录。

  * _purelib_ : 用于站点专属、非平台专属的文件的目录。

  * _include_ : 针对用于 Python C-API 的非平台专属头文件的目录。

  * _platinclude_ : 针对用于 Python C-API 的平台专属头文件的目录。

  * _scripts_ : 用于脚本文件的目录。

  * _data_ : 用于数据文件的目录。

`sysconfig` 提供了一些函数来确定这些路径。

sysconfig.get_scheme_names()¶

    

~~~
返回一个包含 `sysconfig` 目前支持的所有方案的元组。

sysconfig.get_default_scheme()¶
~~~
    

~~~
返回针对当前平台的默认方案的名称。

在 3.10 版本加入: 此函数之前被命名为 `_get_default_scheme()` 并被认为属性实现细节。

在 3.11 版本发生变更: 当 Python 运行于虚拟环境时，将返回 _venv_ 方案。

sysconfig.get_preferred_scheme( _key_ )¶
~~~
    

~~~
返回针对由 _key_ 所指定的安装布局的推荐方案的名称。

_key_ 必须为 `"prefix"`, `"home"` 或 `"user"`。

该返回值是 `get_scheme_names()` 中列出的一个方案名称。 它可以被传给接受 _scheme_ 参数的 `sysconfig` 函数，如 `get_paths()`。

在 3.10 版本加入.

在 3.11 版本发生变更: 当 Python 运行于虚拟环境且 `key="prefix"` 时，将返回 _venv_ 方案。

sysconfig._get_preferred_schemes()¶
~~~
    

~~~
返回一个包含当前平台推荐的方案名称的字典。 Python 的实现方和再分发方可以将他们推荐的方案添加到 `_INSTALL_SCHEMES` 模块层级全局值，并修改此函数以返回这些方案名称，例如为各种系统和语言的包管理器提供不同的方案，这样它们各自安装的包就不会彼此混淆。

最终用户不应使用此函数，而应改用 `get_default_scheme()` 和 `get_preferred_scheme()`。

在 3.10 版本加入.

sysconfig.get_path_names()¶
~~~
    

~~~
返回一个包含在 `sysconfig` 中目前支持的所有路径名称的元组。

sysconfig.get_path( _name_ [, _scheme_ [, _vars_ [, _expand_ ]]])¶
~~~
    

~~~
返回一个对应于路径 _name_ ，来自名为 _scheme_ 的安装方案的安装路径。

_name_ 必须是一个来自 `get_path_names()` 所返回的列表的值。

`sysconfig` 会针对每个平台保存与每个路径名称相对应的安装路径，并带有可扩展的变量。 例如针对 _nt_ 方案的 _stdlib_ 路径是: `{base}/Lib`。

`get_path()` 将使用 `get_config_vars()` 所返回的变量来扩展路径。 所有变量对于每种平台都有相应的默认值因此使用者可以调用此函数来获取默认值。

如果提供了 _scheme_ ，则它必须是一个来自 `get_scheme_names()` 所返回的列表的值。 在其他情况下，将会使用针对当前平台的默认方案。

如果提供了 _vars_ ，则它必须是一个将要更新 `get_config_vars()` 所返回的字典的变量字典。

如果 _expand_ 被设为 `False`，则将不使用这些变量来扩展路径。

如果 _name_ 未找到，则会引发 [`KeyError`](exceptions.md#KeyError "KeyError")。

sysconfig.get_paths([ _scheme_ [, _vars_ [, _expand_ ]]])¶
~~~
    

~~~
返回一个包含与特定安装方案对应的安装路径的字典。 请参阅 `get_path()` 了解详情。

如果未提供 _scheme_ ，则将使用针对当前平台的默认方案。

如果提供了 _vars_ ，则它必须是一个将要更新用于扩展的字典的变量字典。

如果 _expand_ 被设为假值，则路径将不会被扩展。

如果 _scheme_ 不是一个现有的方案，则 `get_paths()` 将引发 [`KeyError`](exceptions.md#KeyError "KeyError")。

## 其他功能¶

sysconfig.get_python_version()¶
~~~
    

~~~
以字符串形式 `MAJOR.MINOR` 返回 Python 版本号。 类似于 `'%d.%d' % sys.version_info[:2]`。

sysconfig.get_platform()¶
~~~
    

~~~
返回一个标识当前平台的字符串。

这主要被用来区分平台专属的构建目录和平台专属的构建分发版。 通常包括 OS 名称和版本以及架构（即 'os.uname()' 所提供的信息)，但是实际包括的信息取决于具体 OS；例如，在 Linux 上，内核版本号并不是特别重要。

返回值的示例：

  * linux-i586

  * linux-alpha (?)

  * solaris-2.6-sun4u

Windows将返回以下之一：

  * win-amd64 (在 AMD64, aka x86_64, Intel64, 和 EM64T上的64位 Windows )

  * win32（所有其他的 —— 确切地说，返回 sys.platform）

macOS 可以返回:

  * macosx-10.6-ppc

  * macosx-10.4-ppc64

  * macosx-10.3-i386

  * macosx-10.4-fat

对于其他非-POSIX 平台， 目前只是返回 [`sys.platform`](sys.md#sys.platform "sys.platform") 。

sysconfig.is_python_build()¶
~~~
    

~~~
如果正在运行的 Python 解释器是使用源代码构建的并在其构建位置上运行，而不是在其他位置例如通过运行 `make install` 或通过二进制机器码安装程序安装则返回 `True`。

sysconfig.parse_config_h( _fp_ [, _vars_ ])¶
~~~
    

~~~
解析一个 `config.h` 风格的文件。

_fp_ 是一个指向 `config.h` 风格的文件的文件类对象。

返回一个包含名称/值对的字典。 如果传入一个可选的字典作为第二个参数，则将使用它而不是新的字典，并使用从文件中读取的值更新它。

sysconfig.get_config_h_filename()¶
~~~
    

~~~
返回 `pyconfig.h` 的目录

sysconfig.get_makefile_filename()¶
~~~
    

~~~
返回 `Makefile` 的目录

## 将 `sysconfig` 作为脚本使用¶

You can use `sysconfig` as a script with Python's _-m_ option:
~~~
    
    
~~~
$ python -m sysconfig
Platform: "macosx-10.4-i386"
Python version: "3.2"
Current installation scheme: "posix_prefix"

Paths:
        data = "/usr/local"
        include = "/Users/tarek/Dev/svn.python.org/py3k/Include"
        platinclude = "."
        platlib = "/usr/local/lib/python3.2/site-packages"
        platstdlib = "/usr/local/lib/python3.2"
        purelib = "/usr/local/lib/python3.2/site-packages"
        scripts = "/usr/local/bin"
        stdlib = "/usr/local/lib/python3.2"

Variables:
        AC_APPLE_UNIVERSAL_BUILD = "0"
        AIX_GENUINE_CPLUSPLUS = "0"
        AR = "ar"
        ARFLAGS = "rc"
        ...
~~~

此调用将把 `get_platform()`, `get_python_version()`, `get_path()` 和 `get_config_vars()` 所返回的信息打印至标准输出。

