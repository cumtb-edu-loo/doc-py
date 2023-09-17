# `pydoc` \--- 文档生成器和在线帮助系统¶

**源代码:** [Lib/pydoc.py](https://github.com/python/cpython/tree/3.12/Lib/pydoc.py)

* * *

`pydoc` 模块会根据 Python 模块来自动生成文档。 生成的文档可在控制台中显示为文本页面，提供给 Web 浏览器访问或者保存为 HTML 文件。

对于模块、类、函数和方法，显示的文档内容取自文档字符串（即 `__doc__` 属性），并会递归地从其带文档的成员中获取。 如果没有文档字符串，`pydoc` 会尝试从类、函数或方法定义上方，或是模块顶部的注释行段落获取 (参见 [`inspect.getcomments()`](inspect.md#inspect.getcomments "inspect.getcomments")).

内置函数 [`help()`](functions.md#help "help") 会发起调用交互式解释器的在线帮助系统，该系统使用 `pydoc` 在终端上生成文本形式的文档内容。 同样的文本文档也可以在 Python 解释器以外通过在操作系统的命令提示符下以脚本方式运行 **pydoc** 来查看。 例如，运行

    
    
~~~
python -m pydoc sys
~~~

在终端提示符下将通过 [`sys`](3.标准库/sys.md#module-sys "sys: Access system-specific parameters and functions.") 模块显示文档内容，其样式类似于 Unix **man** 命令所显示的指南页面。 **pydoc** 的参数可以为函数、模块、包，或带点号的对模块中的类、方法或函数以及包中的模块的引用。 如果传给 **pydoc** 的参数像是一个路径（即包含所在操作系统的路径分隔符，例如 Unix 的正斜杠），并且其指向一个现有的 Python 源文件，则会为该文件生成文档内容。

备注

为了找到对象及其文档内容，`pydoc` 会导入文档所在的模块。 因此，任何模块层级的代码都将被执行。 请使用 `if __name__ == '__main__':` 语句来确保一个文件的特定代码仅在作为脚本被发起调用时执行而不是在被导入时执行。

当打印输出到控制台时， **pydoc** 会尝试对输出进行分页以方便阅读。 如果设置了 `PAGER` 环境变量， **pydoc** 将使用该变量值作为分页程序。

在参数前指定 `-w` 旗标将把 HTML 文档写入到当前目录下的一个文件中，而不是在控制台中显示文本。

在参数前指定 `-k` 旗标将在全部可用模块的提要行中搜索参数所给定的关键字，具体方式同样类似于 Unix **man** 命令。 模块的提要行就是其文档字符串的第一行。

你还可以使用 **pydoc** 在本机上启动一个 HTTP 服务器并向来访的 Web 浏览器展示文档。 **python -m pydoc -p 1234** 将在 1234 端口上启动 HTTP 服务器，允许在你所用的 Web 浏览器上通过 `http://localhost:1234/` 来浏览文档。 指定 `0` 作为端口号将任意选择一个未使用的端口。

**python -m pydoc -n <hostname>** 将启动在给定主机名上监听的服务器。 默认的主机名为 'localhost' 但是如果你希望能从其他机器上搜索该服务器，你可能会想要改变服务器所响应的主机名。 在开发阶段此特性会特别有用，如果你想要在一个容器中运行 pydoc 的话。

**python -m pydoc -b** 将启动服务器并额外打开一个 Web 浏览器访问模块索引页。 所发布的每个页面顶端都带有导航栏，你可以点击 _Get_ 来获取特定条目的帮助信息，点击 _Search_ 在所有模块的摘要行中搜索某个关键词，或点击 _Module index_ , _Topics_ 和 _Keywords_ 前往相应的页面。

当 **pydoc** 生成文档内容时，它会使用当前环境和路径来定位模块。 因此，发起调用 **pydoc spam** 得到的文档版本会与你启动 Python 解释器并输入 `import spam` 时得到的模块版本完全相同。

核心模块的模块文档位置对应于 `https://docs.python.org/X.Y/library/` 其中 `X` 和 `Y` 是 Python 解释器的主要版本号和小版本号。 这可通过设置 `PYTHONDOCS` 环境变量来重载为指向不同的 URL 或包含 Library Reference Manual 页面的本地目录。

在 3.2 版本发生变更: 添加 `-b` 选项。

在 3.3 版本发生变更: 命令行选项 `-g` 已经移除。

在 3.4 版本发生变更: `pydoc` 现在会使用 [`inspect.signature()`](inspect.md#inspect.signature "inspect.signature") 而非 [`inspect.getfullargspec()`](inspect.md#inspect.getfullargspec "inspect.getfullargspec") 来从可调用对象中提取签名信息。

在 3.7 版本发生变更: 添加 `-n` 选项。

