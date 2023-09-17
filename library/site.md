# `site` —— 指定域的配置钩子¶

**源代码:** [Lib/site.py](https://github.com/python/cpython/tree/3.12/Lib/site.py)

* * *

**这个模块将在初始化时被自动导入。** 此自动导入可以通过使用解释器的 [`-S`](../using/cmdline.md#cmdoption-S) 选项来屏蔽。

导入此模块将会附加域特定的路径到模块搜索路径并且添加一些内建对象，除非使用了 [`-S`](../using/cmdline.md#cmdoption-S) 选项。 那样的话，模块可以被安全地导入，而不会对模块搜索路径和内建对象有自动的修改或添加。要明确地触发通常域特定的添加，调用函数 `site.main()`。

在 3.3 版本发生变更: 在之前即便使用了 [`-S`](../using/cmdline.md#cmdoption-S)，导入此模块仍然会触发路径操纵。

它会从头部和尾部构建至多四个目录作为起点。 对于头部，它会使用 `sys.prefix` 和 `sys.exec_prefix`；空的头部会被跳过。 对于尾部，它会使用空字符串然后是 `lib/site-packages` (在 Windows 上) 或 `lib/python _X.Y_ /site-packages` (在 Unix 和 macOS 上)。 对于每个不同的头-尾组合，它会查看其是否指向现有的目录，如果是的话，则将其添加到 `sys.path` 并且检查新添加目录中的配置文件。

在 3.5 版本发生变更: 对 "site-python" 目录的支持已被移除。

如果名为 "pyvenv.cfg" 的文件存在于 sys.executable 之上的一个目录中，则 sys.prefix 和 sys.exec_prefix 将被设置为该目录，并且还会检查 site-packages （ sys.base_prefix 和 sys.base_exec_prefix 始终是 Python 安装的 "真实" 前缀）。 如果 "pyvenv.cfg" （引导程序配置文件）包含设置为非 "true"（不区分大小写）的 "include-system-site-packages" 键，则不会在系统级前缀中搜索 site-packages；反之则会。

一个路径配置文件是具有 `_name_.pth` 命名格式的文件，并且存在上面提到的四个目录之一中；它的内容是要添加到 `sys.path` 中的额外项目（每行一个）。不存在的项目不会添加到 `sys.path`，并且不会检查项目指向的是目录还是文件。项目不会被添加到 `sys.path` 超过一次。空行和由 `#` 起始的行会被跳过。以 `import` 开始的行（跟着空格或 TAB）会被执行。

备注

每次启动 Python，在 `.pth` 文件中的可执行行都将会被运行，而不管特定的模块实际上是否需要被使用。 因此，其影响应降至最低。可执行行的主要预期目的是使相关模块可导入（加载第三方导入钩子，调整 `PATH` 等）。如果它发生了，任何其他的初始化都应当在模块实际导入之前完成。将代码块限制为一行是一种有意采取的措施，不鼓励在此处放置更复杂的内容。

例如，假设 `sys.prefix` 和 `sys.exec_prefix` 已经被设置为 `/usr/local`。 Python X.Y 的库之后被安装为 `/usr/local/lib/python _X.Y_`。假设有一个拥有三个孙目录 `foo`, `bar` 和 `spam` 的子目录 `/usr/local/lib/python _X.Y_ /site-packages`，并且有两个路径配置文件 `foot.pth` 和 `bar.pth`。假定 `foo.pth` 内容如下:

    
    
~~~
# foo package configuration

foo
bar
bletch
~~~

并且 `bar.pth` 包含:

    
    
~~~
# bar package configuration

bar
~~~

则下面特定版目录将以如下顺序被添加到 `sys.path`。

    
    
~~~
/usr/local/lib/pythonX.Y/site-packages/bar
/usr/local/lib/pythonX.Y/site-packages/foo
~~~

请注意 `bletch` 已被省略因为它并不存在；`bar` 目前在 `foo` 目录之前因为 `bar.pth` 按字母顺序排在 `foo.pth` 之前；而 `spam` 已被省略因为它在两个路径配置文件中都未被提及。

在这些路径操作之后，会尝试导入一个名为 `sitecustomize` 的模块，它可以执行任意站点专属的定制。 它通常是由系统管理员在 site-packages 目录下创建的。 如此此导入失败并引发 [`ImportError`](exceptions.md#ImportError "ImportError") 或其子类异常，并且异常的 `name` 属性等于 `'sitecustomize'`，则它会被静默地忽略。 如果 Python 是在没有可用输出流的情况下启动的，例如在 Windows 上使用 `pythonw.exe` (它默认被用于启动 start IDLE)，则来自 `sitecustomize` 的输出尝试会被忽略。 任何其他异常都会导致静默且可能令人迷惑不解的进程失败。

在此之后，会尝试导入一个名为 `usercustomize` 的模块，它可以执行任意用户专属的定制，如果 `ENABLE_USER_SITE` 为真值的话。 这个文件应该在用户的 site-packages 目录中创建（见下文），该目录是 `sys.path` 的组成部分，除非被 [`-s`](../using/cmdline.md#cmdoption-s) 所禁用。 如果此导入失败并引发 [`ImportError`](exceptions.md#ImportError "ImportError") 或者其子类异常，并且异常的 `name` 属性等于 `'usercustomize'`，它会被静默地忽略。

请注意对于某些非 Unix 系统来说，`sys.prefix` 和 `sys.exec_prefix` 均为空值，并且路径操作会被跳过；但是仍然会尝试导入 `sitecustomize` 和 `usercustomize`。

## Readline 配置¶

在支持 [`readline`](readline.md#module-readline "readline: GNU readline support for Python. \(Unix\)") 的系统上，这个模块也将导入并配置 [`rlcompleter`](rlcompleter.md#module-rlcompleter "rlcompleter: Python identifier completion, suitable for the GNU readline library.") 模块，如果 Python 是以 [交互模式](../tutorial/interpreter.md#tut-interactive) 启动并且不带 [`-S`](../using/cmdline.md#cmdoption-S) 选项的话。 默认的行为是启用 tab 键补全并使用 `~/.python_history` 作为历史存档文件。 要禁用它，请删除（或重载）你的 `sitecustomize` 或 `usercustomize` 模块或 [`PYTHONSTARTUP`](../using/cmdline.md#envvar-PYTHONSTARTUP) 文件中的 [`sys.__interactivehook__`](sys.md#sys.__interactivehook__ "sys.__interactivehook__") 属性。

在 3.4 版本发生变更: rlcompleter 和 history 会被自动激活。

## 模块内容¶

site.PREFIXES¶

    

~~~
site-packages 目录的前缀列表。

site.ENABLE_USER_SITE¶
~~~
    

~~~
显示用户 site-packages 目录状态的旗标。 `True` 意味着它被启用并被添加到 `sys.path`。 `False` 意味着它按照用户请求被禁用 (通过 [`-s`](../using/cmdline.md#cmdoption-s) 或 [`PYTHONNOUSERSITE`](../using/cmdline.md#envvar-PYTHONNOUSERSITE))。 `None` 意味着它因安全理由（user 或 group id 和 effective id 之间不匹配）或是被管理员所禁用。

site.USER_SITE¶
~~~
    

~~~
正在运行的 Python 的用户级 site-packages 的路径。 它可以为 `None`，如果 `getusersitepackages()` 尚未被调用的话。 默认值在 UNIX 和非框架 macOS 编译版上为 `~/.local/lib/python _X.Y_ /site-packages`，在 macOS 框架编译版上为 `~/Library/Python/ _X.Y_ /lib/python/site-packages`，而在 Windows 上则为 `_%APPDATA%_ \Python\Python _XY_ \site-packages`。 此目录属于站点目录，这意味着其中的 `.pth` 文件将会被处理。

site.USER_BASE¶
~~~
    

~~~
用户级 site-packages 的基准目录的路径。 如果 `getuserbase()` 尚未被调用则它可以为 `None`。 默认值在 UNIX 和 macOS 非框架编译版上为 `~/.local`，在 macOS 框架编译版上为 `~/Library/Python/ _X.Y_`，而在 Windows 上则为 `_%APPDATA%_ \Python`。 这个值会被用于计算脚本、数据文件和 Python 模块等的安装目录。 对于用户安装方案。 另请参阅 [`PYTHONUSERBASE`](../using/cmdline.md#envvar-PYTHONUSERBASE)。

site.main()¶
~~~
    

~~~
将所有的标准站点专属目录添加到模块搜索路径。 这个函数会在导入此模块时被自动调用，除非 Python 解释器启动时附带了 [`-S`](../using/cmdline.md#cmdoption-S) 旗标。

在 3.3 版本发生变更: 这个函数使用无条件调用。

site.addsitedir( _sitedir_ , _known_paths =None_)¶
~~~
    

~~~
将一个目录添加到 sys.path 并处理其 `.pth` 文件。 通常被用于 `sitecustomize` 或 `usercustomize` (见下文)。

site.getsitepackages()¶
~~~
    

~~~
返回包含所有全局 site-packages 目录的列表。

在 3.2 版本加入.

site.getuserbase()¶
~~~
    

~~~
返回用户基准目录的路径 `USER_BASE`。 如果它尚未被初始化，则此函数还将参照 [`PYTHONUSERBASE`](../using/cmdline.md#envvar-PYTHONUSERBASE) 来设置它。

在 3.2 版本加入.

site.getusersitepackages()¶
~~~
    

~~~
返回用户专属 site-packages 目录的路径 `USER_SITE`。 如果它尚未被初始化，则此函数还将参照 `USER_BASE` 来设置它。 要确定用户专属 site-packages 是否已被添加到 `sys.path` 则应当使用 `ENABLE_USER_SITE`。

在 3.2 版本加入.

## 命令行界面¶

`site` 模块还提供了一个从命令行获取用户目录的方式:
~~~
    
    
~~~
$ python -m site --user-site
/home/user/.local/lib/python3.11/site-packages
~~~

如果它被不带参数地调用，它将在标准输出打印 [`sys.path`](sys.md#sys.path "sys.path") 的内容，再打印 `USER_BASE` 的值以及该目录是否存在，然后打印 `USER_SITE` 的相应信息，最后打印 `ENABLE_USER_SITE` 的值。

\--user-base¶

    

~~~
输出用户基本的路径。

\--user-site¶
~~~
    

~~~
