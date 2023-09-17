# `fileinput` \--- 迭代来自多个输入流的行¶

**源代码:** [Lib/fileinput.py](https://github.com/python/cpython/tree/3.12/Lib/fileinput.py)

* * *

此模块实现了一个辅助类和一些函数用来快速编写访问标准输入或文件列表的循环。 如果你只想要读写一个文件请参阅 [`open()`](functions.md#open "open")。

典型用法为:

    
    
~~~
import fileinput
for line in fileinput.input(encoding="utf-8"):
    process(line)
~~~

此程序会迭代 `sys.argv[1:]` 中列出的所有文件内的行，如果列表为空则会使用 `sys.stdin`。 如果有一个文件名为 `'-'`，它也会被替换为 `sys.stdin` 并且可选参数 _mode_ 和 _openhook_ 会被忽略。 要指定替代文件列表，请将其作为第一个参数传给 `input()`。 也允许使用单个文件。

所有文件都默认以文本模式打开，但你可以通过在调用 `input()` 或 `FileInput` 时指定 _mode_ 形参来重载此行为。 如果在打开或读取文件时发生了 I/O 错误，将会引发 [`OSError`](exceptions.md#OSError "OSError")。

在 3.3 版本发生变更: 原来会引发 [`IOError`](exceptions.md#IOError "IOError")；现在它是 [`OSError`](exceptions.md#OSError "OSError") 的别名。

如果 `sys.stdin` 被使用超过一次，则第二次之后的使用将不返回任何行，除非是被交互式的使用，或都是被显式地重置 (例如使用 `sys.stdin.seek(0)`)。

空文件打开后将立即被关闭；它们在文件列表中会被注意到的唯一情况只有当最后打开的文件为空的时候。

反回的行不会对换行符做任何处理，这意味着文件中的最后一行可能不带换行符。

你可以通过将 _openhook_ 形参传给 `fileinput.input()` 或 `FileInput()` 来提供一个打开钩子以便控制文件的打开方式。 此钩子必须为一个函数，它接受两个参数 _filename_ 和 _mode_ ，并返回一个以相应模式打开的文件类对象。 如果指定了 _encoding_ 和/或 _errors_ ，它们将作为额外的关键字参数被传给这个钩子。 此模块提供了一个 `hook_compressed()` 来支持压缩文件。

以下函数是此模块的初始接口：

fileinput.input( _files =None_, _inplace =False_, _backup =''_, _*_ , _mode ='r'_, _openhook =None_, _encoding =None_, _errors =None_)¶

    

~~~
创建一个 `FileInput` 类的实例。 该实例将被用作此模块中函数的全局状态，并且还将在迭代期间被返回使用。 此函数的形参将被继续传递给 `FileInput` 类的构造器。

`FileInput` 实例可以在 [`with`](../reference/compound_stmts.md#with) 语句中被用作上下文管理器。 在这个例子中， _input_ 在 `with` 语句结束后将会被关闭，即使发生了异常也是如此:
~~~
    
    
~~~
with fileinput.input(files=('spam.txt', 'eggs.txt'), encoding="utf-8") as f:
    for line in f:
        process(line)
~~~

在 3.2 版本发生变更: 可以被用作上下文管理器。

在 3.8 版本发生变更: 关键字形参 _mode_ 和 _openhook_ 现在是仅限关键字形参。

在 3.10 版本发生变更: 增加了仅限关键字形参 _encoding_ 和 _errors_ 。

下列函数会使用 `fileinput.input()` 所创建的全局状态；如果没有活动的状态，则会引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。

fileinput.filename()¶

    

~~~
返回当前被读取的文件名。 在第一行被读取之前，返回 `None`。

fileinput.fileno()¶
~~~
    

~~~
返回以整数表示的当前文件“文件描述符”。 当未打开文件时（处在第一行和文件之间），返回 `-1`。

fileinput.lineno()¶
~~~
    

~~~
返回已被读取的累计行号。 在第一行被读取之前，返回 `0`。 在最后一个文件的最后一行被读取之后，返回该行的行号。

fileinput.filelineno()¶
~~~
    

~~~
返回当前文件中的行号。 在第一行被读取之前，返回 `0`。 在最后一个文件的最后一行被读取之后，返回此文件中该行的行号。

fileinput.isfirstline()¶
~~~
    

~~~
如果刚读取的行是其所在文件的第一行则返回 `True`，否则返回 `False`。

fileinput.isstdin()¶
~~~
    

~~~
如果最后读取的行来自 `sys.stdin` 则返回 `True`，否则返回 `False`。

fileinput.nextfile()¶
~~~
    

~~~
关闭当前文件以使下次迭代将从下一个文件（如果存在）读取第一行；不是从该文件读取的行将不会被计入累计行数。 直到下一个文件的第一行被读取之后文件名才会改变。 在第一行被读取之前，此函数将不会生效；它不能被用来跳过第一个文件。 在最后一个文件的最后一行被读取之后，此函数将不再生效。

fileinput.close()¶
~~~
    

~~~
关闭序列。

此模块所提供的实现了序列行为的类同样也可用于子类化：

_class _fileinput.FileInput( _files =None_, _inplace =False_, _backup =''_, _*_ , _mode ='r'_, _openhook =None_, _encoding =None_, _errors =None_)¶
~~~
    

~~~
类 `FileInput` 是具体的实现；它的方法 `filename()`, `fileno()`, `lineno()`, `filelineno()`, `isfirstline()`, `isstdin()`, `nextfile()` 和 `close()` 对应于此萨夫尼克具有相同名称的函数。 此外它还是一个 [iterable](../glossary.md#term-iterable) 并且具有可返回下一个输入行的 [`readline()`](io.md#io.TextIOBase.readline "io.TextIOBase.readline") 方法。 此序列必须以严格的序列顺序来访问；随机访问和 [`readline()`](io.md#io.TextIOBase.readline "io.TextIOBase.readline") 不可被混用。

通过 _mode_ 你可以指定要传给 [`open()`](functions.md#open "open") 的文件模式。 它必须为 `'r'` 和 `'rb'` 中的一个。

_openhook_ 如果给出则必须为一个函数，它接受两个参数 _filename_ 和 _mode_ ，并相应地返回一个打开的文件类对象。 你不能同时使用 _inplace_ 和 _openhook_ 。

你可以指定 _encoding_ 和 _errors_ 来将其传给 [`open()`](functions.md#open "open") 或 _openhook_ 。

`FileInput` 实例可以在 [`with`](../reference/compound_stmts.md#with) 语句中被用作上下文管理器。 在这个例子中， _input_ 在 `with` 语句结束后将会被关闭，即使发生了异常也是如此:
~~~
    
    
~~~
with FileInput(files=('spam.txt', 'eggs.txt')) as input:
    process(input)
~~~

在 3.2 版本发生变更: 可以被用作上下文管理器。

在 3.8 版本发生变更: 关键字形参 _mode_ 和 _openhook_ 现在是仅限关键字形参。

在 3.10 版本发生变更: 增加了仅限关键字形参 _encoding_ 和 _errors_ 。

在 3.11 版本发生变更: `'rU'` 和 `'U'` 模式以及 `__getitem__()` 方法已被移除。

**可选的原地过滤:** 如果传递了关键字参数 `inplace=True` 给 `fileinput.input()` 或 `FileInput` 构造器，则文件会被移至备份文件并将标准输出定向到输入文件（如果已存在与备份文件同名的文件，它将被静默地替换）。 这使得编写一个能够原地重写其输入文件的过滤器成为可能。 如果给出了 _backup_ 形参 (通常形式为 `backup='.<some extension>'`)，它将指定备份文件的扩展名，并且备份文件会被保留；默认情况下扩展名为 `'.bak'` 并且它会在输出文件关闭时被删除。 在读取标准输入时原地过滤会被禁用。

此模块提供了以下两种打开文件钩子：

fileinput.hook_compressed( _filename_ , _mode_ , _*_ , _encoding =None_, _errors =None_)¶

    

~~~
使用 [`gzip`](gzip.md#module-gzip "gzip: Interfaces for gzip compression and decompression using file objects.") 和 [`bz2`](bz2.md#module-bz2 "bz2: Interfaces for bzip2 compression and decompression.") 模块透明地打开 gzip 和 bzip2 压缩的文件（通过扩展名 `'.gz'` 和 `'.bz2'` 来识别）。 如果文件扩展名不是 `'.gz'` 或 `'.bz2'`，文件会以正常方式打开（即使用 [`open()`](functions.md#open "open") 并且不带任何解压操作）。

_encoding_ 和 _errors_ 值会被传给 [`io.TextIOWrapper`](io.md#io.TextIOWrapper "io.TextIOWrapper") 用于压缩文件以及打开普通文件。

用法示例: `fi = fileinput.FileInput(openhook=fileinput.hook_compressed, encoding="utf-8")`

在 3.10 版本发生变更: 增加了仅限关键字形参 _encoding_ 和 _errors_ 。

fileinput.hook_encoded( _encoding_ , _errors =None_)¶
~~~
    

~~~
