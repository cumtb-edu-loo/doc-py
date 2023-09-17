# `__main__` \--- 最高层级代码环境¶

* * *

在 Python 中，特殊名称 `__main__` 被用于两个重要的构造:

  1. 程序的最高层级环境的名称，可以使用 `__name__ == '__main__'` 表达式来检查它；以及

  2. Python 包中的 `__main__.py` 文件。

这两种机制都有 Python 模块有关；用户如何与它们交互以及它们之间如何交互。 下文将进行详细说明。 如果你还不了解 Python 模块，请查看教程 [模块](6.%20模块.md#tut-modules) 一节的介绍。

## `__name__ == '__main__'`¶

当一个 Python 模块或包被导入时，`__name__` 会被设为模块的名称。 通常，这将是 Python 文件本身的名称去掉 `.py` 后缀:

    
    
~~~shell
>>> import configparser
>>> configparser.__name__
'configparser'
~~~

如果文件是包的组成部分，则 `__name__` 还将包括父包的路径:

    
    
~~~shell
>>> from concurrent.futures import process
>>> process.__name__
'concurrent.futures.process'
~~~

不过，如果模块是在最高层级代码环境中执行的，则它的 `__name__` 会被设为字符串 `'__main__'`。

### 什么是“最高层级代码环境”？¶

`__main__` 是最高层级代码运行所在环境的名称。 “最高层级代码”即用户指定最先启动运行的 Python 模块。 它被称为“最高层级”是因为它将导入程序所需的所有其他模块。 有时“最高层级代码”也被称为应用的 _入口点_ 。

最高层级代码环境可以是:

  * 一个交互提示符的作用域:
    
        >>> __name__
    '__main__'
    

  * 作为文件参数传给 Python 解释器的 Python 模块:

> >     $ python helloworld.py
>     Hello, world!
>  

  * 作为 [`-m`](1.%20命令行与环境.md#cmdoption-m) 参数传给 Python 解释器的 Python 模块或包:

> >     $ python -m tarfile
>     usage: tarfile.py [-h] [-v] (...)
>  

  * Python 解释器从标准输入中读取的 Python 代码:

> >     $ echo "import this" | python
>     The Zen of Python, by Tim Peters
>  
>     Beautiful is better than ugly.
>     Explicit is better than implicit.
>     ...
>  

  * 作为 [`-c`](1.%20命令行与环境.md#cmdoption-c) 参数传递给 Python 解释器的 Python 代码:

> >     $ python -c "import this"
>     The Zen of Python, by Tim Peters
>  
>     Beautiful is better than ugly.
>     Explicit is better than implicit.
>     ...
>  

在以上每个情形中，顶级模块的 `__name__` 被设置为 `'__main__'` 。

因此，一个模块可以通过检查自己的 `__name__` ，来发现它是否在顶层环境中运行。这是允许在模块没有从导入语句中初始化的情况下，有条件地执行代码的一个常见的语句:

    
    
~~~
if __name__ == '__main__':
    # Execute when the module is not initialized from an import statement.
    ...
~~~

参见

关于在所有情况下 `__name__` 是如何设置的细节，请看教程部分 [模块](6.%20模块.md#tut-modules) 。

### 常见用法¶

有些模块包含了仅供脚本使用的代码，比如解析命令行参数或从标准输入获取数据。 如果这样的模块被从不同的模块中导入，例如为了单元测试，脚本代码也会无意中执行。

这就是 `if __name__ == '__main__'` 代码块的用武之地。除非模块在顶层环境中被执行，否则该块内的代码不会运行。

将尽可能少的语句放在位于 `if __name__ == '__main__'` 之下的代码块中可以提高代码的清晰度和准确度。 通常，将由一个名为 `main` 的函数来封装程序的主要行为:

    
    
~~~
# echo.py

import shlex
import sys

def echo(phrase: str) -> None:
   """A dummy wrapper around print."""
   # for demonstration purposes, you can imagine that there is some
   # valuable and reusable logic inside this function
   print(phrase)

def main() -> int:
    """Echo the input arguments to standard output"""
    phrase = shlex.join(sys.argv)
    echo(phrase)
    return 0

if __name__ == '__main__':
    sys.exit(main())  # next section explains the use of sys.exit
~~~

请注意，如果模块没有将代码封装在 `main` 函数内，而是直接放在 `if __name__ == '__main__'` 块内，那么这个 `phrase` 变量对整个模块来说就是全局变量。 这很容易出错，因为模块内的其他函数可能会无意中使用全局变量而不是局部名称。 一个 `main` 函数解决了这个问题。

使用 `main` 函数有一个额外的好处，就是 `echo` 函数本身是孤立的，可以在其他地方导入。当 `echo.py` 被导入时，`echo` 和 `main` 函数将被定义，但它们都不会被调用，因为 `__name__ != '__main__'` 。

### 打包考量¶

`main` 函数经常被用来创建命令行工具，把它们指定为控制台脚本的入口点。 当这样做时，[pip](https://pip.pypa.io/) 将函数调用插入到模板脚本中，其中 `main` 的返回值被传递到 [`sys.exit()`](3.标准库/sys.md#sys.exit "sys.exit") 。例如:

    
    
~~~
sys.exit(main())
~~~

由于 `main` 调用被包裹在 [`sys.exit()`](3.标准库/sys.md#sys.exit "sys.exit") 中，期望你的函数将返回一些可被 [`sys.exit()`](3.标准库/sys.md#sys.exit "sys.exit") 作为输入而接受的值；通常为一个整数或 `None` （如果你的函数没有返回语句，则隐含返回）。

通过主动遵循这一惯例，我们的模块在直接运行时 (即 `python echo.py`) 会有相同的行为，当我们以后把它打包成可用 pip 安装的软件包的控制台脚本入口时也会如此。

特别的是，要小心从你的 `main` 函数中返回字符串。 [`sys.exit()`](3.标准库/sys.md#sys.exit "sys.exit") 将把一个字符串参数解释为失败信息，所以你的程序将有一个 `1` 的退出代码，表示失败。并且这个字符串将被写入 [`sys.stderr`](3.标准库/sys.md#sys.stderr "sys.stderr") 。 前面的 `echo.py` 例子举例说明了使用 `sys.exit(main())` 的约定。

参见

[Python 打包用户指南](https://packaging.python.org/) 包含了一系列关于如何用现代工具分发和安装 Python 包的教程和参考资料。

## Python 包中的 `__main__.py`¶

如果你不熟悉Python包，请参阅本教程的 [包](6.%20模块.md#tut-packages) 一节。最常见的是， `__main__.py` 文件被用来为一个包提供命令行接口。假设有下面这个虚构的包，"bandclass":

    
    
~~~
bandclass
  ├── __init__.py
  ├── __main__.py
  └── student.py
~~~

当使用 [`-m`](1.%20命令行与环境.md#cmdoption-m) 标志从命令行直接调用软件包本身时，将执行 `__main__.py` 。比如说。

    
    
~~~
$ python -m bandclass
~~~

这个命令将导致 `__main__.py` 的运行。你如何利用这一机制将取决于你所编写的软件包的性质，但在这个假设的案例中，允许教师搜索学生可能是有意义的:

    
    
~~~
# bandclass/__main__.py

import sys
from .student import search_students

student_name = sys.argv[2] if len(sys.argv) >= 2 else ''
print(f'Found student: {search_students(student_name)}')
~~~

注意， `from .student import search_students` 是一个相对导入的例子。 这种导入方式可以在引用一个包内的模块时使用。 更多细节，请参见教程 [模块](6.%20模块.md#tut-modules) 中的 [相对导入](6.%20模块.md#intra-package-references) 一节。

### 常见用法¶

`__main__.py` 的内容通常不是用 `if __name__ == '__main__'` 区块围起来的。 相反，这些文件保持简短，功能从其他模块执行。 那些其他模块可以很容易地进行单元测试，并且可以适当地重复使用。

如果使用，一个 `if __name__ == '__main__'` 区块仍然会像预期的那样对包内的 `__main__.py` 文件起作用，因为如果导入，它的 `__name__` 属性将包括包的路径:

    
    
~~~shell
>>> import asyncio.__main__
>>> asyncio.__main__.__name__
'asyncio.__main__'
~~~

但这对 .zip 文件的根目录中的 `__main__.py` 文件不起作用。 因此，为了保持一致性，像下面提到的 [`venv`](3.标准库/venv.md#module-venv "venv: Creation of virtual environments.") 这样的最小 `__main__.py` 是首选。

参见

请参阅 [`venv`](3.标准库/venv.md#module-venv "venv: Creation of virtual environments.") 以获取标准库中具有最小化 `__main__.py` 的软件包示例。 它不包含 `if __name__ == '__main__'` 代码块。 你可以用 `python -m venv [directory]` 来发起调用。

参见 [`runpy`](runpy.md#module-runpy "runpy: Locate and run Python modules without importing them first.") 以了解更多关于 [`-m`](1.%20命令行与环境.md#cmdoption-m) 标志对解释器可执行包的细节。

参见 [`zipapp`](zipapp.md#module-zipapp "zipapp: Manage executable Python zip archives") 了解如何运行打包成 _.zip_ 文件的应用程序。在这种情况下，Python 会在归档文件的根目录下寻找一个 `__main__.py` 文件。

## `import __main__`¶

不管 Python 程序是用哪个模块启动的，在同一程序中运行的其他模块可以通过导入 `__main__` 模块来导入顶级环境的范围 ( [namespace](../glossary.md#term-namespace) )。这并不是导入一个 `__main__.py` 文件，而是导入使用特殊名称 `'__main__'` 的哪个模块。

下面是一个使用 `__main__` 命名空间的模块的例子:

    
    
~~~
# namely.py

import __main__

def did_user_define_their_name():
    return 'my_name' in dir(__main__)

def print_user_name():
    if not did_user_define_their_name():
        raise ValueError('Define the variable `my_name`!')

    if '__file__' in dir(__main__):
        print(__main__.my_name, "found in file", __main__.__file__)
    else:
        print(__main__.my_name)
~~~

该模块的用法示例如下:

    
    
~~~
# start.py

import sys

from namely import print_user_name

# my_name = "Dinsdale"

def main():
    try:
        print_user_name()
    except ValueError as ve:
        return str(ve)

if __name__ == "__main__":
    sys.exit(main())
~~~

现在，如果我们启动我们的程序，结果会是这样的:

    
    
~~~
$ python start.py
Define the variable `my_name`!
~~~

该程序的退出代码为 1 ，表明有错误。取消对 `my_name = "Dinsdale"` 这一行的注释，就可以修复程序，现在它的退出状态代码为 0 ，表示成功。

    
    
~~~
$ python start.py
Dinsdale found in file /path/to/start.py
~~~

请注意，导入 `__main__` 不会导致无意中运行旨在用于脚本的顶层代码的问题，这些代码被放在模块 `start` 的 `if __name__ == "__main__"` 块中。为什么这样做？

Python 解释器启动时会在 [`sys.modules`](3.标准库/sys.md#sys.modules "sys.modules") 中插入一个空的 `__main__` 模块，并通过运行最高层级代码来填充它。 在我们的例子中这就是 `start` 模块，它逐行运行并导入 `namely`。 相应地，`namely` 会导入 `__main__` (它实际上就是 `start`)。 这就是一个导入循环！ 幸运的是，由于部分填充的 `__main__` 模块存在于 [`sys.modules`](3.标准库/sys.md#sys.modules "sys.modules") 中，Python 会将其传递给 `namely`。 请参阅导入系统的参考文档中 [有关 __main__ 的特别考量](5.%20导入系统.md#import-dunder-main) 来了解其中的详情。

Python REPL 是另一个 "顶层环境 "的例子，所以在 REPL 中定义的任何东西都成为 `__main__` 范围的一部分:

    
    
~~~shell
>>> import namely
>>> namely.did_user_define_their_name()
False
>>> namely.print_user_name()
Traceback (most recent call last):
...
ValueError: Define the variable `my_name`!
>>> my_name = 'Jabberwocky'
>>> namely.did_user_define_their_name()
True
>>> namely.print_user_name()
Jabberwocky
~~~

注意，在这种情况下， `__main__` 范围不包含 `__file__` 属性，因为它是交互式的。

`__main__` 范围用于 [`pdb`](pdb.md#module-pdb "pdb: The Python debugger for interactive interpreters.") 和 [`rlcompleter`](rlcompleter.md#module-rlcompleter "rlcompleter: Python identifier completion, suitable for the GNU readline library.") 的实现。

