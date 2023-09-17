# `warnings` —— 警告信息的控制¶

**源代码：** [Lib/warnings.py](https://github.com/python/cpython/tree/3.12/Lib/warnings.py)

* * *

通常以下情况会引发警告：提醒用户注意程序中的某些情况，而这些情况（通常）还不值得触发异常并终止程序。例如，当程序用到了某个过时的模块时，就可能需要发出一条警告。

Python 程序员可调用本模块中定义的 `warn()` 函数来发布警告。（C 语言程序员则用 [`PyErr_WarnEx()`](10.C%20API接口/exceptions.md#c.PyErr_WarnEx "PyErr_WarnEx") ； 详见 [异常处理](10.C%20API接口/exceptions.md#exceptionhandling) ）。

警告信息通常会写入 [`sys.stderr`](3.标准库/sys.md#sys.stderr "sys.stderr") ，但可以灵活改变，从忽略所有警告到变成异常都可以。警告的处理方式可以依据 警告类型 、警告信息的文本和发出警告的源位置而进行变化。同一源位置重复出现的警告通常会被抑制。

控制警告信息有两个阶段：首先，每次引发警告时，决定信息是否要发出；然后，如果要发出信息，就用可由用户设置的钩子进行格式化并打印输出。

警告过滤器 控制着是否发出警告信息，也即一系列的匹配规则和动作。调用 `filterwarnings()` 可将规则加入过滤器，调用 `resetwarnings()` 则可重置为默认状态。

警告信息的打印输出是通过调用 `showwarning()` 完成的，该函数可被重写；默认的实现代码是调用 `formatwarning()` 进行格式化，自己编写的代码也可以调用此格式化函数。

参见

利用 [`logging.captureWarnings()`](3.标准库/logging.md#logging.captureWarnings "logging.captureWarnings") 可以采用标准的日志架构处理所有警告。

## 警告类别¶

警告的类别由一些内置的异常表示。这种分类有助于对警告信息进行分组过滤。

虽然在技术上警告类别属于 [内置异常](3.标准库/exceptions.md#warning-categories-as-exceptions)，但也只是在此记录一下而已，因为在概念上他们属于警告机制的一部分。

通过对某个标准的警告类别进行派生，用户代码可以定义其他的警告类别。 警告类别必须是 [`Warning`](3.标准库/exceptions.md#Warning "Warning") 类的子类。

目前已定义了以下警告类别的类：

类

|

描述  
  
---|---  
  
[`Warning`](3.标准库/exceptions.md#Warning "Warning")

|

这是所有警告类别的基类。它是 [`Exception`](3.标准库/exceptions.md#Exception "Exception") 的子类。  
  
[`UserWarning`](3.标准库/exceptions.md#UserWarning "UserWarning")

|

The default category for `warn()`.  
  
[`DeprecationWarning`](3.标准库/exceptions.md#DeprecationWarning "DeprecationWarning")

|

已废弃特性警告的基类，这些警告是为其他 Python 开发者准备的（默认会忽略，除非在 `__main__` 中用代码触发）。  
  
[`SyntaxWarning`](3.标准库/exceptions.md#SyntaxWarning "SyntaxWarning")

|

用于警告可疑语法的基类。  
  
[`RuntimeWarning`](3.标准库/exceptions.md#RuntimeWarning "RuntimeWarning")

|

用于警告可疑运行时特性的基类。  
  
[`FutureWarning`](3.标准库/exceptions.md#FutureWarning "FutureWarning")

|

用于警告已废弃特性的基类，这些警告是为 Python 应用程序的最终用户准备的。  
  
[`PendingDeprecationWarning`](3.标准库/exceptions.md#PendingDeprecationWarning "PendingDeprecationWarning")

|

用于警告即将废弃功能的基类（默认忽略）。  
  
[`ImportWarning`](3.标准库/exceptions.md#ImportWarning "ImportWarning")

|

导入模块时触发的警告的基类（默认忽略）。  
  
[`UnicodeWarning`](3.标准库/exceptions.md#UnicodeWarning "UnicodeWarning")

|

用于 Unicode 相关警告的基类。  
  
[`BytesWarning`](3.标准库/exceptions.md#BytesWarning "BytesWarning")

|

[`bytes`](stdtypes.md#bytes "bytes") 和 [`bytearray`](stdtypes.md#bytearray "bytearray") 相关警告的基类。  
  
[`ResourceWarning`](3.标准库/exceptions.md#ResourceWarning "ResourceWarning")

|

资源使用相关警告的基础类别（默认会被忽略）。ignored by default).  
  
在 3.7 版本发生变更: 以前 [`DeprecationWarning`](3.标准库/exceptions.md#DeprecationWarning "DeprecationWarning") 和 [`FutureWarning`](3.标准库/exceptions.md#FutureWarning "FutureWarning") 是根据某个功能是否完全删除或改变其行为来区分的。现在是根据受众和默认警告过滤器的处理方式来区分的。

## 警告过滤器¶

警告过滤器控制着警告是否被忽略、显示或转为错误（触发异常）。

从概念上讲，警告过滤器维护着一个经过排序的过滤器类别列表；任何具体的警告都会依次与列表中的每种过滤器进行匹配，直到找到一个匹配项；过滤器决定了匹配项的处理方式。每个列表项均为 ( _action_ , _message_ , _category_ , _module_ , _lineno_ ) 格式的元组，其中：

  * _action_ 是以下字符串之一：

值

|

处置  
  
---|---  
  
`"default"`

|

为发出警告的每个位置（模块+行号）打印第一个匹配警告  
  
`"error"`

|

将匹配警告转换为异常  
  
`"ignore"`

|

从不打印匹配的警告  
  
`"always"`

|

总是打印匹配的警告  
  
`"module"`

|

为发出警告的每个模块打印第一次匹配警告（无论行号如何）  
  
`"once"`

|

无论位置如何，仅打印第一次出现的匹配警告  
  
  * _message_ 是一个包含警告消息的开头需要匹配的正则表达式的字符串，对大小写不敏感。 在 [`-W`](1.%20命令行与环境.md#cmdoption-W) 和 [`PYTHONWARNINGS`](1.%20命令行与环境.md#envvar-PYTHONWARNINGS) 中， _message_ 是警告消息的开头需要包含的字符串字面值（对大小写不敏感），将忽略 _message_ 开头和末尾的任何空格。

  * _category_ 是警告类别的类（[`Warning`](3.标准库/exceptions.md#Warning "Warning") 的子类），警告类别必须是其子类，才能匹配。

  * _module_ 是一个包含完整限定模块名称的开头需要匹配的正则表达式的字符串，对大小写敏感。 在 [`-W`](1.%20命令行与环境.md#cmdoption-W) 和 [`PYTHONWARNINGS`](1.%20命令行与环境.md#envvar-PYTHONWARNINGS) 中， _module_ 是完整限定模块名称需要与之相等的字符串字面值（对大小写敏感），将忽略 _module_ 开头和末尾的任何空格。

  * _lineno_ 是个整数，发生警告的行号必须与之匹配，或为 `0` 表示与所有行号匹配。

由于 [`Warning`](3.标准库/exceptions.md#Warning "Warning") 类是由内置类 [`Exception`](3.标准库/exceptions.md#Exception "Exception") 派生出来的，要把某个警告变成错误，只要触发 `category(message)` 即可。

如果警告不匹配所有已注册的过滤器，那就会应用 “default” 动作（正如其名）。

### 警告过滤器的介绍¶

警告过滤器由传给 Python 解释器的命令行 [`-W`](1.%20命令行与环境.md#cmdoption-W) 选项和 [`PYTHONWARNINGS`](1.%20命令行与环境.md#envvar-PYTHONWARNINGS) 环境变量初始化。解释器在 `sys.warningoptions` 中保存了所有给出的参数，但不作解释；`warnings` 模块在第一次导入时会解析这些参数（无效的选项被忽略，并会先向 [`sys.stderr`](3.标准库/sys.md#sys.stderr "sys.stderr") 打印一条信息）。

每个警告过滤器的设定格式为冒号分隔的字段序列：

    
    
~~~
action:message:category:module:line
~~~

这些字段的含义在 警告过滤器 中描述。当一行中列出多个过滤器时（如 [`PYTHONWARNINGS`](1.%20命令行与环境.md#envvar-PYTHONWARNINGS)），过滤器间用逗号隔开，后面的优先于前面的（因为是从左到右应用的，最近应用的过滤器优先于前面的）。

常用的警告过滤器适用于所有的警告、特定类别的警告、由特定模块和包引发的警告。下面是一些例子：

    
    
~~~python
default                      # Show all warnings (even those ignored by default)
ignore                       # Ignore all warnings
error                        # Convert all warnings to errors
error::ResourceWarning       # Treat ResourceWarning messages as errors
default::DeprecationWarning  # Show DeprecationWarning messages
ignore,default:::mymodule    # Only report warnings triggered by "mymodule"
error:::mymodule             # Convert warnings to errors in "mymodule"
~~~

### 默认警告过滤器¶

Python 默认安装了几个警告过滤器，可以通过 [`-W`](1.%20命令行与环境.md#cmdoption-W) 命令行参数、 [`PYTHONWARNINGS`](1.%20命令行与环境.md#envvar-PYTHONWARNINGS) 环境变量及调用 `filterwarnings()` 进行覆盖。

在常规发布的版本中，默认的警告过滤器包括（按优先顺序排列）：

    
    
~~~python
default::DeprecationWarning:__main__
ignore::DeprecationWarning
ignore::PendingDeprecationWarning
ignore::ImportWarning
ignore::ResourceWarning
~~~

在 [调试版本](3.%20配置%20Python.md#debug-build) 中，默认警告过滤器的列表是空的。

在 3.2 版本发生变更: 除了 [`PendingDeprecationWarning`](3.标准库/exceptions.md#PendingDeprecationWarning "PendingDeprecationWarning") 之外，[`DeprecationWarning`](3.标准库/exceptions.md#DeprecationWarning "DeprecationWarning") 现在默认会被忽略。

在 3.7 版本发生变更: [`DeprecationWarning`](3.标准库/exceptions.md#DeprecationWarning "DeprecationWarning") 在被 `__main__` 中的代码直接触发时，默认会再次显示。

在 3.7 版本发生变更: 如果指定两次 [`-b`](1.%20命令行与环境.md#cmdoption-b)，则 [`BytesWarning`](3.标准库/exceptions.md#BytesWarning "BytesWarning") 不再出现在默认的过滤器列表中，而是通过 `sys.warningoptions` 进行配置。

### 重写默认的过滤器¶

Python 应用程序的开发人员可能希望在默认情况下向用户隐藏 _所有_ Python级别的警告，而只在运行测试或其他调试时显示这些警告。用于向解释器传递过滤器配置的 `sys.warningoptions` 属性可以作为一个标记，表示是否应该禁用警告：

    
    
~~~
import sys

if not sys.warnoptions:
    import warnings
    warnings.simplefilter("ignore")
~~~

建议 Python 代码测试的开发者使用如下代码，以确保被测代码默认显示 _所有_ 警告：

    
    
~~~
import sys

if not sys.warnoptions:
    import os, warnings
    warnings.simplefilter("default") # Change the filter in this process
    os.environ["PYTHONWARNINGS"] = "default" # Also affect subprocesses
~~~

最后，建议在 `__main__` 以外的命名空间运行用户代码的交互式开发者，请确保 [`DeprecationWarning`](3.标准库/exceptions.md#DeprecationWarning "DeprecationWarning") 在默认情况下是可见的，可采用如下代码（这里 `user_ns` 是用于执行交互式输入代码的模块）：

    
    
~~~
import warnings
warnings.filterwarnings("default", category=DeprecationWarning,
                                   module=user_ns.get("__name__"))
~~~

## 暂时禁止警告¶

如果明知正在使用会引起警告的代码，比如某个废弃函数，但不想看到警告（即便警告已经通过命令行作了显式配置），那么可以使用 `catch_warnings` 上下文管理器来抑制警告。

    
    
~~~
import warnings

def fxn():
    warnings.warn("deprecated", DeprecationWarning)

with warnings.catch_warnings():
    warnings.simplefilter("ignore")
    fxn()
~~~

在上下文管理器中，所有的警告将被简单地忽略。这样就能使用已知的过时代码而又不必看到警告，同时也不会限制警告其他可能不知过时的代码。注意：只能保证在单线程应用程序中生效。如果两个以上的线程同时使用 `catch_warnings` 上下文管理器，行为不可预知。

## 测试警告¶

要测试由代码引发的警告，请采用 `catch_warnings` 上下文管理器。有了它，就可以临时改变警告过滤器以方便测试。例如，以下代码可捕获所有的警告以便查看：

    
    
~~~
import warnings

def fxn():
    warnings.warn("deprecated", DeprecationWarning)

with warnings.catch_warnings(record=True) as w:
    # Cause all warnings to always be triggered.
    warnings.simplefilter("always")
    # Trigger a warning.
    fxn()
    # Verify some things
    assert len(w) == 1
    assert issubclass(w[-1].category, DeprecationWarning)
    assert "deprecated" in str(w[-1].message)
~~~

也可以用 `error` 取代 `always` ，让所有的警告都成为异常。需要注意的是，如果某条警告已经因为 `once` / `default` 规则而被引发，那么无论设置什么过滤器，该条警告都不会再出现，除非该警告有关的注册数据被清除。

一旦上下文管理器退出，警告过滤器将恢复到刚进此上下文时的状态。这样在多次测试时可防止意外改变警告过滤器，从而导致不确定的测试结果。模块中的 `showwarning()` 函数也被恢复到初始值。注意：这只能在单线程应用程序中得到保证。如果两个以上的线程同时使用 `catch_warnings` 上下文管理器，行为未定义。

当测试多项操作会引发同类警告时，重点是要确保每次操作都会触发新的警告（比如，将警告设置为异常并检查操作是否触发异常，检查每次操作后警告列表的长度是否有增加，否则就在每次新操作前将以前的警告列表项删除）。

## 为新版本的依赖关系更新代码¶

在默认情况下，主要针对 Python 开发者（而不是 Python 应用程序的最终用户）的警告类别，会被忽略。

值得注意的是，这个“默认忽略”的列表包含 [`DeprecationWarning`](3.标准库/exceptions.md#DeprecationWarning "DeprecationWarning") （适用于每个模块，除了 `__main__`），这意味着开发人员应该确保在测试代码时应将通常忽略的警告显示出来，以便未来破坏性 API 变化时及时收到通知（无论是在标准库还是第三方包）。

理想情况下，代码会有一个合适的测试套件，在运行测试时会隐含地启用所有警告（由 [`unittest`](unittest.md#module-unittest "unittest: Unit testing framework for Python.") 模块提供的测试运行程序就是如此）。

在不太理想的情况下，可以通过向 Python 解释器传入 [`-Wd`](1.%20命令行与环境.md#cmdoption-W) (这是 `-W default` 的简写) 或设置环境变量 `PYTHONWARNINGS=default` 来检查应用程序是否用到了已弃用的接口。 这样可以启用对所有警告的默认处理操作，包括那些默认忽略的警告。 要改变遇到警告后执行的动作，可以改变传给 [`-W`](1.%20命令行与环境.md#cmdoption-W) 的参数 (例如 `-W error`)。 请参阅 [`-W`](1.%20命令行与环境.md#cmdoption-W) 旗标来了解更多的细节。

## 可用的函数¶

warnings.warn( _message_ , _category=None_ , _stacklevel=1_ , _source=None_ , _\\*_ , _skip_file_prefixes=None_ )¶

    

~~~
引发警告、忽略或者触发异常。 如果给出 _category_ 参数，则必须是 警告类别类 ；默认为 [`UserWarning`](exceptions.md#UserWarning "UserWarning")。 或者 _message_ 可为 [`Warning`](exceptions.md#Warning "Warning") 的实例，这时 _category_ 将被忽略，转而采用 `message.__class__`。 在这种情况下，错误信息文本将是 `str(message)`。 如果某条警告被 警告过滤器 改成了错误，本函数将触发一条异常。 参数 _stacklevel_ 可供 Python 包装函数使用，比如:
~~~
    
    
~~~python
def deprecated_api(message):
    warnings.warn(message, DeprecationWarning, stacklevel=2)
~~~

这会让警告指向 `deprecated_api` 的调用者，而不是 `deprecated_api` 本身的来源（因为后者会破坏警告消息的目的）。

_skip_file_prefixes_ 关键字参数可被用来指明在栈层级计数时哪些栈帧要被忽略。 当常数 _stacklevel_ 不能适应所有调用路径或在其他情况下难以维护如果你希望警告总是在一个包以外的调用位置上出现这将会很有用处。 如果提供，则它必须是一个字符串元组。 当提供了 prefixes 前缀时，stacklevel 会被隐式地覆盖为 `max(2, stacklevel)`。 要使得一个警告被归因至当前包以外的调用方你可以这样写:

    
    
~~~
# example/lower.py
_warn_skips = (os.path.dirname(__file__),)

def one_way(r_luxury_yacht=None, t_wobbler_mangrove=None):
    if r_luxury_yacht:
        warnings.warn("Please migrate to t_wobbler_mangrove=.",
                      skip_file_prefixes=_warn_skips)

# example/higher.py
from . import lower

def another_way(**kw):
    lower.one_way(**kw)
~~~

这将使得警告同时指向 `example.lower.one_way()` 和来自 `example` 包以外的调用代码的 `package.higher.another_way()` 调用位置。

_source_ 是发出 [`ResourceWarning`](3.标准库/exceptions.md#ResourceWarning "ResourceWarning") 的被销毁对象。

在 3.6 版本发生变更: 加入 _source_ 参数。

在 3.12 版本发生变更: 增加了 _skip_file_prefixes_ 。

warnings.warn_explicit( _message_ , _category_ , _filename_ , _lineno_ , _module =None_, _registry =None_, _module_globals =None_, _source =None_)¶

    

~~~
这是 `warn()` 函数的底层接口，显式传入消息、类别、文件名和行号，以及可选的模块名和注册表（应为模块的 `__warningregistry__` 字典）。 模块名称默认为去除了 `.py` 的文件名；如果未传递注册表，警告就不会被抑制。 _message_ 必须是个字符串， _category_ 是 [`Warning`](exceptions.md#Warning "Warning") 的子类；或者*message* 可为 [`Warning`](exceptions.md#Warning "Warning") 的实例，且 _category_ 将被忽略。

_module_globals_ 应为发出警告的代码所用的全局命名空间。（该参数用于从 zip 文件或其他非文件系统导入模块时显式源码）。

_source_ 是发出 [`ResourceWarning`](exceptions.md#ResourceWarning "ResourceWarning") 的被销毁对象。

在 3.6 版本发生变更: 加入 _source_ 参数。

warnings.showwarning( _message_ , _category_ , _filename_ , _lineno_ , _file =None_, _line =None_)¶
~~~
    

~~~
将警告信息写入文件。默认的实现代码是调用 `formatwarning(message, category, filename, lineno, line)` 并将结果字符串写入 _file_ ，默认文件为 [`sys.stderr`](sys.md#sys.stderr "sys.stderr")。通过将任何可调用对象赋给 `warnings.showwarning` 可替换掉该函数。 _line_ 是要包含在警告信息中的一行源代码；如果未提供 _line_ ，`showwarning()` 将尝试读取由*filename* 和 _lineno_ 指定的行。

warnings.formatwarning( _message_ , _category_ , _filename_ , _lineno_ , _line =None_)¶
~~~
    

~~~
以标准方式格式化一条警告信息。将返回一个字符串，可能包含内嵌的换行符，并以换行符结束。如果未提供 _line_ ，`formatwarning()` 将尝试读取由 _filename_ 和 _lineno_ 指定的行。

warnings.filterwarnings( _action_ , _message =''_, _category =Warning_, _module =''_, _lineno =0_, _append =False_)¶
~~~
    

~~~
在 警告过滤器种类 列表中插入一条数据项。默认情况下，该数据项将被插到前面；如果 _append_ 为 True，则会插到后面。这里会检查参数的类型，编译 _message_ 和 _module_ 正则表达式，并将他们作为一个元组插入警告过滤器的列表中。如果两者都与某种警告匹配，那么靠近列表前面的数据项就会覆盖后面的项。省略的参数默认匹配任意值。

warnings.simplefilter( _action_ , _category =Warning_, _lineno =0_, _append =False_)¶
~~~
    

~~~
在 警告过滤器种类 列表中插入一条简单数据项。函数参数的含义与 `filterwarnings()` 相同，但不需要正则表达式，因为插入的过滤器总是匹配任何模块中的任何信息，只要类别和行号匹配即可。

warnings.resetwarnings()¶
~~~
    

~~~
重置警告过滤器。这将丢弃之前对 `filterwarnings()` 的所有调用，包括 [`-W`](../using/cmdline.md#cmdoption-W) 命令行选项和对 `simplefilter()` 的调用效果。

## 可用的上下文管理器¶

_class _warnings.catch_warnings( _*_ , _record =False_, _module =None_, _action =None_, _category =Warning_, _lineno =0_, _append =False_)¶
~~~
    

~~~
