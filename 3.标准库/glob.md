# `glob` \--- Unix 风格路径名模式扩展¶

**源代码:** [Lib/glob.py](https://github.com/python/cpython/tree/3.12/Lib/glob.py)

* * *

`glob` 模块会按照 Unix shell 所使用的规则找出所有匹配特定模式的路径名称，但返回结果的顺序是不确定的。 波浪号扩展不会生效，但 `*`, `?` 以及用 `[]` 表示的字符范围将被正确地匹配。 这是通过配合使用 [`os.scandir()`](os.md#os.scandir "os.scandir") 和 [`fnmatch.fnmatch()`](fnmatch.md#fnmatch.fnmatch "fnmatch.fnmatch") 函数来实现的，而不是通过实际发起调用子 shell。

请注意以点号 (`.`) 打头的文件只能用同样以点号打头的模式来匹配，这不同于 [`fnmatch.fnmatch()`](fnmatch.md#fnmatch.fnmatch "fnmatch.fnmatch") 或 [`pathlib.Path.glob()`](pathlib.md#pathlib.Path.glob "pathlib.Path.glob")。 （对于波浪号和 shell 变量扩展，请使用 [`os.path.expanduser()`](os.path.md#os.path.expanduser "os.path.expanduser") 和 [`os.path.expandvars()`](os.path.md#os.path.expandvars "os.path.expandvars")。）

对于字面值匹配，请将原字符用方括号括起来。 例如，`'[?]'` 将匹配字符 `'?'`。

参见

[`pathlib`](pathlib.md#module-pathlib "pathlib: Object-oriented filesystem paths") 模块提供高级路径对象。

glob.glob( _pathname_ , _*_ , _root_dir =None_, _dir_fd =None_, _recursive =False_, _include_hidden =False_)¶

    

~~~
返回一个匹配 _pathname_ 的可能为空的路径名列表，其中的元素必须为包含路径信息的字符串。 _pathname_ 可以是绝对路径 (如 `/usr/src/Python-1.5/Makefile`) 或相对路径 (如 `../../Tools/*/*.gif`)，并可包含 shell 风格的通配符。 无效的符号链接也将包括在结果中 (如像在 shell 中一样)。 结果是否排序取决于具体文件系统。 如果某个符合条件的文件在调用此函数期间被移除或添加，是否包括该文件的路径是没有规定的。

如果 _root_dir_ 不为 `None`，则它应当是指明要搜索的根目录的 [path-like object](../glossary.md#term-path-like-object)。 它用在 `glob()` 上与在调用它之前改变当前目录有相同的效果。 如果 _pathname_ 为相对路径，结果将包含相对于 _root_dir_ 的路径。

本函数带有 _dir_fd_ 参数，支持 [基于目录描述符的相对路径](os.md#dir-fd)。

如果 _recursive_ 为真值，则模式 "`**`" 将匹配目录中的任何文件以及零个或多个目录、子目录和符号链接。 如果模式加了一个 [`os.sep`](os.md#os.sep "os.sep") 或 [`os.altsep`](os.md#os.altsep "os.altsep") 则将不匹配文件。

如果 _include_hidden_ 为真值，"`**`" 模式将匹配隐藏目录。

引发一个 [审计事件](sys.md#auditing) `glob.glob` 附带参数 `pathname`, `recursive`。

引发一个 [审计事件](sys.md#auditing) `glob.glob/2`，附带参数 `pathname`, `recursive`, `root_dir`, `dir_fd`。

备注

在一个较大的目录树中使用 "`**`" 模式可能会消耗非常多的时间。

在 3.5 版本发生变更: 支持使用 "`**`" 的递归 glob。

在 3.10 版本发生变更: 添加了 _root_dir_ 和 _dir_fd_ 形参。

在 3.11 版本发生变更: 增加了 _include_hidden_ 形参。

glob.iglob( _pathname_ , _*_ , _root_dir =None_, _dir_fd =None_, _recursive =False_, _include_hidden =False_)¶
~~~
    

~~~
返回一个 [iterator](../glossary.md#term-iterator)，它会产生与 `glob()` 相同的结果，但不会实际地同时保存它们。

引发一个 [审计事件](sys.md#auditing) `glob.glob` 附带参数 `pathname`, `recursive`。

引发一个 [审计事件](sys.md#auditing) `glob.glob/2`，附带参数 `pathname`, `recursive`, `root_dir`, `dir_fd`。

在 3.5 版本发生变更: 支持使用 "`**`" 的递归 glob。

在 3.10 版本发生变更: 添加了 _root_dir_ 和 _dir_fd_ 形参。

在 3.11 版本发生变更: 增加了 _include_hidden_ 形参。

glob.escape( _pathname_ )¶
~~~
    

~~~
转义所有特殊字符 (`'?'`, `'*'` 和 `'['`)。 这适用于当你想要匹配可能带有特殊字符的任意字符串字面值的情况。 在 drive/UNC 共享点中的特殊字符不会被转义，例如在 Windows 上 `escape('//?/c:/Quo vadis?.txt')` 将返回 `'//?/c:/Quo vadis[?].txt'`。

在 3.4 版本加入.

例如，考虑一个包含以下内容的目录：文件 `1.gif`, `2.txt`, `card.gif` 以及一个子目录 `sub` 其中只包含一个文件 `3.txt`. `glob()` 将产生如下结果。 请注意路径的任何开头部分都将被保留。:
~~~
    
    
~~~shell
>>> import glob
>>> glob.glob('./[0-9].*')
['./1.gif', './2.txt']
>>> glob.glob('*.gif')
['1.gif', 'card.gif']
>>> glob.glob('?.gif')
['1.gif']
>>> glob.glob('**/*.txt', recursive=True)
['2.txt', 'sub/3.txt']
>>> glob.glob('./**/', recursive=True)
['./', './sub/']
~~~

如果目录包含以 `.` 打头的文件，它们默认将不会被匹配。 例如，考虑一个包含 `card.gif` 和 `.card.gif` 的目录:

    
    
~~~shell
>>> import glob
>>> glob.glob('*.gif')
['card.gif']
>>> glob.glob('.c*')
['.card.gif']
~~~

参见

模块 [`fnmatch`](fnmatch.md#module-fnmatch "fnmatch: Unix shell style filename pattern matching.")

    

~~~
