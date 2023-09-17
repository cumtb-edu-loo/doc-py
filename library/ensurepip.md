# `ensurepip` \--- 引导 `pip` 安装器¶

在 3.4 版本加入.

**源代码:** [Lib/ensurepip](https://github.com/python/cpython/tree/3.12/Lib/ensurepip)

* * *

`ensurepip` 包为在已有的Python安装实例或虚拟环境中引导 `pip` 安装器提供了支持。需要使用引导才能使用pip的这一事实也正好反映了 `pip` 是一个独立的项目，有其自己的发布周期，其最新版本随CPython解释器的维护版本和新特性版本一同捆绑。

在大多数情况下，Python的终端使用者不需要直接调用这个模块（ `pip` 默认应该已被引导），不过，如果在安装Python（或创建虚拟环境）之时跳过了安装 `pip` 步骤，或者日后特意卸载了 `pip` ，则需要使用这个模块。

备注

这个模块 _无需_ 访问互联网。引导启动 `pip` 所需的全部组件均包含在包的内部。

参见

[安装 Python 模块](../installing/index.md#installing-index)

    

~~~
安装Python包的终端使用者教程

[**PEP 453**](https://peps.python.org/pep-0453/): 在Python安装实例中显式引导启动pip
~~~
    

~~~
这个模块的原始缘由以及规范文档

[可用性](intro.md#availability): 非 Emscripten，非 WASI。

此模块在 WebAssembly 平台 `wasm32-emscripten` 和 `wasm32-wasi` 上不适用或不可用。 请参阅 [WebAssembly 平台](intro.md#wasm-availability) 了解详情。

## 命令行界面¶

使用解释器的 `-m` 参数调用命令行接口。

最简单的调用方式为：
~~~
    
    
~~~
python -m ensurepip
~~~

该调用会在当前未安装 `pip` 的情况下安装 `pip` ，如已安装则无事发生。如要确保安装的 `pip` 版本至少为 `ensurepip` 所支援的最新版本，传入 `--upgrade` 参数：

    
    
~~~
python -m ensurepip --upgrade
~~~

在默认情况下，`pip` 会被安装到当前虚拟环境（如果激活了虚拟环境）或系统的包目录（如果未激活虚拟环境）。 安装位置可通过两个额外的命令行选项来控制:

  * `--root 1`: 相对于给定的根目录而不是当前已激活虚拟环境（如果存在）的根目录或当前 Python 安装版的默认根目录来安装 `pip`。

  * `--user`: 将 `pip` 安装到用户包目录而不是全局安装到当前 Python 安装版（此选项不允许在已激活虚拟环境中使用）。

在默认情况下，脚本 `pipX` 和 `pipX.Y` 将被安装（其中 X.Y 表示被用来发起调用 `ensurepip` 的 Python 的版本）。 所安装的脚本可通过两个额外的命令行选项来控制:

  * `--altinstall`: 如果请求了一个替代安装版，则 `pipX` 脚本将 _不会_ 被安装。

  * `--default-pip`: 如果请求了一个 "默认的 pip" 安装版，则除了两个常规脚本之外还将安装 `pip` 脚本。

同时提供这两个脚本选择选项将会触发异常。

## 模块 API¶

`ensurepip` 暴露了两个函数用于编程:

ensurepip.version()¶

    

~~~
返回一个指明在初始创建环境时将被安装的可用 pip 版本的字符串。

ensurepip.bootstrap( _root =None_, _upgrade =False_, _user =False_, _altinstall =False_, _default_pip =False_, _verbosity =0_)¶
~~~
    

~~~
