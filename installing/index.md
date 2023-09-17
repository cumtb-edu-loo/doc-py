# 安装 Python 模块¶

电子邮箱:

    

~~~
[distutils-sig@python.org](mailto:distutils-sig%40python.org)

作为一个流行的开源开发项目，Python拥有一个活跃的贡献者和用户支持社区，这些社区也可以让他们的软件可供其他Python开发人员在开源许可条款下使用。

这允许Python用户有效地共享和协作，从其他人已经创建的解决方案中受益于常见（有时甚至是罕见的）问题，以及可以提供他们自己的解决方案。

本指南涵盖了安装部分的流程。 有关创建和共享自己的 Python 项目的指导，请参阅 [Python packaging user guide](https://packaging.python.org/en/latest/tutorials/packaging-projects/)。

备注

对于企业和其他机构用户，请注意许多组织都有自己的政策来使用和贡献开源软件。在使用Python提供的分发和安装工具时，请考虑这些政策。

## 关键术语¶

  * `pip` 是首选的安装程序。从Python 3.4开始，它默认包含在Python二进制安装程序中。

  * _virtual environment_ 是一种半隔离的 Python 环境，允许为特定的应用安装各自的包，而不是安装到整个系统。

  * `venv` 是创建虚拟环境的标准工具，从 Python 3.3 开始成为 Python 的组成部分。 从 Python 3.4 开始，它会默认安装 `pip` 到所创建的全部虚拟环境。

  * `virtualenv` 是 `venv` 的第三方替代（及其前身）。 它允许在 Python 3.4 之前的版本中使用虚拟环境，那些版本或是完全不提供 `venv`，或是不会自动安装 `pip` 到所创建的虚拟环境。

  * [Python Package Index](https://pypi.org) 是一个开源许可的软件包公共存储库，可供所有 Python 用户使用。

  * [Python Packaging Authority](https://www.pypa.io/) 是负责标准打包工具以及相关元数据和文件格式标准维护与改进的开发人员和文档作者团队。 他们基于 [GitHub](https://github.com/pypa) 维护着各种工具、文档和问题追踪系统。

  * `distutils` 是最初的构建和分发系统，于 1998 年首次加入 Python 标准库。 虽然直接使用 `distutils` 的方式已被淘汰，它仍然是当前打包和分发架构的基础，而且它不仅仍然是标准库的一部分，这个名称还以其他方式存在（例如用于协调 Python 打包标准开发流程的邮件列表就以此命名）。

在 3.5 版本发生变更: 现在推荐使用 `venv` 来创建虚拟环境。

参见

[Python 软件包用户指南：创建和使用虚拟环境](https://packaging.python.org/installing/#creating-virtual-environments)

## 基本使用¶

标准打包工具完全是针对命令行使用方式来设计的。

以下命令将从 Python Package Index 安装一个模块的最新版本及其依赖项:
~~~
    
    
~~~
python -m pip install SomePackage
~~~

备注

对于 POSIX 用户（包括 macOS 和 Linux 用户）本指南中的示例假定使用了 [virtual environment](../glossary.md#term-virtual-environment)。

对于 Windows 用户，本指南中的示例假定在安装 Python 时选择了修改系统 PATH 环境变量。

在命令行中指定一个准确或最小版本也是可以的。 当使用比较运算符例如 `>`, `<` 或其他某些可以被终端所解析的特殊字符时，包名称与版本号应当用双引号括起来:

    
    
~~~
python -m pip install SomePackage==1.0.4    # specific version
python -m pip install "SomePackage>=1.0.4"  # minimum version
~~~

通常，如果一个匹配的模块已安装，尝试再次安装将不会有任何效果。 要升级现有模块必须显式地发出请求:

    
    
~~~
python -m pip install --upgrade SomePackage
~~~

更多有关 `pip` 及其功能的信息和资源可以在 [Python 软件包用户指南](https://packaging.python.org) 中找到。

虚拟环境的创建可使用 [`venv`](../library/venv.md#module-venv "venv: Creation of virtual environments.") 模块来完成。 向已激活虚拟环境安装软件包可使用上文所介绍的命令。

参见

[Python 软件包用户指南：安装 Python 分发包](https://packaging.python.org/installing/)

## 我应如何 ...？¶

这是一些常见任务的快速解答或相关链接。

### ... 在 Python 3.4 之前的 Python 版本中安装 `pip` ？¶

Python 捆绑 `pip` 是从 Python 3.4 才开始的。 对于更早的版本，`pip` 需要“引导安装”，具体说明参见 Python 软件包用户指南。

参见

[Python 软件包用户指南：安装软件包的前提要求](https://packaging.python.org/installing/#requirements-for-installing-packages)

### ... 只为当前用户安装软件包？¶

将 `--user` 选项传入 `python -m pip install` 将只为当前用户而非为系统中的所有用户安装软件包。

### ... 安装科学计算类 Python 软件包？¶

许多科学计算类 Python 软件包都有复杂的二进制编译文件依赖，直接使用 `pip` 安装目前并不太容易。 在当前情况下，通过 [其他方式](https://packaging.python.org/science/) 而非尝试用 `pip` 安装这些软件包对用户来说通常会更容易。

参见

[Python 软件包用户指南：安装科学计算类软件包](https://packaging.python.org/science/)

### ... 使用并行安装的多个 Python 版本？¶

在 Linux, macOS 以及其他 POSIX 系统中，使用带版本号的 Python 命令配合 `-m` 开关选项来运行特定版本的 `pip`:

    
    
~~~
python2   -m pip install SomePackage  # default Python 2
python2.7 -m pip install SomePackage  # specifically Python 2.7
python3   -m pip install SomePackage  # default Python 3
python3.4 -m pip install SomePackage  # specifically Python 3.4
~~~

也可以使用带特定版本号的 `pip` 命令。

在 Windows 中，使用 `py` Python 启动器命令配合 `-m` 开关选项:

    
    
~~~
py -2   -m pip install SomePackage  # default Python 2
py -2.7 -m pip install SomePackage  # specifically Python 2.7
py -3   -m pip install SomePackage  # default Python 3
py -3.4 -m pip install SomePackage  # specifically Python 3.4
~~~

## 常见的安装问题¶

### 在 Linux 的系统 Python 版本上安装¶

Linux 系统通常会将某个 Python 版本作为发行版的一部分包含在内。 将软件包安装到这个 Python 版本上需要系统 root 权限，并可能会干扰到系统包管理器和其他系统组件的运作，如果这些组件在使用 `pip` 时被意外升级的话。

在这样的系统上，通过 `pip` 安装软件包通常最好是使用虚拟环境或分用户安装。

### 未安装 pip¶

默认情况下可能未安装 `pip`，一种可选解决方案是:

    
    
~~~
python -m ensurepip --default-pip
~~~

还有其他附加资源可用来 [安装 pip。](https://packaging.python.org/en/latest/tutorials/installing-packages/#ensure-pip-setuptools-and-wheel-are-up-to-date)

### 安装二进制编译扩展¶

Python 通常非常依赖基于源代码的发布方式，也就是期望最终用户在安装过程中使用源码来编译生成扩展模块。

随着对二进制码 `wheel` 格式支持的引入，以及通过 Python Package Index 至少发布 Windows 和 macOS 版的 wheel 文件，预计此问题将逐步得到解决，因为用户将能够更频繁地安装预编译扩展，而不再需要自己编译它们。

某些用来安装 [科学计算类软件包](https://packaging.python.org/science/) 的解决方案对于尚未提供预编译 `wheel` 文件的那些扩展模块来说，也有助于用户在无需进行本机编译的情况下获取二进制码扩展模块。

参见

[Python 软件包用户指南：二进制码扩展](https://packaging.python.org/extensions/)

