# `doctest` \--- 测试交互性的Python示例¶

** 源代码 ** [Lib/doctest.py](https://github.com/python/cpython/tree/3.12/Lib/doctest.py)

* * *

`doctest` 模块寻找像Python交互式代码的文本，然后执行这些代码来确保它们的确就像展示的那样正确运行，有许多方法来使用doctest：

  * 通过验证所有交互式示例仍然按照记录的方式工作，以此来检查模块的文档字符串是否是最新的。

  * 通过验证来自一个测试文件或一个测试对象的交互式示例按预期工作，来进行回归测试。

  * 为一个包写指导性的文档，用输入输出的例子来说明。 取决于是强调例子还是说明性的文字，这有一种 "文本测试 "或 "可执行文档 "的风格。

下面是一个小却完整的示例模块:

    
    
~~~
"""
This is the "example" module.

The example module supplies one function, factorial().  For example,

>>> factorial(5)
120
"""

def factorial(n):
    """Return the factorial of n, an exact integer >= 0.

    >>> [factorial(n) for n in range(6)]
    [1, 1, 2, 6, 24, 120]
    >>> factorial(30)
    265252859812191058636308480000000
    >>> factorial(-1)
    Traceback (most recent call last):
        ...
    ValueError: n must be >= 0

    Factorials of floats are OK, but the float must be an exact integer:
    >>> factorial(30.1)
    Traceback (most recent call last):
        ...
    ValueError: n must be exact integer
    >>> factorial(30.0)
    265252859812191058636308480000000

    It must also not be ridiculously large:
    >>> factorial(1e100)
    Traceback (most recent call last):
        ...
    OverflowError: n too large
    """

    import math
    if not n >= 0:
        raise ValueError("n must be >= 0")
    if math.floor(n) != n:
        raise ValueError("n must be exact integer")
    if n+1 == n:  # catch a value like 1e300
        raise OverflowError("n too large")
    result = 1
    factor = 2
    while factor <= n:
        result *= factor
        factor += 1
    return result
~~~
    
    if __name__ == "__main__":
        import doctest
        doctest.testmod()
    

如果你直接在命令行里运行 `example.py` ， `doctest` 将发挥它的作用。

    
    
~~~
$ python example.py
$
~~~

没有输出！ 这很正常，这意味着所有的例子都成功了。 把 `-v` 传给脚本，`doctest` 会打印出它所尝试的详细日志，并在最后打印出一个总结。

    
    
~~~
$ python example.py -v
Trying:
    factorial(5)
Expecting:
    120
ok
Trying:
    [factorial(n) for n in range(6)]
Expecting:
    [1, 1, 2, 6, 24, 120]
ok
~~~

以此类推，最终以：

    
    
~~~
Trying:
    factorial(1e100)
Expecting:
    Traceback (most recent call last):
        ...
    OverflowError: n too large
ok
2 items passed all tests:
   1 tests in __main__
   8 tests in __main__.factorial
9 tests in 2 items.
9 passed and 0 failed.
Test passed.
$
~~~

这就是对于高效地使用 `doctest` 你所需要知道的一切！开始上手吧。 下面的章节提供了完整的细节。 请注意，在标准的 Python 测试套件和库中有许多 doctest 的例子。特别有用的例子可以在标准测试文件 `Lib/test/test_doctest.py` 中找到。

## 简单用法：检查Docstrings中的示例¶

开始使用doctest的最简单方法（但不一定是你将继续这样做的方式）是结束每个模块 `M` 使用:

    
    
~~~
if __name__ == "__main__":
    import doctest
    doctest.testmod()
~~~

`doctest` 会随后检查模块 `M` 中的文档字符串。

以脚本形式运行该模块会使文档中的例子得到执行和验证:

    
    
~~~
python M.py
~~~

这不会显示任何东西，除非一个例子失败了，在这种情况下，失败的例子和失败的原因会被打印到stdout，最后一行的输出是 `***Test Failed*** N failures.`，其中*N*是失败的例子的数量。

用 `-v` 来运行它来切换，而不是:

    
    
~~~
python M.py -v
~~~

并将所有尝试过的例子的详细报告打印到标准输出，最后还有各种总结。

你可以通过向 `testmod()` 传递 `verbose=True` 来强制执行 verbose 模式，或者通过传递 `verbose=False` 来禁止它。 在这两种情况下，`sys.argv` 都不会被 `testmod()` 检查（所以传递 `-v` 或不传递都没有影响）。

还有一个命令行快捷方式用于运行 `testmod()`。 你可以指示Python解释器直接从标准库中运行doctest模块，并在命令行中传递模块名称:

    
    
~~~
python -m doctest -v example.py
~~~

这将导入 `example.py` 作为一个独立的模块，并对其运行 `testmod()`。 注意，如果该文件是一个包的一部分，并且从该包中导入了其他子模块，这可能无法正确工作。

关于 `testmod()` 的更多信息，请参见 基本API 部分。

## 简单的用法：检查文本文件中的例子¶

doctest 的另一个简单应用是测试文本文件中的交互式例子。 这可以用 `testfile()` 函数来完成;:

    
    
~~~
import doctest
doctest.testfile("example.txt")
~~~

这个简短的脚本执行并验证文件 `example.txt` 中包含的任何交互式 Python 示例。该文件的内容被当作一个巨大的文档串来处理；该文件不需要包含一个Python程序！例如，也许 `example.txt` 包含以下内容:

    
    
~~~
The ``example`` module
======================

Using ``factorial``
-------------------

This is an example text file in reStructuredText format.  First import
``factorial`` from the ``example`` module:

    >>> from example import factorial

Now use it:

    >>> factorial(6)
    120
~~~

运行 `doctest.testfile("example.txt")`，然后发现这个文档中的错误:

    
    
~~~
File "./example.txt", line 14, in example.txt
Failed example:
    factorial(6)
Expected:
    120
Got:
    720
~~~

与 `testmod()` 一样， `testfile()` 不会显示任何东西，除非一个例子失败。 如果一个例子失败了，那么失败的例子和失败的原因将被打印到stdout，使用的格式与 `testmod()` 相同。

默认情况下，`testfile()` 在调用模块的目录中寻找文件。参见章节 基本API，了解可用于告诉它在其他位置寻找文件的可选参数的描述。

像 `testmod()` 一样，`testfile()` 的详细程度可以通过命令行 `-v` 切换或可选的关键字参数 _verbose_ 来设置。

还有一个命令行快捷方式用于运行 `testfile()`。 你可以指示Python解释器直接从标准库中运行doctest模块，并在命令行中传递文件名:

    
    
~~~
python -m doctest -v example.txt
~~~

因为文件名没有以 `.py` 结尾，`doctest` 推断它必须用 `testfile()` 运行，而不是 `testmod()`。

关于 `testfile()` 的更多信息，请参见 基本API 一节。

## 它是如何工作的¶

这一节详细研究了doctest的工作原理：它查看哪些文档串，它如何找到交互式的用例，它使用什么执行环境，它如何处理异常，以及如何用选项标志来控制其行为。这是你写doctest例子所需要知道的信息；关于在这些例子上实际运行doctest的信息，请看下面的章节。

### 哪些文件串被检查了？¶

模块的文档串以及所有函数、类和方法的文档串都将被搜索。 导入模块的对象不被搜索。

此外，如果 `M.__test__` 存在并且 "为真值"，则它必须是一个字典，其中每个条目都将一个（字符串）名称映射到一个函数对象、类对象或字符串。 从 `M.__test__` 找到的函数和类对象的文档字符串会被搜索，而字符串会被当作文档字符串来处理。 在输出时，每个键 `K` 在 `M.__test__` 中都显示为其名称

    
    
~~~
<name of M>.__test__.K
~~~

任何发现的类都会以类似的方式进行递归搜索，以测试其包含的方法和嵌套类中的文档串。

### 文档串的例子是如何被识别的？¶

在大多数情况下，对交互式控制台会话的复制和粘贴功能工作得很好，但是 doctest 并不试图对任何特定的 Python shell 进行精确的模拟。

    
    
~~~shell
>>> # comments are ignored
>>> x = 12
>>> x
12
>>> if x == 13:
...     print("yes")
... else:
...     print("no")
...     print("NO")
...     print("NO!!!")
...
no
NO
NO!!!
>>>
~~~

任何预期的输出必须紧随包含代码的最后 `'>>> '` 或 `'... '` 行，预期的输出（如果有的话）延伸到下一 `'>>> '` 行或全空白行。

fine输出：

  * 预期输出不能包含一个全白的行，因为这样的行被认为是预期输出的结束信号。 如果预期的输出包含一个空行，在你的测试例子中，在每一个预期有空行的地方加上 `<BLANKLINE>`。

  * 所有硬制表符都被扩展为空格，使用 8 列的制表符。由测试代码生成的输出中的制表符不会被修改。 因为样本输出中的任何硬制表符都会被扩展，这意味着如果代码输出包括硬制表符，文档测试通过的唯一方法是 `NORMALIZE_WHITESPACE` 选项或者 指令 是有效的。 另外，测试可以被重写，以捕获输出并将其与预期值进行比较，作为测试的一部分。这种对源码中标签的处理是通过试错得出的，并被证明是最不容易出错的处理方式。通过编写一个自定义的 `DocTestParser` 类，可以使用一个不同的算法来处理标签。

  * 向stdout的输出被捕获，但不向stderr输出（异常回溯通过不同的方式被捕获）。

  * 如果你在交互式会话中通过反斜线续行，或出于任何其他原因使用反斜线，你应该使用原始文件串，它将完全保留你输入的反斜线:
    
        >>> def f(x):
    ...     r'''Backslashes in a raw docstring: m\n'''
    ...
    >>> print(f.__doc__)
    Backslashes in a raw docstring: m\n
    

否则，反斜杠将被解释为字符串的一部分。例如，上面的 `\n` 会被解释为一个换行符。 另外，你可以在doctest版本中把每个反斜杠加倍（而不使用原始字符串）:

    
        >>> def f(x):
    ...     '''Backslashes in a raw docstring: m\\n'''
    ...
    >>> print(f.__doc__)
    Backslashes in a raw docstring: m\n
    

  * 起始列并不重要:
    
        >>> assert "Easy!"
          >>> import math
              >>> math.floor(1.9)
              1
    

并从预期的输出中剥离出与开始该例子的初始 `'>>> '` 行中出现的同样多的前导空白字符。

### 什么是执行上下文？¶

默认情况下，每次 `doctest` 找到要测试的文档串时，它都会使用 `M` 的*浅层副本*，这样运行测试就不会改变模块的真正全局变量，而且 `M` 的一个测试也不会留下临时变量，从而意外地让另一个测试通过。这意味着例子可以自由地使用 `M` 中的任何顶级定义的名字，以及正在运行的文档串中早期定义的名字。用例不能看到其他文档串中定义的名字。

你可以通过将 `globs=your_dict` 传递给 `testmod()` 或 `testfile()` 来强制使用你自己的dict作为执行环境。

### 异常如何处理？¶

没问题，只要回溯是这个例子产生的唯一输出：只要粘贴回溯即可。[1] 由于回溯所包含的细节可能会迅速变化（例如，确切的文件路径和行号），这是doctest努力使其接受的内容具有灵活性的一种情况。

简单实例:

    
    
~~~shell
>>> [1, 2, 3].remove(42)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: 42 is not in list
~~~

That doctest succeeds if [`ValueError`](exceptions.md#ValueError "ValueError") is raised, with the `42 is not in list` detail as shown.

异常的预期输出必须以回溯头开始，可以是以下两行中的任何一行，缩进程度与例子中的第一行相同:

    
    
~~~
Traceback (most recent call last):
Traceback (innermost last):
~~~

回溯头的后面是一个可选的回溯堆栈，其内容被doctest忽略。 回溯堆栈通常是省略的，或者从交互式会话中逐字复制的。

回溯堆栈的后面是最有用的部分：包含异常类型和细节的一行（几行）。 这通常是回溯的最后一行，但如果异常有多行细节，则可以延伸到多行:

    
    
~~~shell
>>> raise ValueError('multi\n    line\ndetail')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: multi
    line
detail
~~~

最后三行（以 [`ValueError`](exceptions.md#ValueError "ValueError") 开头）将与异常的类型和细节进行比较，其余的被忽略。

最佳实践是省略回溯栈，除非它为这个例子增加了重要的文档价值。 因此，最后一个例子可能更好，因为:

    
    
~~~shell
>>> raise ValueError('multi\n    line\ndetail')
Traceback (most recent call last):
    ...
ValueError: multi
    line
detail
~~~

请注意，回溯的处理方式非常特别。 特别是，在重写的例子中，`...` 的使用与 doctest 的 `ELLIPSIS` 选项无关。 该例子中的省略号可以不写，也可以是三个（或三百个）逗号或数字，或者是一个缩进的 Monty Python 短剧的剧本。

有些细节你应该读一遍，但不需要记住：

  * Doctest 不能猜测你的预期输出是来自异常回溯还是来自普通打印。 因此，例如，一个期望 `ValueError: 42 is prime` 的用例将通过测试，无论 [`ValueError`](exceptions.md#ValueError "ValueError") 是真的被触发，或者该用例只是打印了该回溯文本。 在实践中，普通输出很少以回溯标题行开始，所以这不会产生真正的问题。

  * 回溯堆栈的每一行（如果有的话）必须比例子的第一行缩进， _或者_ 以一个非字母数字的字符开始。回溯头之后的第一行缩进程度相同，并且以字母数字开始，被认为是异常细节的开始。当然，这对真正的回溯来说是正确的事情。

  * 当 `IGNORE_EXCEPTION_DETAIL` doctest 选项被指定时，最左边的冒号后面的所有内容以及异常名称中的任何模块信息都被忽略。

  * 交互式 shell 省略了一些 [`SyntaxError`](exceptions.md#SyntaxError "SyntaxError") 的回溯头行。但 doctest 使用回溯头行来区分异常和非异常。所以在罕见的情况下，如果你需要测试一个省略了回溯头的 [`SyntaxError`](exceptions.md#SyntaxError "SyntaxError")，你将需要手动添加回溯头行到你的测试用例中。

  * 对于某些异常，Python 会使用 `^` 标记和波浪号来显示错误位置:
    
        >>> 1 + None
      File "<stdin>", line 1
        1 + None
        ~~^~~~~~
    TypeError: unsupported operand type(s) for +: 'int' and 'NoneType'
    

由于显示错误位置的行在异常类型和细节之前，它们不被doctest检查。 例如，下面的测试会通过，尽管它把 `^` 标记放在了错误的位置:

    
        >>> 1 + None
      File "<stdin>", line 1
        1 + None
        ^~~~~~~~
    TypeError: unsupported operand type(s) for +: 'int' and 'NoneType'
    

### 选项标记¶

一系列选项旗标控制着 doctest 的各方面行为。 旗标的符号名称以模块常量的形式提供，可以一起 [bitwise ORed](../reference/expressions.md#bitwise) 并传递给各种函数。 这些名称也可以在 doctest directives 中使用，并且可以通过 `-o` 选项传递给 doctest 命令行接口。

在 3.4 版本加入: 命令行选项 `-o` 。

第一组选项定义了测试语义，控制doctest如何决定实际输出是否与用例的预期输出相匹配方面的问题。

doctest.DONT_ACCEPT_TRUE_FOR_1¶

    

~~~
默认情况下，如果一个预期的输出块只包含 `1`，那么实际的输出块只包含 `1` 或只包含 `True` 就被认为是匹配的，同样，`0` 与 `False` 也是如此。 当 `DONT_ACCEPT_TRUE_FOR_1` 被指定时，两种替换都不允许。 默认行为是为了适应 Python 将许多函数的返回类型从整数改为布尔值；期望 "小整数" 输出的测试在这些情况下仍然有效。 这个选项可能会消失，但不会在几年内消失。

doctest.DONT_ACCEPT_BLANKLINE¶
~~~
    

~~~
默认情况下，如果一个预期输出块包含一个只包含字符串 `<BLANKLINE>` 的行，那么该行将与实际输出中的一个空行相匹配。 因为一个真正的空行是对预期输出的限定，这是传达预期空行的唯一方法。 当 `DONT_ACCEPT_BLANKLINE` 被指定时，这种替换是不允许的。

doctest.NORMALIZE_WHITESPACE¶
~~~
    

~~~
当指定时，所有的空白序列（空白和换行）都被视为相等。预期输出中的任何空白序列将与实际输出中的任何空白序列匹配。默认情况下，空白必须完全匹配。 `NORMALIZE_WHITESPACE` 在预期输出非常长的一行，而你想把它包在源代码的多行中时特别有用。

doctest.ELLIPSIS¶
~~~
    

~~~
当指定时，预期输出中的省略号（ `...` ）可以匹配实际输出中的任何子串。这包括跨行的子串和空子串，所以最好保持简单的用法。复杂的用法会导致与 `.*` 在正则表达式中容易出现的 "oops, it matched too much!"相同的意外情况。

doctest.IGNORE_EXCEPTION_DETAIL¶
~~~
    

~~~
当被指定时，只要有预期类型的异常被引发 doctests 就会预期异常测试通过，即使细节（消息和完整限定名称）并不匹配。

举例来说，一个预期 `ValueError: 42` 的用例在实际引发的异常为 `ValueError: 3*14` 将会通过，但是如果是引发 [`TypeError`](exceptions.md#TypeError "TypeError") 则将会失败。 它也将忽略任何包括在异常类前面的完整限定名称，该名称在所使用的不同 Python 版本和代码/库中可能会不同。 因此，以下三种形式对于指定的旗标均有效:
~~~
    
    
~~~shell
>>> raise Exception('message')
Traceback (most recent call last):
Exception: message

>>> raise Exception('message')
Traceback (most recent call last):
builtins.Exception: message

>>> raise Exception('message')
Traceback (most recent call last):
__main__.Exception: message
~~~

请注意 `ELLIPSIS` 也可以被用来忽略异常消息中的细节，但这样的测试仍然可能根据特定模块名称是否存在或是否完全匹配而失败。

在 3.2 版本发生变更: `IGNORE_EXCEPTION_DETAIL` 现在也忽略了与包含被测异常的模块有关的任何信息。

doctest.SKIP¶

    

~~~
当指定时，完全不运行这个用例。 这在doctest用例既是文档又是测试案例的情况下很有用，一个例子应该包括在文档中，但不应该被检查。例如，这个例子的输出可能是随机的；或者这个例子可能依赖于测试驱动程序所不能使用的资源。

SKIP标志也可用于临时 "注释" 用例。

doctest.COMPARISON_FLAGS¶
~~~
    

~~~
一个比特或运算将上述所有的比较标志放在一起。

第二组选项控制测试失败的报告方式：

doctest.REPORT_UDIFF¶
~~~
    

~~~
当指定时，涉及多行预期和实际输出的故障将使用统一的差异来显示。

doctest.REPORT_CDIFF¶
~~~
    

~~~
当指定时，涉及多行预期和实际输出的故障将使用上下文差异来显示。

doctest.REPORT_NDIFF¶
~~~
    

~~~
当指定时，差异由 `difflib.Differ` 来计算，使用与流行的:file:ndiff.py`工具相同的算法。这是唯一一种标记行内和行间差异的方法。 例如，如果一行预期输出包含数字 ``1` ，而实际输出包含字母 `l` ，那么就会插入一行，用圆点标记不匹配的列位置。

doctest.REPORT_ONLY_FIRST_FAILURE¶
~~~
    

~~~
当指定时，在每个 doctest 中显示第一个失败的用例，但隐藏所有其余用例的输出。 这将防止 doctest 报告由于先前的失败而中断的正确用例；但也可能隐藏独立于第一个失败的不正确用例。 当 `REPORT_ONLY_FIRST_FAILURE` 被指定时，其余的用例仍然被运行，并且仍然计入报告的失败总数；只是输出被隐藏了。

doctest.FAIL_FAST¶
~~~
    

~~~
当指定时，在第一个失败的用例后退出，不尝试运行其余的用例。因此，报告的失败次数最多为1。这个标志在调试时可能很有用，因为第一个失败后的用例甚至不会产生调试输出。

doctest 命令行接受选项 `-f` 作为 `-o FAIL_FAST` 的简洁形式。

在 3.4 版本加入.

doctest.REPORTING_FLAGS¶
~~~
    

~~~
一个比特或操作将上述所有的报告标志组合在一起。

还有一种方法可以注册新的选项标志名称，不过这并不有用，除非你打算通过子类来扩展 `doctest` 内部。

doctest.register_optionflag( _name_ )¶
~~~
    

~~~
用给定的名称创建一个新的选项标志，并返回新标志的整数值。 `register_optionflag()` 可以在继承 `OutputChecker` 或 `DocTestRunner` 时使用，以创建子类支持的新选项。 `register_optionflag()` 应始终使用以下方式调用:
~~~
    
    
~~~
MY_FLAG = register_optionflag('MY_FLAG')
~~~

### 指令¶

Doctest指令可以用来修改单个例子的 option flags 。 Doctest指令是在一个用例的源代码后面的特殊Python注释。

    
    
~~~
**directive** ::=  "#" "doctest:" directive_options
**directive_options** ::=  directive_option ("," directive_option)*
**directive_option** ::=  on_or_off directive_option_name
**on_or_off** ::=  "+" | "-"
**directive_option_name** ::=  "DONT_ACCEPT_BLANKLINE" | "NORMALIZE_WHITESPACE" | ...
~~~

`+` 或 `-` 与指令选项名称之间不允许有空格。 指令选项名称可以是上面解释的任何一个选项标志名称。

一个用例的 doctest 指令可以修改 doctest 对该用例的行为。 使用 `+` 来启用指定的行为，或者使用 `-` 来禁用它。

例如，这个测试将会通过:

    
    
~~~shell
>>> print(list(range(20)))  # doctest: +NORMALIZE_WHITESPACE
[0,   1,  2,  3,  4,  5,  6,  7,  8,  9,
10,  11, 12, 13, 14, 15, 16, 17, 18, 19]
~~~

如果没有这个指令则它将失败，因为实际的输出在只有个位数的列表元素前没有两个空格，也因为实际的输出是单行的。 这个测试也会通过，并且也需要一个指令来完成:

    
    
~~~shell
>>> print(list(range(20)))  # doctest: +ELLIPSIS
[0, 1, ..., 18, 19]
~~~

在单个物理行中可以使用多条指令，以逗号分隔:

    
    
~~~shell
>>> print(list(range(20)))  # doctest: +ELLIPSIS, +NORMALIZE_WHITESPACE
[0,    1, ...,   18,    19]
~~~

如果在单个用例中使用了多条指令注释，则它们会被合并:

    
    
~~~shell
>>> print(list(range(20)))  # doctest: +ELLIPSIS
...                         # doctest: +NORMALIZE_WHITESPACE
[0,    1, ...,   18,    19]
~~~

正如前面的例子所示，你可以在你的用例中添加只包含指令的行 `...`。 当一个用例太长以至于不能方便地放到同一行时这将会很有用:

    
    
~~~shell
>>> print(list(range(5)) + list(range(10, 20)) + list(range(30, 40)))
... # doctest: +ELLIPSIS
[0, ..., 4, 10, ..., 19, 30, ..., 39]
~~~

请注意，由于所有的选项都是默认禁用的，而指令只适用于它们出现的用例，所以启用选项 (通过指令中的 `+`) 通常是唯一有意义的选择。 然而，选项标志也可以被传递给运行测试的函数，建立不同的默认值。 在这种情况下，通过指令中的 `-` 来禁用一个选项可能是有用的。

### 警告¶

`doctest` 是严格地要求在预期输出中完全匹配。 如果哪怕只有一个字符不匹配，测试就会失败。 这可能会让你吃惊几次，在你确切地了解到 Python 对输出的保证和不保证之前。 例如，当打印一个集合时，Python 不保证元素以任何特定的顺序被打印出来，所以像:

    
    
~~~shell
>>> foo()
{"Hermione", "Harry"}
~~~

是不可靠的！一个变通方法是用:

    
    
~~~shell
>>> foo() == {"Hermione", "Harry"}
True
~~~

来取代。另一个是使用

    
    
~~~shell
>>> d = sorted(foo())
>>> d
['Harry', 'Hermione']
~~~

还有其他的问题，但你会明白的。

另一个不好的做法是打印嵌入了对象地址的值，例如

    
    
~~~shell
>>> id(1.0)  # certain to fail some of the time  
7948648
>>> class C: pass
>>> C()  # the default repr() for instances embeds an address   
<C object at 0x00AC18F0>
~~~

`ELLIPSIS` 指令为之前的例子提供了一个不错的方案:

    
    
~~~shell
>>> C()  # doctest: +ELLIPSIS
<C object at 0x...>
~~~

浮点数在不同的平台上也会有小的输出变化，因为Python在浮点数的格式化上依赖于平台的C库，而C库在这个问题上的质量差异很大。:

    
    
~~~shell
>>> 1./7  # risky
0.14285714285714285
>>> print(1./7) # safer
0.142857142857
>>> print(round(1./7, 6)) # much safer
0.142857
~~~

形式 `I/2.**J` 的数字在所有的平台上都是安全的，我经常设计一些测试的用例来产生该形式的数:

    
    
~~~shell
>>> 3./4  # utterly safe
0.75
~~~

简单的分数也更容易让人理解，这也使得文件更加完善。

## 基本API¶

函数 `testmod()` 和 `testfile()` 为 doctest 提供了一个简单的接口，应该足以满足大多数基本用途。关于这两个函数的不太正式的介绍，请参见 简单用法：检查Docstrings中的示例 和 简单的用法：检查文本文件中的例子 部分。

doctest.testfile( _filename_ , _module_relative =True_, _name =None_, _package =None_, _globs =None_, _verbose =None_, _report =True_, _optionflags =0_, _extraglobs =None_, _raise_on_error =False_, _parser =DocTestParser()_, _encoding =None_)¶

    

~~~
除了*filename*，所有的参数都是可选的，而且应该以关键字的形式指定。

测试名为 _filename_ 的文件中的用例。 返回 `(failure_count, test_count)`。

可选参数 _module_relative_ 指定了文件名的解释方式。

  * 如果 _module_relative_ 是 `True` (默认)，那么 _filename_ 指定一个独立于操作系统的模块相对路径。 默认情况下，这个路径是相对于调用模块的目录的；但是如果指定了 _package_ 参数，那么它就是相对于该包的。 为了保证操作系统的独立性， _filename_ 应该使用字符来分隔路径段，并且不能是一个绝对路径 (即不能以 `/` 开始)。

  * 如果 _module_relative_ 是 `False`，那么 _filename_ 指定了一个操作系统特定的路径。路径可以是绝对的，也可以是相对的；相对路径是相对于当前工作目录而言的。

可选参数 _name_ 给出了测试的名称；默认情况下，或者如果是 `None`，那么使用 `os.path.basename(filename)`。

可选参数 _package_ 是一个 Python 包或一个 Python 包的名字，其目录应被用作模块相关文件名的基础目录。 如果没有指定包，那么调用模块的目录将作为模块相关文件名的基础目录。如果 _module_relative_ 是 `False`，那么指定 _package_ 是错误的。

可选参数 _globs_ 给出了一个在执行示例时用作全局变量的dict。 这个dict的一个新的浅层副本将为doctest创建，因此它的用例将从一个干净的地方开始。默认情况下，或者如果为 `None`，使用一个新的空dict。

可选参数 _extraglobs_ 给出了一个合并到用于执行用例全局变量中的dict。 这就像 [`dict.update()`](stdtypes.md#dict.update "dict.update") 一样：如果 _globs_ 和 _extraglobs_ 有一个共同的键，那么 _extraglobs_ 中的相关值会出现在合并的dict中。 默认情况下，或者为 `None` ，则不使用额外的全局变量。这是一个高级功能，允许对 doctest 进行参数化。例如，可以为一个基类写一个测试，使用该类的通用名称，然后通过传递一个 _extraglobs_ dict，将通用名称映射到要测试的子类，从而重复用于测试任何数量的子类。

可选的参数 _verbose_ 如果为真值会打印很多东西，如果为假值则只打印失败信息；默认情况下，或者为 `None`，只有当 `'-v'` 在 `sys.argv` 中时才为真值。

可选参数 _report_ 为True时，在结尾处打印一个总结，否则在结尾处什么都不打印。 在verbose模式下，总结是详细的，否则总结是非常简短的（事实上，如果所有的测试都通过了，总结就是空的）。

可选参数 _optionflags_ （默认值为0）是选项标志的 [bitwise OR](../reference/expressions.md#bitwise) 。参见章节 选项标记 。

可选参数 _raise_on_error_ 默认为False。 如果是True，在一个用例中第一次出现失败或意外的异常时，会触发一个异常。这允许对失败进行事后调试。默认行为是继续运行例子。

可选参数 _parser_ 指定一个 `DocTestParser` （或子类），它应该被用来从文件中提取测试。它默认为一个普通的解析器（即 `DocTestParser()`）。

可选参数 _encoding_ 指定了一个编码，应该用来将文件转换为unicode。

doctest.testmod( _m =None_, _name =None_, _globs =None_, _verbose =None_, _report =True_, _optionflags =0_, _extraglobs =None_, _raise_on_error =False_, _exclude_empty =False_)¶
~~~
    

~~~
所有的参数都是可选的，除了 _m_ 之外，都应该以关键字的形式指定。

测试从模块 _m_ （或模块 [`__main__`](__main__.md#module-__main__ "__main__: The environment where top-level code is run. Covers command-line interfaces, import-time behavior, and ``__name__ == '__main__'``.") ，如果 _m_ 没有被提供或为 `None` ）可达到的函数和类的文档串中的用例，从 `m.__doc__` 开始。

也测试从dict `m.__test__` 可达到的用例，如果它存在并且不是 `None` 。 `m.__test__` 将名字（字符串）映射到函数、类和字符串；函数和类的文档串被搜索到的用例；字符串被直接搜索到，就像它们是文档串一样。

只搜索附属于模块 _m_ 中的对象的文档串。

返回 `(failure_count, test_count)` 。

可选参数 _name_ 给出了模块的名称；默认情况下，或者如果为 `None` ，则为 `m.__name__` 。

可选参数 _exclude_empty_ 默认为假值。 如果为真值，没有找到任何 doctest 的对象将被排除在考虑范围之外。 默认情况下是向后兼容，所以仍然使用 `doctest.master.summarize()` 和 `testmod()` 的代码会继续得到没有测试的对象的输出。 较新的 `DocTestFinder` 构造器的 _exclude_empty_ 参数默认为真值。

可选参数 _extraglobs_ 、 _verbose_ 、 _report_ 、 _optionflags_ 、 _raise_on_error_ 和 _globs_ 与上述函数 `testfile()` 的参数相同，只是 _globs_ 默认为 `m.__dict__` 。

doctest.run_docstring_examples( _f_ , _globs_ , _verbose =False_, _name ='NoName'_, _compileflags =None_, _optionflags =0_)¶
~~~
    

~~~
与对象 _f_ 相关的测试用例；例如， _f_ 可以是一个字符串、一个模块、一个函数或一个类对象。

dict 参数 _globs_ 的浅层拷贝被用于执行环境。

可选参数 _name_ 在失败信息中使用，默认为 `"NoName"` 。

如果可选参数 _verbose_ 为真，即使没有失败也会产生输出。 默认情况下，只有在用例失败的情况下才会产生输出。

可选参数 _compileflags_ 给出了Python编译器在运行例子时应该使用的标志集。默认情况下，或者如果为 `None` ，标志是根据 _globs_ 中发现的未来特征集推导出来的。

可选参数 _optionflags_ 的作用与上述 `testfile()` 函数中的相同。

## Unittest API¶

`doctest` 提供了两个函数，可以用来从模块和包含测试的文本文件中创建 [`unittest`](unittest.md#module-unittest "unittest: Unit testing framework for Python.") 测试套件。 要与 [`unittest`](unittest.md#module-unittest "unittest: Unit testing framework for Python.") 测试发现集成，请在你的测试模块中包含一个 `load_tests()` 函数：:
~~~
    
    
~~~
import unittest
import doctest
import my_module_with_doctests

def load_tests(loader, tests, ignore):
    tests.addTests(doctest.DocTestSuite(my_module_with_doctests))
    return tests
~~~

有两个主要函数用于从文本文件和带doctest的模块中创建 [`unittest.TestSuite`](unittest.md#unittest.TestSuite "unittest.TestSuite") 实例。

doctest.DocFileSuite( _* paths_, _module_relative =True_, _package =None_, _setUp =None_, _tearDown =None_, _globs =None_, _optionflags =0_, _parser =DocTestParser()_, _encoding =None_)¶

    

~~~
将一个或多个文本文件中的doctest测试转换为一个 [`unittest.TestSuite`](unittest.md#unittest.TestSuite "unittest.TestSuite") 。

返回的 [`unittest.TestSuite`](unittest.md#unittest.TestSuite "unittest.TestSuite") 将由 unittest 框架运行，并运行每个文件中的交互式示例。如果任何文件中的用例失败了，那么合成的单元测试就会失败，并触发一个 `failureException` 异常，显示包含该测试的文件名和一个（有时是近似的）行号。

传递一个或多个要检查的文本文件的路径（作为字符串）。

选项可以作为关键字参数提供：

可选参数 _module_relative_ 指定了 _paths_ 中的文件名应该如何解释。

  * 如果*module_relative*是 `True` （默认值），那么 _paths_ 中的每个文件名都指定了一个独立于操作系统的模块相对路径。 默认情况下，这个路径是相对于调用模块的目录的；但是如果指定了 _package_ 参数，那么它就是相对于该包的。 为了保证操作系统的独立性，每个文件名都应该使用字符来分隔路径段，并且不能是绝对路径（即不能以 `/` 开始）。

  * 如果 _module_relative_ 是 `False` ，那么 _paths_ 中的每个文件名都指定了一个操作系统特定的路径。 路径可以是绝对的，也可以是相对的；相对路径是关于当前工作目录的解析。

可选参数 _package_ 是一个Python包或一个Python包的名字，其目录应该被用作 _paths_ 中模块相关文件名的基本目录。 如果没有指定包，那么调用模块的目录将作为模块相关文件名的基础目录。 如果 _module_relative_ 是 `False` ，那么指定 _package_ 是错误的。

可选的参数 _setUp_ 为测试套件指定了一个设置函数。在运行每个文件中的测试之前，它被调用。 _setUp_ 函数将被传递给一个 `DocTest` 对象。 setUp函数可以通过测试的 _globs_ 属性访问测试的全局变量。

可选的参数 _tearDown_ 指定了测试套件的卸载函数。 在运行每个文件中的测试后，它会被调用。 _tearDown_ 函数将被传递一个 `DocTest` 对象。 setUp函数可以通过测试的 _globs_ 属性访问测试的全局变量。

可选参数 _globs_ 是一个包含测试的初始全局变量的字典。 这个字典的一个新副本为每个测试创建。 默认情况下， _globs_ 是一个新的空字典。

可选参数 _optionflags_ 为测试指定默认的doctest选项，通过将各个选项的标志连接在一起创建。 参见章节 选项标记 。参见下面的函数 `set_unittest_reportflags()` ，以了解设置报告选项的更好方法。

可选参数 _parser_ 指定一个 `DocTestParser` （或子类），它应该被用来从文件中提取测试。它默认为一个普通的解析器（即 `DocTestParser()`）。

可选参数 _encoding_ 指定了一个编码，应该用来将文件转换为unicode。

该全局 `__file__` 被添加到提供给用 `DocFileSuite()` 从文本文件加载的doctest的全局变量中。

doctest.DocTestSuite( _module =None_, _globs =None_, _extraglobs =None_, _test_finder =None_, _setUp =None_, _tearDown =None_, _optionflags =0_, _checker =None_)¶
~~~
    

~~~
将一个模块的doctest测试转换为 [`unittest.TestSuite`](unittest.md#unittest.TestSuite "unittest.TestSuite") 。

返回的 [`unittest.TestSuite`](unittest.md#unittest.TestSuite "unittest.TestSuite") 将由 unittest 框架运行，并运行模块中的每个 doctest。 如果任何一个测试失败，那么合成的单元测试就会失败，并触发一个 `failureException` 异常，显示包含该测试的文件名和一个（有时是近似的）行号。

可选参数 _module_ 提供了要测试的模块。 它可以是一个模块对象或一个（可能是带点的）模块名称。 如果没有指定，则使用调用此函数的模块。

可选参数 _globs_ 是一个包含测试的初始全局变量的字典。 这个字典的一个新副本为每个测试创建。 默认情况下， _globs_ 是一个新的空字典。

可选参数 _extraglobs_ 指定了一组额外的全局变量，这些变量被合并到 _globs_ 中。 默认情况下，不使用额外的全局变量。

可选参数 _test_finder_ 是 `DocTestFinder` 对象（或一个可替换的对象），用于从模块中提取测试。

可选参数 _setUp_ 、 _tearDown_ 和 _optionflags_ 与上述函数 `DocFileSuite()` 相同。

这个函数使用与 `testmod()` 相同的搜索技术。

在 3.5 版本发生变更: 如果 _module_ 不包含任何文件串，则 `DocTestSuite()` 返回一个空的 [`unittest.TestSuite`](unittest.md#unittest.TestSuite "unittest.TestSuite")，而不是触发 [`ValueError`](exceptions.md#ValueError "ValueError")。

`DocTestSuite()` 从 `doctest.DocTestCase` 实例中创建一个 [`unittest.TestSuite`](unittest.md#unittest.TestSuite "unittest.TestSuite") ，而 `DocTestCase` 是 [`unittest.TestCase`](unittest.md#unittest.TestCase "unittest.TestCase") 的子类。 `DocTestCase` 在这里没有记录（这是一个内部细节），但研究其代码可以回答关于 [`unittest`](unittest.md#module-unittest "unittest: Unit testing framework for Python.") 集成的实际细节问题。

同样， `DocFileSuite()` 从 `doctest.DocFileCase` 实例中创建一个 [`unittest.TestSuite`](unittest.md#unittest.TestSuite "unittest.TestSuite") ，并且 `DocFileCase` 是 `DocTestCase` 的一个子类。

所以这两种创建 [`unittest.TestSuite`](unittest.md#unittest.TestSuite "unittest.TestSuite") 的方式都是运行 `DocTestCase` 的实例。这一点很重要，因为有一个微妙的原因：当你自己运行 `doctest` 函数时，你可以直接控制使用中的 `doctest` 选项，通过传递选项标志给 `doctest` 函数。 然而，如果你正在编写一个 [`unittest`](unittest.md#module-unittest "unittest: Unit testing framework for Python.") 框架， [`unittest`](unittest.md#module-unittest "unittest: Unit testing framework for Python.") 最终会控制测试的运行时间和方式。 框架作者通常希望控制 `doctest` 的报告选项（也许，例如，由命令行选项指定），但没有办法通过 [`unittest`](unittest.md#module-unittest "unittest: Unit testing framework for Python.") 向 `doctest` 测试运行者传递选项。

出于这个原因， `doctest` 也支持一个概念，即 `doctest` 报告特定于 [`unittest`](unittest.md#module-unittest "unittest: Unit testing framework for Python.") 支持的标志，通过这个函数：

doctest.set_unittest_reportflags( _flags_ )¶
~~~
    

~~~
设置要使用的 `doctest` 报告标志。

参数 _flags_ 是选项标志的 [bitwise OR](../reference/expressions.md#bitwise) 。 参见章节 选项标记 。 只有 "报告标志" 可以被使用。

这是一个模块的全局设置，并影响所有未来由模块 [`unittest`](unittest.md#module-unittest "unittest: Unit testing framework for Python.") 运行的测试： `DocTestCase` 的 `runTest()` 方法会查看 `DocTestCase` 实例构建时为测试用例指定的选项标志。 如果没有指定报告标志（这是典型的和预期的情况）， `doctest` 的 [`unittest`](unittest.md#module-unittest "unittest: Unit testing framework for Python.") 报告标志被 [bitwise ORed](../reference/expressions.md#bitwise) 到选项标志中，这样增加的选项标志被传递给 `DocTestRunner` 实例来运行doctest。 如果在构建 `DocTestCase` 实例时指定了任何报告标志，那么 `doctest` 的 [`unittest`](unittest.md#module-unittest "unittest: Unit testing framework for Python.") 报告标志会被忽略。

[`unittest`](unittest.md#module-unittest "unittest: Unit testing framework for Python.") 报告标志的值在调用该函数之前是有效的，由该函数返回。

## 高级 API¶

基本 API 是一个简单的封装，旨在使 doctest 易于使用。它相当灵活，应该能满足大多数用户的需求；但是，如果你需要对测试进行更精细的控制，或者希望扩展 doctest 的功能，那么你应该使用高级 API 。

高级API围绕着两个容器类，用于存储从 doctest 案例中提取的交互式用例:

  * `Example`: 一个单一的 Python [statement](../glossary.md#term-statement) ，与它的预期输出配对。

  * `DocTest`: 一组 `Example`s 的集合，通常从一个文档字符串或文本文件中提取。

定义了额外的处理类来寻找、解析和运行，并检查 doctest 的用例。

  * `DocTestFinder` : 查找给定模块中的所有文档串，并使用 `DocTestParser` 从每个包含交互式用例的文档串中创建一个 `DocTest` 。

  * `DocTestParser` : 从一个字符串（如一个对象的文档串）创建一个 `DocTest` 对象。

  * `DocTestRunner` : 执行 `DocTest` 中的用例，并使用 `OutputChecker` 来验证其输出。

  * `OutputChecker` : 将一个测试用例的实际输出与预期输出进行比较，并决定它们是否匹配。

这些处理类之间的关系总结在下图中:
~~~
    
    
~~~
                            list of:
+------+                   +---------+
|module| --DocTestFinder-> | DocTest | --DocTestRunner-> results
+------+    |        ^     +---------+     |       ^    (printed)
            |        |     | Example |     |       |
            v        |     |   ...   |     v       |
           DocTestParser   | Example |   OutputChecker
                           +---------+
~~~

### DocTest 对象¶

_class _doctest.DocTest( _examples_ , _globs_ , _name_ , _filename_ , _lineno_ , _docstring_ )¶

    

~~~
应该在单一命名空间中运行的doctest用例的集合。构造函数参数被用来初始化相同名称的属性。

`DocTest` 定义了以下属性。 它们由构造函数初始化，不应该被直接修改。

examples¶
~~~
    

~~~
一个 `Example` 对象的列表，它编码了应该由该测试运行的单个交互式 Python 用例。

globs¶
~~~
    

~~~
例子应该运行的命名空间（又称 globals ）。这是一个将名字映射到数值的字典。例子对名字空间的任何改变（比如绑定新的变量）将在测试运行后反映在 `globs` 中。

name¶
~~~
    

~~~
识别 `DocTest` 的字符串名称。 通常情况下，这是从测试中提取的对象或文件的名称。

filename¶
~~~
    

~~~
这个 `DocTest` 被提取的文件名；或者为 `None`，如果文件名未知，或者 `DocTest` 没有从文件中提取。

lineno¶
~~~
    

~~~
`filename` 中的行号，这个 `DocTest` 开始的地方，或者行号不可用时为 `None`。 这个行号相对于文件的开头来说是零的。

docstring¶
~~~
    

~~~
从测试中提取的字符串，或者如果字符串不可用，或者为 `None` ，如果测试没有从字符串中提取。

### Example 对象¶

_class _doctest.Example( _source_ , _want_ , _exc_msg =None_, _lineno =0_, _indent =0_, _options =None_)¶
~~~
    

~~~
单个交互式用例，由一个 Python 语句及其预期输出组成。 构造函数参数被用来初始化相同名称的属性。

`Example` 定义了以下属性。 它们由构造函数初始化，不应该被直接修改。

source¶
~~~
    

~~~
一个包含该用例源码的字符串。 源码由一个 Python 语句组成，并且总是以换行结束；构造函数在必要时添加一个换行。

want¶
~~~
    

~~~
运行这个用例的源码的预期输出（可以是 stdout ，也可以是异常情况下的回溯）。 `want` 以一个换行符结束，除非没有预期的输出，在这种情况下它是一个空字符串。 构造函数在必要时添加一个换行。

exc_msg¶
~~~
    

~~~
用例产生的异常信息，如果这个例子被期望产生一个异常；或者为 `None` ，如果它不被期望产生一个异常。 这个异常信息与 [`traceback.format_exception_only()`](traceback.md#traceback.format_exception_only "traceback.format_exception_only") 的返回值进行比较。 `exc_msg` 以换行结束，除非是 `None` 。

lineno¶
~~~
    

~~~
包含本例的字符串中的行号，即本例的开始。 这个行号相对于包含字符串的开头来说是以零开始的。

indent¶
~~~
    

~~~
用例在包含字符串中的缩进，即在用例的第一个提示前有多少个空格字符。

options¶
~~~
    

~~~
一个从选项标志到 `True` 或 `False` 的字典映射，用于覆盖这个例子的默认选项。 任何不包含在这个字典中的选项标志都被保留为默认值（由 `DocTestRunner` 的 `optionflags` 指定）。默认情况下，没有选项被设置。

### DocTestFinder 对象¶

_class _doctest.DocTestFinder( _verbose =False_, _parser =DocTestParser()_, _recurse =True_, _exclude_empty =True_)¶
~~~
    

~~~
一个处理类，用于从一个给定的对象的 docstring 和其包含的对象的 docstring 中提取与之相关的 `DocTest` 。 `DocTest` 可以从模块、类、函数、方法、静态方法、类方法和属性中提取。

可选的参数 _verbose_ 可以用来显示查找器搜索到的对象。 它的默认值是 `False` （无输出）。

可选的参数 _parser_ 指定了 `DocTestParser` 对象（或一个可替换的对象），用于从文档串中提取 doctest 。

如果可选的参数 _recurse_ 是 False ，那么 `DocTestFinder.find()` 将只检查给定的对象，而不是任何包含的对象。

如果可选参数 _exclude_empty_ 为 False ，那么 `DocTestFinder.find()` 将包括对文档字符串为空的对象的测试。

`DocTestFinder` 定义了以下方法：

find( _obj[, name][, module][, globs][, extraglobs]_)¶
~~~
    

~~~
返回 `DocTest` 的列表，该列表由 _obj_ 的文档串或其包含的任何对象的文档串定义。

可选参数 _name_ 指定了对象的名称；这个名称将被用来为返回的 `DocTest` 构建名称。 如果没有指定*name*，则使用 `obj.__name__` 。

可选参数 _module_ 是包含给定对象的模块。如果没有指定模块或者是 `None` ，那么测试查找器将试图自动确定正确的模块。 该对象被使用的模块：

  * 作为一个默认的命名空间，如果没有指定 _globs_ 。

  * 为了防止 DocTestFinder 从其他模块导入的对象中提取 DocTest 。 (包含有除 _module_ 以外的模块的对象会被忽略)。

  * 找到包含该对象的文件名。

  * 找到该对象在其文件中的行号。

如果 _module_ 是 `False` ，将不会试图找到这个模块。 这是不明确的，主要用于测试 doctest 本身：如果 _module_ 是 `False` ，或者是 `None` 但不能自动找到，那么所有对象都被认为属于（不存在的）模块，所以所有包含的对象将（递归地）被搜索到 doctest 。

每个 `DocTest` 的 globals 是由 _globs_ 和 _extraglobs_ 组合而成的（ _extraglobs_ 的绑定覆盖 _globs_ 的绑定）。 为每个 `DocTest` 创建一个新的 globals 字典的浅层拷贝。如果没有指定 _globs_ ，那么它默认为模块的 ___dict___ ，如果指定了或者为 `{}` ，则除外。 如果没有指定 _extraglobs_ ，那么它默认为 `{}` 。

### DocTestParser 对象¶

_class _doctest.DocTestParser¶
~~~
    

~~~
一个处理类，用于从一个字符串中提取交互式的用例，并使用它们来创建一个 `DocTest` 对象。

`DocTestParser` 定义了以下方法：

get_doctest( _string_ , _globs_ , _name_ , _filename_ , _lineno_ )¶
~~~
    

~~~
从给定的字符串中提取所有的测试用例，并将它们收集到一个 `DocTest` 对象中。

_globs_ 、 _name_ 、 _filename_ 和 _lineno_ 是新的 `DocTest` 对象的属性。 更多信息请参见 `DocTest` 的文档。

get_examples( _string_ , _name ='<string>'_)¶
~~~
    

~~~
从给定的字符串中提取所有的测试用例，并以 `Example` 对象列表的形式返回。 行数以 0 为基数。 可选参数 _name_ 用于识别这个字符串的名称，只用于错误信息。

parse( _string_ , _name ='<string>'_)¶
~~~
    

~~~
将给定的字符串分成用例和中间的文本，并以 `Example` 和字符串交替的列表形式返回。 `Example` 的行号以 0 为基数。 可选参数 _name_ 用于识别这个字符串的名称，只用于错误信息。

### TestResults objects¶

_class _doctest.TestResults( _failed_ , _attempted_ )¶
~~~
    

~~~
failed¶
~~~
    

~~~
Number of failed tests.

attempted¶
~~~
    

~~~
Number of attempted tests.

skipped¶
~~~
    

~~~
Number of skipped tests.

在 3.13 版本加入.

### DocTestRunner 对象¶

_class _doctest.DocTestRunner( _checker =None_, _verbose =None_, _optionflags =0_)¶
~~~
    

~~~
一个处理类，用于执行和验证 `DocTest` 中的交互式用例。

预期输出和实际输出之间的比较是由一个 `OutputChecker` 完成的。 这种比较可以用一些选项标志来定制；更多信息请看 选项标记 部分。 如果选项标志不够，那么也可以通过向构造函数传递 `OutputChecker` 的子类来定制比较。

The test runner's display output can be controlled in two ways. First, an output function can be passed to `run()`; this function will be called with strings that should be displayed. It defaults to `sys.stdout.write`. If capturing the output is not sufficient, then the display output can be also customized by subclassing DocTestRunner, and overriding the methods `report_start()`, `report_success()`, `report_unexpected_exception()`, and `report_failure()`.

可选的关键字参数 _checker_ 指定了 `OutputChecker` 对象（或其相似替换），它应该被用来比较预期输出和 doctest 用例的实际输出。

可选的关键字参数 _verbose_ 控制 `DocTestRunner` 的详细程度。 如果 _verbose_ 是 `True` ，那么每个用例的信息都会被打印出来，当它正在运行时。 如果 _verbose_ 是 `False` ，则只打印失败的信息。 当 _verbose_ 没有指定，或者为 `None` ，如果使用了命令行开关 `-v` ，则使用verbose输出。

可选的关键字参数 _optionflags_ 可以用来控制测试运行器如何比较预期输出和实际输出，以及如何显示失败。更多信息，请参见章节 选项标记 。

The test runner accumulates statistics. The aggregated number of attempted, failed and skipped examples is also available via the `tries`, `failures` and `skips` attributes. The `run()` and `summarize()` methods return a `TestResults` instance.

`DocTestParser` 定义了以下方法：

report_start( _out_ , _test_ , _example_ )¶
~~~
    

~~~
报告测试运行器即将处理给定的用例。提供这个方法是为了让 `DocTestRunner` 的子类能够定制他们的输出；它不应该被直接调用。

_example_ 是即将被处理的用。 _test_ 是 _包含用例_ 的测试。 _out_ 是传递给 `DocTestRunner.run()` 的输出函数。

report_success( _out_ , _test_ , _example_ , _got_ )¶
~~~
    

~~~
报告给定的用例运行成功。 提供这个方法是为了让 `DocTestRunner` 的子类能够定制他们的输出；它不应该被直接调用。

_example_ 是即将被处理的用例。 _got_ 是这个例子的实际输出。 _test_ 是包含 _example_ 的测试。 _out_ 是传递给 `DocTestRunner.run()` 的输出函数。

report_failure( _out_ , _test_ , _example_ , _got_ )¶
~~~
    

~~~
报告给定的用例运行失败。 提供这个方法是为了让 `DocTestRunner` 的子类能够定制他们的输出；它不应该被直接调用。

_example_ 是即将被处理的用例。 _got_ 是这个例子的实际输出。 _test_ 是包含 _example_ 的测试。 _out_ 是传递给 `DocTestRunner.run()` 的输出函数。

report_unexpected_exception( _out_ , _test_ , _example_ , _exc_info_ )¶
~~~
    

~~~
报告给定的用例触发了一个异常。 提供这个方法是为了让 `DocTestRunner` 的子类能够定制他们的输出；它不应该被直接调用。

_example_ 是将要被处理的用例。 _exc_info_ 是一个元组，包含关于异常的信息（如由 [`sys.exc_info()`](sys.md#sys.exc_info "sys.exc_info") 返回）。 _test_ 是包含 _example_ 的测试。 _out_ 是传递给 `DocTestRunner.run()` 的输出函数。

run( _test_ , _compileflags =None_, _out =None_, _clear_globs =True_)¶
~~~
    

~~~
Run the examples in _test_ (a `DocTest` object), and display the results using the writer function _out_. Return a `TestResults` instance.

这些用例都是在命名空间 `test.globs` 中运行的。 如果 _clear_globs_ 为 True （默认），那么这个命名空间将在测试运行后被清除，以帮助进行垃圾回收。如果你想在测试完成后检查命名空间，那么使用 _clear_globs=False_ 。

_compileflags_ 给出了 Python 编译器在运行例子时应该使用的标志集。如果没有指定，那么它将默认为适用于 _globs_ 的 future-import 标志集。

每个用例的输出都使用 `DocTestRunner` 的输出检查器进行检查，结果由 `DocTestRunner.report_*()` 方法进行格式化。

summarize( _verbose =None_)¶
~~~
    

~~~
Print a summary of all the test cases that have been run by this DocTestRunner, and return a `TestResults` instance.

可选的 _verbose_ 参数控制摘要的详细程度。 如果没有指定 verbose ，那么将使用 `DocTestRunner` 的 verbose 。

`DocTestParser` has the following attributes:

tries¶
~~~
    

~~~
Number of attempted examples.

failures¶
~~~
    

~~~
Number of failed examples.

skips¶
~~~
    

~~~
Number of skipped examples.

在 3.13 版本加入.

### OutputChecker 对象¶

_class _doctest.OutputChecker¶
~~~
    

~~~
一个用于检查测试用例的实际输出是否与预期输出相匹配的类。 `OutputChecker` 定义了两个方法： `check_output()` ，比较给定的一对输出，如果它们匹配则返回 `True` ； `output_difference()` ，返回一个描述两个输出之间差异的字符串。

`OutputChecker` 定义了以下方法：

check_output( _want_ , _got_ , _optionflags_ )¶
~~~
    

~~~
如果一个用例的实际输出（ _got_ ）与预期输出（ _want_ ）匹配，则返回 `True` 。 如果这些字符串是相同的，总是被认为是匹配的；但是根据测试运行器使用的选项标志，也可能有几种非精确的匹配类型。 参见章节 选项标记 了解更多关于选项标志的信息。

output_difference( _example_ , _got_ , _optionflags_ )¶
~~~
    

~~~
返回一个字符串，描述给定用例（ _example_ ）的预期输出和实际输出（ _got_ ）之间的差异。 _optionflags_ 是用于比较 _want_ 和 _got_ 的选项标志集。

## 调试¶

Doctest 提供了几种调试 doctest 用例的机制：

  * 有几个函数将测试转换为可执行的 Python 程序，这些程序可以在 Python 调试器， [`pdb`](pdb.md#module-pdb "pdb: The Python debugger for interactive interpreters.") 下运行。

  * `DebugRunner` 类是 `DocTestRunner` 的一个子类，它为第一个失败的用例触发一个异常，包含关于这个用例的信息。这些信息可以用来对这个用例进行事后调试。

  * 由 `DocTestSuite()` 生成的 [`unittest`](unittest.md#module-unittest "unittest: Unit testing framework for Python.") 用例支持由 [`unittest.TestCase`](unittest.md#unittest.TestCase "unittest.TestCase") 定义的 `debug()` 方法，。

  * 你可以在 doctest 的用例中加入对 [`pdb.set_trace()`](pdb.md#pdb.set_trace "pdb.set_trace") 的调用，当这一行被执行时，你会进入 Python 调试器。 然后你可以检查变量的当前值，等等。 例如，假设 `a.py` 只包含这个模块 docstring
    
        """
    >>> def f(x):
    ...     g(x*2)
    >>> def g(x):
    ...     print(x+3)
    ...     import pdb; pdb.set_trace()
    >>> f(3)
    9
    """
~~~

那么一个交互式Python会话可能是这样的:

    
        >>> import a, doctest
    >>> doctest.testmod(a)
    --Return--
    > <doctest a[1]>(3)g()->None
    -> import pdb; pdb.set_trace()
    (Pdb) list
      1     def g(x):
      2         print(x+3)
      3  ->     import pdb; pdb.set_trace()
    [EOF]
    (Pdb) p x
    6
    (Pdb) step
    --Return--
    > <doctest a[0]>(2)f()->None
    -> g(x*2)
    (Pdb) list
      1     def f(x):
      2  ->     g(x*2)
    [EOF]
    (Pdb) p x
    3
    (Pdb) step
    --Return--
    > <doctest a[2]>(1)?()->None
    -> f(3)
    (Pdb) cont
    (0, 3)
    >>>
    

将测试转换为 Python 代码的函数，并可能在调试器下运行合成的代码:

doctest.script_from_examples( _s_ )¶

    

~~~
将带有用例的文本转换为脚本。

参数 _s_ 是一个包含测试用例的字符串。 该字符串被转换为 Python 脚本，其中 _s_ 中的 doctest 用例被转换为常规代码，其他的都被转换为 Python 注释。 生成的脚本将以字符串的形式返回。例如，
~~~
    
    
~~~
import doctest
print(doctest.script_from_examples(r"""
    Set x and y to 1 and 2.
    >>> x, y = 1, 2

    Print their sum:
    >>> print(x+y)
    3
"""))
~~~

显示:

    
    
~~~
# Set x and y to 1 and 2.
x, y = 1, 2
#
# Print their sum:
print(x+y)
# Expected:
## 3
~~~

这个函数在内部被其他函数使用（见下文），但当你想把一个交互式 Python 会话转化为 Python 脚本时，也会很有用。

doctest.testsource( _module_ , _name_ )¶

    

~~~
将一个对象的 doctest 转换为一个脚本。

参数 _module_ 是一个模块对象，或者一个模块带点的名称，包含对其 doctest 感兴趣的对象。 参数 _name_ 是具有感兴趣的测试的对象的名称（在模块中）。 结果是一个字符串，包含该对象的文本串转换为 Python 脚本，如上面 `script_from_examples()` 所述。 例如，如果模块 `a.py` 包含一个顶级函数 `f()` ，那么
~~~
    
    
~~~
import a, doctest
print(doctest.testsource(a, "a.f"))
~~~

打印函数 `f()` 的文档串的脚本版本，将测试转换为代码，其余部分放在注释中。

doctest.debug( _module_ , _name_ , _pm =False_)¶

    

~~~
对一个对象的 doctest 进行调试。

_module_ 和 _name_ 参数与上面函数 `testsource()` 的参数相同。 被命名对象的文本串的合成 Python 脚本被写入一个临时文件，然后该文件在 Python 调试器 [`pdb`](pdb.md#module-pdb "pdb: The Python debugger for interactive interpreters.") 的控制下运行。

`module.__dict__` 的一个浅层拷贝被用于本地和全局的执行环境。

可选参数 _pm_ 控制是否使用事后调试。 如果 _pm_ 为 True ，则直接运行脚本文件，只有当脚本通过引发一个未处理的异常而终止时，调试器才会介入。如果是这样，就会通过 [`pdb.post_mortem()`](pdb.md#pdb.post_mortem "pdb.post_mortem") 调用事后调试，并传递未处理异常的跟踪对象。如果没有指定 _pm_ ，或者是 False ，脚本将从一开始就在调试器下运行，通过传递一个适当的 [`exec()`](functions.md#exec "exec") 调用给 [`pdb.run()`](pdb.md#pdb.run "pdb.run") 。

doctest.debug_src( _src_ , _pm =False_, _globs =None_)¶
~~~
    

~~~
在一个字符串中调试 doctest 。

这就像上面的函数 `debug()` ，只是通过 _src_ 参数，直接指定一个包含测试用例的字符串。

可选参数 _pm_ 的含义与上述函数 `debug()` 的含义相同。

可选的参数 _globs_ 给出了一个字典，作为本地和全局的执行环境。 如果没有指定，或者为 `None` ，则使用一个空的字典。如果指定，则使用字典的浅层拷贝。

`DebugRunner` 类，以及它可能触发的特殊异常，是测试框架作者最感兴趣的，在此仅作简要介绍。请看源代码，特别是 `DebugRunner` 的文档串（这是一个测试！）以了解更多细节。

_class _doctest.DebugRunner( _checker =None_, _verbose =None_, _optionflags =0_)¶
~~~
    

~~~
`DocTestRunner` 的一个子类，一旦遇到失败，就会触发一个异常。 如果一个意外的异常发生，就会引发一个 `UnexpectedException` 异常，包含测试、用例和原始异常。 如果输出不匹配，那么就会引发一个 `DocTestFailure` 异常，包含测试、用例和实际输出。

关于构造函数参数和方法的信息，请参见 `DocTestRunner` 部分的文档 高级 API 。

`DebugRunner` 实例可能会触发两种异常。

_exception _doctest.DocTestFailure( _test_ , _example_ , _got_ )¶
~~~
    

~~~
`DocTestRunner` 触发的异常，表示一个 doctest 用例的实际输出与预期输出不一致。构造函数参数被用来初始化相同名称的属性。

`DocTestFailure` 定义了以下属性:

DocTestFailure.test¶
~~~
    

~~~
当该用例失败时正在运行的 `DocTest` 对象。

DocTestFailure.example¶
~~~
    

~~~
失败的 `Example` 。

DocTestFailure.got¶
~~~
    

~~~
用例的实际输出。

_exception _doctest.UnexpectedException( _test_ , _example_ , _exc_info_ )¶
~~~
    

~~~
一个由 `DocTestRunner` 触发的异常，以提示一个 doctest 用例引发了一个意外的异常。 构造函数参数被用来初始化相同名称的属性。

`UnexpectedException` 定义了以下属性:

UnexpectedException.test¶
~~~
    

~~~
当该用例失败时正在运行的 `DocTest` 对象。

UnexpectedException.example¶
~~~
    

~~~
失败的 `Example` 。

UnexpectedException.exc_info¶
~~~
    

~~~
一个包含意外异常信息的元组，由 `sys.ex_info()` 返回。

## 肥皂盒¶

正如介绍中提到的， `doctest` 已经发展到有三个主要用途:

  1. 检查 docstring 中的用例。

  2. 回归测试。

  3. 可执行的文档/文字测试。

这些用途有不同的要求，区分它们是很重要的。特别是，用晦涩难懂的测试用例来填充你的文档字符串会使文档变得很糟糕。

在编写文档串时，要谨慎地选择文档串的用例。这是一门需要学习的艺术——一开始可能并不自然。用例应该为文档增加真正的价值。 一个好的用例往往可以抵得上许多文字。如果用心去做，这些用例对你的用户来说是非常有价值的，而且随着时间的推移和事情的变化，收集这些用例所花费的时间也会得到很多倍的回报。 我仍然惊讶于我的 `doctest` 用例在一个“无害”的变化后停止工作。

Doctest 也是回归测试的一个很好的工具，特别是如果你不吝啬解释的文字。 通过交错的文本和用例，可以更容易地跟踪真正的测试，以及为什么。当测试失败时，好的文本可以使你更容易弄清问题所在，以及如何解决。 的确，你可以在基于代码的测试中写大量的注释，但很少有程序员这样做。许多人发现，使用 doctest 方法反而能使测试更加清晰。 也许这只是因为 doctest 使写散文比写代码容易一些，而在代码中写注释则有点困难。 我认为它比这更深入：当写一个基于 doctest 的测试时，自然的态度是你想解释你的软件的细微之处，并用例子来说明它们。这反过来又自然地导致了测试文件从最简单的功能开始，然后逻辑地发展到复杂和边缘案例。 一个连贯的叙述是结果，而不是一个孤立的函数集合，似乎是随机的测试孤立的功能位。 这是一种不同的态度，产生不同的结果，模糊了测试和解释之间的区别。

回归测试最好限制在专用对象或文件中。 有几种组织测试的选择：

  * 编写包含测试案例的文本文件作为交互式例子，并使用 `testfile()` 或 `DocFileSuite()` 来测试这些文件。 建议这样做，尽管对于新的项目来说是最容易做到的，从一开始就设计成使用 doctest 。

  * 定义命名为 `_regrtest_topic` 的函数，由单个文档串组成，包含命名主题的测试用例。 这些函数可以包含在与模块相同的文件中，或分离出来成为一个单独的测试文件。

  * 定义一个从回归测试主题到包含测试用例的文档串的 `__test__` 字典映射。

当你把你的测试放在一个模块中时，这个模块本身就可以成为测试运行器。 当一个测试失败时，你可以安排你的测试运行器只重新运行失败的测试，同时调试问题。 下面是这样一个测试运行器的最小例子:
~~~
    
    
~~~
if __name__ == '__main__':
    import doctest
    flags = doctest.REPORT_NDIFF|doctest.FAIL_FAST
    if len(sys.argv) > 1:
        name = sys.argv[1]
        if name in globals():
            obj = globals()[name]
        else:
            obj = __test__[name]
        doctest.run_docstring_examples(obj, globals(), name=name,
                                       optionflags=flags)
    else:
        fail, total = doctest.testmod(optionflags=flags)
        print("{} failures out of {} tests".format(fail, total))
~~~

备注

[1]

不支持同时包含预期输出和异常的用例。试图猜测一个在哪里结束，另一个在哪里开始，太容易出错了，而且这也会使测试变得混乱。

