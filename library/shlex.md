# `shlex` —— 简单的词法分析¶

**源代码：** [Lib/shlex.py](https://github.com/python/cpython/tree/3.12/Lib/shlex.py)

* * *

`shlex` 类可用于编写类似 Unix shell 的简单词法分析程序。通常可用于编写“迷你语言”（如 Python 应用程序的运行控制文件）或解析带引号的字符串。

`shlex` 模块中定义了以下函数：

shlex.split( _s_ , _comments =False_, _posix =True_)¶

    

~~~
用类似 shell 的语法拆分字符串 _s_ 。如果 _comments_ 为 [`False`](constants.md#False "False") (默认值)，则不会解析给定字符串中的注释 (`commenters` 属性的 `shlex` 实例设为空字符串)。 本函数默认工作于 POSIX 模式下，但若 _posix_ 参数为 False，则采用非 POSIX 模式。

在 3.12 版本发生变更: 传入 `None` 作为 _s_ 参数现在会引发异常，而不是读取 [`sys.stdin`](sys.md#sys.stdin "sys.stdin")。

shlex.join( _split_command_ )¶
~~~
    

~~~
将列表 _split_command_ 中的词法单元（token）串联起来，返回一个字符串。本函数是 `split()` 的逆运算。
~~~
    
    
~~~shell
>>> from shlex import join
>>> print(join(['echo', '-n', 'Multiple words']))
echo -n 'Multiple words'
~~~

为防止注入漏洞，返回值是经过 shell 转义的（参见 `quote()` ）。

在 3.8 版本加入.

shlex.quote( _s_ )¶

    

~~~
返回经过 shell 转义的字符串 _s_ 。返回值为字符串，可以安全地用作 shell 命令行中的词法单元，可用于不能使用列表的场合。

警告

`shlex` 模块 **仅适用于 Unix shell** 。

在不兼容 POSIX 的 shell 或其他操作系统（如Windows）的shell上，并不保证 `quote()` 函数能够正常使用。在这种 shell 中执行用本模块包装过的命令，有可能会存在命令注入漏洞。

请考虑采用命令参数以列表形式给出的函数，比如带了 `shell=False` 参数的 [`subprocess.run()`](subprocess.md#subprocess.run "subprocess.run") 。

以下用法是不安全的：
~~~
    
    
~~~shell
>>> filename = 'somefile; rm -rf ~'
>>> command = 'ls -l {}'.format(filename)
>>> print(command)  # executed by a shell: boom!
ls -l somefile; rm -rf ~
~~~

用 `quote()` 可以堵住这种安全漏洞：

    
    
~~~shell
>>> from shlex import quote
>>> command = 'ls -l {}'.format(quote(filename))
>>> print(command)
ls -l 'somefile; rm -rf ~'
>>> remote_command = 'ssh home {}'.format(quote(command))
>>> print(remote_command)
ssh home 'ls -l '"'"'somefile; rm -rf ~'"'"''
~~~

这种包装方式兼容于 UNIX shell 和 `split()` 。

    
    
~~~shell
>>> from shlex import split
>>> remote_command = split(remote_command)
>>> remote_command
['ssh', 'home', "ls -l 'somefile; rm -rf ~'"]
>>> command = split(remote_command[-1])
>>> command
['ls', '-l', 'somefile; rm -rf ~']
~~~

在 3.3 版本加入.

`shlex` 模块中定义了以下类：

_class _shlex.shlex( _instream =None_, _infile =None_, _posix =False_, _punctuation_chars =False_)¶

    

~~~
`shlex` 及其子类的实例是一种词义分析器对象。 利用初始化参数可指定从哪里读取字符。 初始化参数必须是具备 [`read()`](io.md#io.TextIOBase.read "io.TextIOBase.read") 和 [`readline()`](io.md#io.TextIOBase.readline "io.TextIOBase.readline") 方法的文件/流对象，或者是一个字符串。 如果没有给出初始化参数，则会从 `sys.stdin` 获取输入。 第二个可选参数是个文件名字符串，用于设置 `infile` 属性的初始值。 如果 _instream_ 参数被省略或等于 `sys.stdin`，则第二个参数默认为 "stdin"。 _posix_ 参数定义了操作的模式：若 _posix_ 不为真值（默认），则 `shlex` 实例将工作于兼容模式。 若运行于 POSIX 模式下，则 `shlex` 会尽可能地应用 POSIX shell 解析规则。 _punctuation_chars_ 参数提供了一种使行为更接近于真正的 shell 解析的方式。 该参数可接受多种值：默认值、`False`、保持 Python 3.5 及更早版本的行为。 如果设为 `True`，则会改变对字符 `();<>|&` 的解析方式：这些字符将作为独立的词法单元被返回（视作标点符号）。 如果设为非空字符串，则这些字符将被用作标点符号。 出现在 _punctuation_chars_ 中的 `wordchars` 属性中的任何字符都会从 `wordchars` 中被删除。 请参阅 改进的 shell 兼容性 了解详情。 _punctuation_chars_ 只能在创建 `shlex` 实例时设置，以后不能再作修改。

在 3.6 版本发生变更: 加入 _punctuation_chars_ 参数。

参见

[`configparser`](configparser.md#module-configparser "configparser: Configuration file parser.") 模块
~~~
    

~~~
配置文件解析器，类似于 Windows 的 `.ini` 文件。

## shlex 对象¶

`shlex` 实例具备以下方法：

shlex.get_token()¶
~~~
    

~~~
返回一个词法单元。如果所有单词已用 `push_token()` 堆叠在一起了，则从堆栈中弹出一个词法单元。否则就从输入流中读取一个。如果读取时遇到文件结束符，则会返回 `eof`（在非 POSIX 模式下为空字符串 ``''`，在 POSIX 模式下为 ``None`）。

shlex.push_token( _str_ )¶
~~~
    

~~~
将参数值压入词法单元堆栈。

shlex.read_token()¶
~~~
    

~~~
读取一个原始词法单元。忽略堆栈，且不解释源请求。（通常没什么用，只是为了完整起见。）

shlex.sourcehook( _filename_ )¶
~~~
    

~~~
当 `shlex` 检测到源请求（见下面的 `source`），以下词法单元可作为参数，并应返回一个由文件名和打开的文件对象组成的元组。

通常本方法会先移除参数中的引号。如果结果为绝对路径名，或者之前没有有效的源请求，或者之前的源请求是一个流对象（比如 `sys.stdin`），那么结果将不做处理。否则，如果结果是相对路径名，那么前面将会加上目录部分，目录名来自于源堆栈中前一个文件名（类似于 C 预处理器对 `#include "file.h"` 的处理方式）。

结果被视为一个文件名，并作为元组的第一部分返回，元组的第二部分以此为基础调用 [`open()`](functions.md#open "open") 获得。（注意：这与实例初始化过程中的参数顺序相反！)

此钩子函数是公开的，可用于实现路径搜索、添加文件扩展名或黑入其他命名空间。没有对应的“关闭”钩子函数，但 shlex 实例在返回 EOF 时会调用源输入流的 [`close()`](io.md#io.IOBase.close "io.IOBase.close") 方法。

若要更明确地控制源堆栈，请采用 `push_source()` 和 `pop_source()` 方法。

shlex.push_source( _newstream_ , _newfile =None_)¶
~~~
    

~~~
将输入源流压入输入堆栈。如果指定了文件名参数，以后错误信息中将会用到。`sourcehook()` 内部同样使用了本方法。

shlex.pop_source()¶
~~~
    

~~~
从输入堆栈中弹出最后一条输入源。当遇到输入流的 EOF 时，内部也使用同一方法。

shlex.error_leader( _infile =None_, _lineno =None_)¶
~~~
    

~~~
本方法生成一条错误信息的首部，以 Unix C 编译器错误标签的形式；格式为 `'"%s", line %d: '`，其中 `%s` 被替换为当前源文件的名称，`%d` 被替换为当前输入行号（可用可选参数覆盖）。

这是个快捷函数，旨在鼓励 `shlex` 用户以标准的、可解析的格式生成错误信息，以便 Emacs 和其他 Unix 工具理解。

`shlex` 子类的实例有一些公共实例变量，这些变量可以控制词义分析，也可用于调试。

shlex.commenters¶
~~~
    

~~~
将被视为注释起始字符串。从注释起始字符串到行尾的所有字符都将被忽略。默认情况下只包括 `'#'`。

shlex.wordchars¶
~~~
    

~~~
可连成多字符词法单元的字符串。默认包含所有 ASCII 字母数字和下划线。在 POSIX 模式下，Latin-1 字符集的重音字符也被包括在内。如果 `punctuation_chars` 不为空，则可出现在文件名规范和命令行参数中的 `~-./*?=` 字符也将包含在内，任何 `punctuation_chars` 中的字符将从 `wordchars` 中移除。如果 `whitespace_split` 设为 `True`，则本规则无效。

shlex.whitespace¶
~~~
    

~~~
将被视为空白符并跳过的字符。空白符是词法单元的边界。默认包含空格、制表符、换行符和回车符。

shlex.escape¶
~~~
    

~~~
将视为转义字符。仅适用于 POSIX 模式，默认只包含 `'\'`。

shlex.quotes¶
~~~
    

~~~
将视为引号的字符。词法单元中的字符将会累至再次遇到同样的引号（因此，不同的引号会像在 shell 中一样相互包含。）默认包含 ASCII 单引号和双引号。

shlex.escapedquotes¶
~~~
    

~~~
`quotes` 中的字符将会解析 `escape` 定义的转义字符。这只在 POSIX 模式下使用，默认只包含 `'"'`。

shlex.whitespace_split¶
~~~
    

~~~
若为 `True`，则只根据空白符拆分词法单元。这很有用，比如用 `shlex` 解析命令行，用类似 shell 参数的方式读取各个词法单元。当与 `punctuation_chars` 一起使用时，将根据空白符和这些字符拆分词法单元。

在 3.8 版本发生变更: `punctuation_chars` 属性已与 `whitespace_split` 属性兼容。

shlex.infile¶
~~~
    

~~~
当前输入的文件名，可能是在类实例化时设置的，或者是由后来的源请求堆栈生成的。在构建错误信息时可能会用到本属性。

shlex.instream¶
~~~
    

~~~
`shlex` 实例正从中读取字符的输入流。

shlex.source¶
~~~
    

~~~
本属性默认值为 `None`。 如果给定一个字符串，则会识别为包含请求，类似于各种 shell 中的 `source` 关键字。 也就是说，紧随其后的词法单元将作为文件名打开，作为输入流，直至遇到 EOF 后调用流的 [`close()`](io.md#io.IOBase.close "io.IOBase.close") 方法，然后原输入流仍变回输入源。Source 请求可以在词义堆栈中嵌套任意深度。

shlex.debug¶
~~~
    

~~~
如果本属性为大于 `1` 的数字，则 `shlex` 实例会把动作进度详细地输出出来。若需用到本属性，可阅读源代码来了解细节。

shlex.lineno¶
~~~
    

~~~
源的行数（到目前为止读到的换行符数量加 1）。

shlex.token¶
~~~
    

~~~
词法单元的缓冲区。在捕获异常时可能会用到。

shlex.eof¶
~~~
    

~~~
用于确定文件结束的词法单元。在非 POSIX 模式下，将设为空字符串 `''`，在 POSIX 模式下被设为 `None`。

shlex.punctuation_chars¶
~~~
    

~~~
只读属性。表示应视作标点符号的字符。标点符号将作为单个词法单元返回。然而，请注意不会进行语义有效性检查：比如 “>>>” 可能会作为一个词法单元返回，虽然 shell 可能无法识别。

在 3.6 版本加入.

## 解析规则¶

在非 POSIX 模式下时，`shlex` 会试图遵守以下规则：

  * 不识别单词中的引号（`Do"Not"Separate` 解析为一个单词 `Do"Not"Separate`）；

  * 不识别转义字符；

  * 引号包裹的字符保留字面意思；

  * 成对的引号会将单词分离（`"Do"Separate` 解析为 `"Do"` 和 `Separate`）；

  * 如果 `whitespace_split` 为 `False`，则未声明为单词字符、空白或引号的字符将作为单字符的词法单元返回。若为 `True`， 则 `shlex` 只根据空白符拆分单词。

  * EOF 用空字符串（`''`）表示；

  * 空字符串无法解析，即便是加了引号。

在 POSIX 模式时，`shlex` 将尝试遵守以下解析规则：

  * 引号会被剔除，且不会拆分单词（ `"Do"Not"Separate"` 将解析为单个单词 `DoNotSeparate`）；

  * 未加引号包裹的转义字符（如 `'\'` ）保留后一个字符的字面意思；

  * 引号中的字符不属于 `escapedquotes` （例如，`"'"`），则保留引号中所有字符的字面值；

  * 若引号包裹的字符属于 `escapedquotes` （例如 `'"'`），则保留引号中所有字符的字面意思，属于 `escape` 中的字符除外。仅当后跟后半个引号或转义字符本身时，转义字符才保留其特殊含义。否则，转义字符将视作普通字符；

  * EOF 用 [`None`](constants.md#None "None") 表示；

  * 允许出现引号包裹的空字符串（`''`）。

## 改进的 shell 兼容性¶

在 3.6 版本加入.

`shlex` 类提供了与常见 Unix shell（如 `bash`、 `dash` 和 `sh`）的解析兼容性。为了充分利用这种兼容性，请在构造函数中设定 `punctuation_chars` 参数。该参数默认为 `False`，维持 3.6 以下版本的行为。如果设为 `True`，则会改变对 `();<>|&` 字符的解析方式：这些字符都将视为单个的词法单元返回。虽然不算是完整的 shell 解析程序（考虑到 shell 的多样性，超出了标准库的范围），但确实能比其他方式更容易进行命令行的处理。以下代码段演示了两者的差异：
~~~
    
    
~~~shell
 >>> import shlex
 >>> text = "a && b; c && d || e; f >'abc'; (def \"ghi\")"
 >>> s = shlex.shlex(text, posix=True)
 >>> s.whitespace_split = True
 >>> list(s)
 ['a', '&&', 'b;', 'c', '&&', 'd', '||', 'e;', 'f', '>abc;', '(def', 'ghi)']
 >>> s = shlex.shlex(text, posix=True, punctuation_chars=True)
 >>> s.whitespace_split = True
 >>> list(s)
 ['a', '&&', 'b', ';', 'c', '&&', 'd', '||', 'e', ';', 'f', '>', 'abc', ';',
 '(', 'def', 'ghi', ')']
~~~

当然，返回的词法单元对 shell 无效，需要对返回的词法单元自行进行错误检查。

punctuation_chars 参数可以不传入 `True` ，而是传入包含特定字符的字符串，用于确定由哪些字符构成标点符号。例如：

    
    
~~~shell
>>> import shlex
>>> s = shlex.shlex("a && b || c", punctuation_chars="|")
>>> list(s)
['a', '&', '&', 'b', '||', 'c']
~~~

备注

如果指定了 `punctuation_chars`，则 `wordchars` 属性的参数会是 `~-./*?=`。因为这些字符可以出现在文件名（包括通配符）和命令行参数中（如 `--color=auto`）。因此：

    
    
~~~shell
>>> import shlex
>>> s = shlex.shlex('~/a && b-c --color=auto || d *.py?',
...                 punctuation_chars=True)
>>> list(s)
['~/a', '&&', 'b-c', '--color=auto', '||', 'd', '*.py?']
~~~

不过为了尽可能接近于 shell ，建议在使用 `punctuation_chars` 时始终使用 `posix` 和 `whitespace_split` ，这将完全否定 `wordchars` 。

为了达到最佳效果，`punctuation_chars` 应与 `posix=True` 一起设置。（注意 `posix=False` 是 `shlex` 的默认设置)。

