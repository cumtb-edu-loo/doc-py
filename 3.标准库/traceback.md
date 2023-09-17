# `traceback` —— 打印或读取堆栈的跟踪信息¶

**源代码：** [Lib/traceback.py](https://github.com/python/cpython/tree/3.12/Lib/traceback.py)

* * *

该模块提供了一个标准接口来提取、格式化和打印 Python 程序的堆栈跟踪结果。它完全模仿Python 解释器在打印堆栈跟踪结果时的行为。当您想要在程序控制下打印堆栈跟踪结果时，例如在“封装”解释器时，这是非常有用的。

本模块使用回溯对象 --- 它们是类型为 [`types.TracebackType`](types.md#types.TracebackType "types.TracebackType") 的对象，将被赋值给 [`BaseException`](3.标准库/exceptions.md#BaseException "BaseException") 实例的 `__traceback__` 字段。

参见

模块 [`faulthandler`](faulthandler.md#module-faulthandler "faulthandler: Dump the Python traceback.")

    

~~~
用于在发生错误、超时或用户信号时显式地转储 Python 回溯信息。

模块 [`pdb`](pdb.md#module-pdb "pdb: The Python debugger for interactive interpreters.")
~~~
    

~~~
用于 Python 程序的交互式源代码调试器。

这个模块定义了以下函数：

traceback.print_tb( _tb_ , _limit =None_, _file =None_)¶
~~~
    

~~~
如果*limit*是正整数，那么从 traceback 对象 "tb" 输出最高 _limit_ 个（从调用函数开始的）栈的堆栈回溯‎条目‎；如果 _limit_ 是负数就输出 `abs(limit)` 个回溯条目；又如果 _limit_ 被省略或者为 `None` ，那么就会输出所有回溯条目。如果 _file_ 被省略或为 `None` 那么就会输出至标准输出 `sys.stderr` 否则它应该是一个打开的文件或者文件类对象来接收输出

在 3.5 版本发生变更: 添加了对负数值 _limit_ 的支持

traceback.print_exception( _exc_ , _/_ , [ _value_ , _tb_ , ] _limit=None_ , _file=None_ , _chain=True_ )¶
~~~
    

~~~
打印回溯对象 _tb_ 到 _file_ 的异常信息和整个堆栈回溯。这和 `print_tb()` 比有以下方面不同：

  * 如果 _tb_ 不为 `None`，它将打印头部 `Traceback (most recent call last):`

  * 它将在栈回溯之后打印异常类型和 _value_

  * 如果 _type(value)_ 为 [`SyntaxError`](exceptions.md#SyntaxError "SyntaxError") 且 _value_ 具有适当的格式，它会打印发生语法错误的行并用一个圆点来指明错误的大致位置。

从 Python 3.10 开始，可以不再传递 _value_ 和 _tb_ ，而是传递一个异常对象作为第一个参数。 如果提供了 _value_ 和 _tb_ ，则第一个参数会被忽略以便提供向下兼容性。

可选的 _limit_ 参数具有与 `print_tb()` 的相同含义。 如果 _chain_ 为真值（默认），则链式异常（异常的 `__cause__` 或 `__context__` 属性）也将被打印出来，就像解释器本身在打印未处理的异常时一样。

在 3.5 版本发生变更: _etype_ 参数会被忽略并根据 _value_ 推断出来。

在 3.10 版本发生变更: _etype_ 形参已被重命名为 _exc_ 并且现在是仅限位置形参。

traceback.print_exc( _limit =None_, _file =None_, _chain =True_)¶
~~~
    

~~~
这是 `print_exception(sys.exception(), limit, file, chain)` 的快捷操作。

traceback.print_last( _limit =None_, _file =None_, _chain =True_)¶
~~~
    

~~~
这是 `print_exception(sys.last_exc, limit, file, chain)` 的一个快捷方式。 通常它将只在异常到达交互提示符之后才会起作用 (参见 [`sys.last_exc`](sys.md#sys.last_exc "sys.last_exc"))。

traceback.print_stack( _f =None_, _limit =None_, _file =None_)¶
~~~
    

~~~
如果 _limit_ 为正数则打印至多 _limit_ 个栈回溯条目（从发起调用点开始）。 在其他情况下，则打印最后 `abs(limit)` 个条目。 如果 _limit_ 被省略或为 `None`，则会打印所有条目。 可选的 _f_ 参数可被用来指明一个用于开始的替代栈。 可选的 _file_ 参数具有与 `print_tb()` 的相同含义。

在 3.5 版本发生变更: 添加了对负数值 _limit_ 的支持

traceback.extract_tb( _tb_ , _limit =None_)¶
~~~
    

~~~
返回一个 `StackSummary` 对象来代表从回溯对象 _tb_ 提取的 "预处理" 栈回溯条目列表。 它适用于栈回溯的替代格式化。 可选的 _limit_ 参数具有与 `print_tb()` 的相同含义。 "预处理" 栈回溯条目是一个 `FrameSummary` 对象，其中包含代表通常为栈回溯打印的信息的 `filename`, `lineno`, `name` 和 `line` 等属性。 `line` 是一个去除了前导和末尾空白符的字符串；如果源代码不可用则它将为 `None`。

traceback.extract_stack( _f =None_, _limit =None_)¶
~~~
    

~~~
从当前的栈帧提取原始回溯信息。 返回值具有与 `extract_tb()` 的相同格式。 可选的 _f_ 和 _limit_ 参数具有与 `print_stack()` 的相同含义。

traceback.format_list( _extracted_list_ )¶
~~~
    

~~~
给定一个由元组或如 `extract_tb()` 或 `extract_stack()` 所返回的 `FrameSummary` 对象组成的列表，返回一个可打印的字符串列表。 结果列表中的每个字符串都对应于参数列表中具有相同索引号的条目。 每个字符串以一个换行符结束；对于那些源文本行不为 `None` 的条目，字符串也可能包含内部换行符。

traceback.format_exception_only( _exc_ , _/_ [, _value_ ])¶
~~~
    

~~~
使用 `sys.last_value` 等给出的异常值来格式化回溯中的异常部分。 返回值是一个字符串的列表，每个字符串都以换行符结束。 该列表包含异常消息，它通常是一个字符串；但是，对于 [`SyntaxError`](exceptions.md#SyntaxError "SyntaxError") 异常，它将包含多行并且（当打印时）会显示语法错误发生位置的详细信息。 在异常消息之后，该列表还包含了异常的 [`注释`](exceptions.md#BaseException.__notes__ "BaseException.__notes__")。

从 Python 3.10 开始，可以不传入 _value_ ，而是传入一个异常对象作为第一个参数。 如果提供了 _value_ ，则第一个参数将被忽略以便提供向下兼容性。

在 3.10 版本发生变更: _etype_ 形参已被重命名为 _exc_ 并且现在是仅限位置形参。

在 3.11 版本发生变更: 返回的列表现在包括关联到异常的任何注释。

traceback.format_exception( _exc_ , _/_ , [ _value_ , _tb_ , ] _limit=None_ , _chain=True_ )¶
~~~
    

~~~
格式化一个栈跟踪和异常信息。 参数的含义与传给 `print_exception()` 的相应参数相同。 返回值是一个字符串列表，每个字符串都以一个换行符结束且有些还包含内部换行符。 当这些行被拼接并打印时，打印的文本与 `print_exception()` 的完全相同。

在 3.5 版本发生变更: _etype_ 参数会被忽略并根据 _value_ 推断出来。

在 3.10 版本发生变更: 此函数的行为和签名已被修改以与 `print_exception()` 相匹配。

traceback.format_exc( _limit =None_, _chain =True_)¶
~~~
    

~~~
这类似于 `print_exc(limit)` 但会返回一个字符串而不是打印到一个文件。

traceback.format_tb( _tb_ , _limit =None_)¶
~~~
    

~~~
是 `format_list(extract_tb(tb, limit))` 的简写形式。

traceback.format_stack( _f =None_, _limit =None_)¶
~~~
    

~~~
是 `format_list(extract_stack(f, limit))` 的简写形式。

traceback.clear_frames( _tb_ )¶
~~~
    

~~~
通过调用每个帧对象的 `clear()` 方法来清除回溯 _tb_ 中所有栈帧的局部变量。

在 3.4 版本加入.

traceback.walk_stack( _f_ )¶
~~~
    

~~~
从给定的帧开始访问 `f.f_back` 之后的栈，产生每一帧的帧和行号。 如果 _f_ 为 `None`，则会使用当前栈。 这个辅助函数要与 `StackSummary.extract()` 一起使用。

在 3.5 版本加入.

traceback.walk_tb( _tb_ )¶
~~~
    

~~~
访问 `tb_next` 之后的回溯并产生每一帧的帧和行号。 这个辅助函数要与 `StackSummary.extract()` 一起使用。

在 3.5 版本加入.

此模块还定义了以下的类:

## `TracebackException` 对象¶

在 3.5 版本加入.

`TracebackException` 对象基于实际的异常创建通过轻量的方式捕获数据以便随后打印。

_class _traceback.TracebackException( _exc_type_ , _exc_value_ , _exc_traceback_ , _*_ , _limit =None_, _lookup_lines =True_, _capture_locals =False_, _compact =False_, _max_group_width =15_, _max_group_depth =10_)¶
~~~
    

~~~
捕获一个异常以便随后渲染。 _limit_ , _lookup_lines_ 和 _capture_locals_ 的含义与 `StackSummary` 类的相同。

如果 _compact_ 为真值，则只有 `TracebackException` 的 `format` 方法所需要的数据会被保存在类属性中。 特别地，`__context__` 字段只有在 `__cause__` 为 `None` 且 `__suppress_context__` 为假值时才会被计算。

请注意当局部变量被捕获时，它们也会被显示在回溯中。

_max_group_width_ 和 _max_group_depth_ 控制异常组的格式化 (参见 [`BaseExceptionGroup`](exceptions.md#BaseExceptionGroup "BaseExceptionGroup"))。 depth 是指分组的嵌套层级，而 width 是指一个异常组的异常数组的大小。 格式化的输出在达到某个限制时将被截断。

在 3.10 版本发生变更: 增加了 _compact_ 形参。

在 3.11 版本发生变更: 添加了 _max_group_width_ 和 _max_group_depth_ 形参。parameters.

__cause__¶
~~~
    

~~~
原始 `__cause__` 的 `TracebackException`。

__context__¶
~~~
    

~~~
原始 `__context__` 的 `TracebackException`。

exceptions¶
~~~
    

~~~
如果 `self` 代表一个 [`ExceptionGroup`](exceptions.md#ExceptionGroup "ExceptionGroup")，此字段将保存一个由代表被嵌套异常的 `TracebackException` 实例组成的列表。 否则它将为 `None`。

在 3.11 版本加入.

__suppress_context__¶
~~~
    

~~~
来自原始异常的 `__suppress_context__`。

__notes__¶
~~~
    

~~~
来自原始异常的 `__notes__` 值，或者如果异常没有任何注解则为 `None`。 如果它不为 `None` 则会在异常字符串的回溯之后进行格式化。

在 3.11 版本加入.

stack¶
~~~
    

~~~
代表回溯的 `StackSummary`。

exc_type¶
~~~
    

~~~
原始回溯的类。

filename¶
~~~
    

~~~
针对语法错误 —— 错误发生所在的文件名。

lineno¶
~~~
    

~~~
针对语法错误 —— 错误发生所在的行号。

end_lineno¶
~~~
    

~~~
针对语法错误 —— 错误发生所在的末尾行号。 如不存在则可以为 `None`。

在 3.10 版本加入.

text¶
~~~
    

~~~
针对语法错误 —— 错误发生所在的文本。

offset¶
~~~
    

~~~
针对语法错误 —— 错误发生所在的文本内部的偏移量。

end_offset¶
~~~
    

~~~
针对语法错误 —— 错误发生所在的文本末尾偏移量。 如不存在则可以为 `None`。

在 3.10 版本加入.

msg¶
~~~
    

~~~
针对语法错误 —— 编译器错误消息。

_classmethod _from_exception( _exc_ , _*_ , _limit =None_, _lookup_lines =True_, _capture_locals =False_)¶
~~~
    

~~~
捕获一个异常以便随后渲染。 _limit_ , _lookup_lines_ 和 _capture_locals_ 的含义与 `StackSummary` 类的相同。

请注意当局部变量被捕获时，它们也会被显示在回溯中。

print( _*_ , _file =None_, _chain =True_)¶
~~~
    

~~~
将 [`format()`](functions.md#format "format") 所返回的异常信息打印至 _file_ (默认为 `sys.stderr`)。

在 3.11 版本加入.

format( _*_ , _chain =True_)¶
~~~
    

~~~
格式化异常。

如果 _chain_ 不为 `True`，则 `__cause__` 和 `__context__` 将不会被格式化。

返回值是一个字符串的生成器，其中每个字符串都以换行符结束并且有些还会包含内部换行符。 `print_exception()` 是此方法的一个包装器，它只是将这些行打印到一个文件。

format_exception_only( _*_ , _show_group =False_)¶
~~~
    

~~~
格式化回溯的异常部分。

返回值是一个字符串的生成器，每个字符串都以一个换行符结束。

When _show_group_ is `False`, the generator emits the exception's message followed by its notes (if it has any). The exception message is normally a single string; however, for [`SyntaxError`](exceptions.md#SyntaxError "SyntaxError") exceptions, it consists of several lines that (when printed) display detailed information about where the syntax error occurred.

When _show_group_ is `True`, and the exception is an instance of [`BaseExceptionGroup`](exceptions.md#BaseExceptionGroup "BaseExceptionGroup"), the nested exceptions are included as well, recursively, with indentation relative to their nesting depth.

在 3.11 版本发生变更: 异常的注释现在将被包括在输出中。

在 3.13 版本发生变更: Added the _show_group_ parameter.

## `StackSummary` 对象¶

在 3.5 版本加入.

代表一个可被格式化的调用栈的 `StackSummary` 对象。

_class _traceback.StackSummary¶
~~~
    

~~~
_classmethod _extract( _frame_gen_ , _*_ , _limit =None_, _lookup_lines =True_, _capture_locals =False_)¶
~~~
    

~~~
构造一个来自帧生成器的 `StackSummary` 对象（如 `walk_stack()` 或 `walk_tb()` 所返回的一样）。

如果提供了 _limit_ ，则只从 _frame_gen_ 提取指定数量的帧。 如果 _lookup_lines_ 为 `False`，则返回的 `FrameSummary` 对象将不会读入它们的行，这使得创建 `StackSummary` 的开销更低（如果它不会被实际格式化这就会很有价值）。 如果 _capture_locals_ 为 `True` 则每个 `FrameSummary` 中的局部变量会被捕获为对象表示形式。

在 3.12 版本发生变更: 在局部变量的 [`repr()`](functions.md#repr "repr") 上被引发的异常（当 _capture_locals_ 为 `True` 时）不会再被传播给调用方。

_classmethod _from_list( _a_list_ )¶
~~~
    

~~~
从所提供的 `FrameSummary` 对象列表或旧式的元组列表构造一个 `StackSummary` 对象。 每个元组都应当是以文件、行号、名称和行为元素的 4 元组。

format()¶
~~~
    

~~~
返回一个可打印的字符串列表。 结果列表中的每个字符串各自对应来自于栈的单独帧。 每个字符串都以一个换行符结束；对于带有源文本行的条目来说，字符串还可能包含内部换行符。

对于同一帧与行的长序列，将显示前几个重复项，后面跟一个指明之后的实际重复次数的摘要行。

在 3.6 版本发生变更: 重复帧的长序列现在将被缩减。

format_frame_summary( _frame_summary_ )¶
~~~
    

~~~
返回用于打印栈中涉及的某一帧的字符串。 此方法会为每个要用 `StackSummary.format()` 来打印的 `FrameSummary` 对象进行调用。 如果它返回 `None`，该帧将从输出中被省略。

在 3.11 版本加入.

## `FrameSummary` 对象¶

在 3.5 版本加入.

`FrameSummary` 对象代表回溯中的一个单独帧。

_class _traceback.FrameSummary( _filename_ , _lineno_ , _name_ , _lookup_line =True_, _locals =None_, _line =None_)¶
~~~
    

~~~
代表回溯或栈中被格式化或打印的一个单独帧。 它有时可能还带有包括在其中的帧局部变量的字符串化版本。 如果 _lookup_line_ 为 `False`，则不会查找源代码直到 `FrameSummary` 已经访问了 `line` 属性（这还将发生在将其转换为元组时）。 `line` 可能会被直接提供，并将完全阻止行查找的发生。 _locals_ 是一个可选的局部变量字典，如果有提供的话这些变量的表示形式将被存储在摘要中以便随后显示。

## 回溯示例¶

这个简单示例是一个基本的读取-求值-打印循环，类似于（但实用性小于）标准 Python 交互式解释器循环。 对于解释器循环的更完整实现，请参阅 [`code`](code.md#module-code "code: Facilities to implement read-eval-print loops.") 模块。
~~~
    
    
~~~
import sys, traceback

def run_user_code(envdir):
    source = input(">>> ")
    try:
        exec(source, envdir)
    except Exception:
        print("Exception in user code:")
        print("-"*60)
        traceback.print_exc(file=sys.stdout)
        print("-"*60)

envdir = {}
while True:
    run_user_code(envdir)
~~~

下面的例子演示了打印和格式化异常与回溯的不同方式:

    
    
~~~
import sys, traceback

def lumberjack():
    bright_side_of_life()

def bright_side_of_life():
    return tuple()[0]

try:
    lumberjack()
except IndexError:
    exc = sys.exception()
    print("*** print_tb:")
    traceback.print_tb(exc.__traceback__, limit=1, file=sys.stdout)
    print("*** print_exception:")
    traceback.print_exception(exc, limit=2, file=sys.stdout)
    print("*** print_exc:")
    traceback.print_exc(limit=2, file=sys.stdout)
    print("*** format_exc, first and last line:")
    formatted_lines = traceback.format_exc().splitlines()
    print(formatted_lines[0])
    print(formatted_lines[-1])
    print("*** format_exception:")
    print(repr(traceback.format_exception(exc)))
    print("*** extract_tb:")
    print(repr(traceback.extract_tb(exc.__traceback__)))
    print("*** format_tb:")
    print(repr(traceback.format_tb(exc.__traceback__)))
    print("*** tb_lineno:", exc.__traceback__.tb_lineno)
~~~

该示例的输出看起来像是这样的:

    
    
~~~
*** print_tb:
  File "<doctest...>", line 10, in <module>
    lumberjack()
*** print_exception:
Traceback (most recent call last):
  File "<doctest...>", line 10, in <module>
    lumberjack()
  File "<doctest...>", line 4, in lumberjack
    bright_side_of_life()
IndexError: tuple index out of range
*** print_exc:
Traceback (most recent call last):
  File "<doctest...>", line 10, in <module>
    lumberjack()
  File "<doctest...>", line 4, in lumberjack
    bright_side_of_life()
IndexError: tuple index out of range
*** format_exc, first and last line:
Traceback (most recent call last):
IndexError: tuple index out of range
*** format_exception:
['Traceback (most recent call last):\n',
 '  File "<doctest default[0]>", line 10, in <module>\n    lumberjack()\n',
 '  File "<doctest default[0]>", line 4, in lumberjack\n    bright_side_of_life()\n',
 '  File "<doctest default[0]>", line 7, in bright_side_of_life\n    return tuple()[0]\n           ~~~~~~~^^^\n',
 'IndexError: tuple index out of range\n']
*** extract_tb:
[<FrameSummary file <doctest...>, line 10 in <module>>,
 <FrameSummary file <doctest...>, line 4 in lumberjack>,
 <FrameSummary file <doctest...>, line 7 in bright_side_of_life>]
*** format_tb:
['  File "<doctest default[0]>", line 10, in <module>\n    lumberjack()\n',
 '  File "<doctest default[0]>", line 4, in lumberjack\n    bright_side_of_life()\n',
 '  File "<doctest default[0]>", line 7, in bright_side_of_life\n    return tuple()[0]\n           ~~~~~~~^^^\n']
*** tb_lineno: 10
~~~

下面的例子演示了打印和格式化栈的不同方式:

    
    
~~~shell
>>> import traceback
>>> def another_function():
...     lumberstack()
...
>>> def lumberstack():
...     traceback.print_stack()
...     print(repr(traceback.extract_stack()))
...     print(repr(traceback.format_stack()))
...
>>> another_function()
  File "<doctest>", line 10, in <module>
    another_function()
  File "<doctest>", line 3, in another_function
    lumberstack()
  File "<doctest>", line 6, in lumberstack
    traceback.print_stack()
[('<doctest>', 10, '<module>', 'another_function()'),
 ('<doctest>', 3, 'another_function', 'lumberstack()'),
 ('<doctest>', 7, 'lumberstack', 'print(repr(traceback.extract_stack()))')]
['  File "<doctest>", line 10, in <module>\n    another_function()\n',
 '  File "<doctest>", line 3, in another_function\n    lumberstack()\n',
 '  File "<doctest>", line 8, in lumberstack\n    print(repr(traceback.format_stack()))\n']
~~~

最后这个例子演示了最后几个格式化函数:

    
    
~~~shell
>>> import traceback
>>> traceback.format_list([('spam.py', 3, '<module>', 'spam.eggs()'),
...                        ('eggs.py', 42, 'eggs', 'return "bacon"')])
['  File "spam.py", line 3, in <module>\n    spam.eggs()\n',
 '  File "eggs.py", line 42, in eggs\n    return "bacon"\n']
>>> an_error = IndexError('tuple index out of range')
>>> traceback.format_exception_only(type(an_error), an_error)
['IndexError: tuple index out of range\n']
~~~

