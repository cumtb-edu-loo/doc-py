# `code` \--- 解释器基类¶

**源代码：** [Lib/code.py](https://github.com/python/cpython/tree/3.12/Lib/code.py)

* * *

`code` 模块提供了在 Python 中实现 read-eval-print 循环的功能。它包含两个类和一些快捷功能，可用于构建提供交互式解释器的应用程序。

_class _code.InteractiveInterpreter( _locals =None_)¶

    

~~~
这个类处理解析器和解释器状态（用户命名空间的）；它不处理缓冲器、终端提示区或着输入文件名（文件名总是显示地传递）。可选的 _locals_ 参数指定一个字典，字典里面包含将在此类执行的代码；它默认创建新的字典，其键 `'__name__'` 设置为 `'__console__'` ，键 `'__doc__'` 设置为 `None` 。

_class _code.InteractiveConsole( _locals =None_, _filename ='<console>'_)¶
~~~
    

~~~
尽可能模拟交互式 Python 解释器的行为。此类建立在 `InteractiveInterpreter` 的基础上，使用熟悉的 `sys.ps1` 和 `sys.ps2` 作为输入提示符，并有输入缓冲。

code.interact( _banner =None_, _readfunc =None_, _local =None_, _exitmsg =None_)¶
~~~
    

~~~
运行一个 read-eval-print 循环的便捷函数。这会创建一个新的 `InteractiveConsole` 实例。如果提供了 _readfunc_ ，会设置为 `InteractiveConsole.raw_input()` 方法。如果提供了 _local_ ，则将其传递给 `InteractiveConsole` 的构造函数，以用作解释器循环的默认命名空间。然后，如果提供了 _banner_ 和 _exitmsg_ ，实例的 `interact()` 方法会以此为标题和退出消息。控制台对象在使用后将被丢弃。

在 3.6 版本发生变更: 加入 _exitmsg_ 参数。

code.compile_command( _source_ , _filename ='<input>'_, _symbol ='single'_)¶
~~~
    

~~~
这个函数主要用来模拟 Python 解释器的主循环（即 read-eval-print 循环）。难点的部分是当用户输入不完整命令时，判断能否通过之后的输入来完成（要么成为完整的命令，要么语法错误）。该函数 _几乎_ 和实际的解释器主循环的判断是相同的。

_source_ 是源字符串； _filename_ 是可选的用作读取源的文件名，默认为 `'<input>'`； _symbol_ 是可选的语法开启符号，应为 `'single'` (默认), `'eval'` 或 `'exec'`。

如果命令完整且有效则返回一个代码对象 (等价于 `compile(source, filename, symbol)`)；如果命令不完整则返回 `None`；如果命令完整但包含语法错误则会引发 [`SyntaxError`](exceptions.md#SyntaxError "SyntaxError") 或 [`OverflowError`](exceptions.md#OverflowError "OverflowError") 而如果命令包含无效字面值则将引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

## 交互解释器对象¶

InteractiveInterpreter.runsource( _source_ , _filename ='<input>'_, _symbol ='single'_)¶
~~~
    

~~~
在解释器中编译并运行一段源码。 所用参数与 `compile_command()` 一样； _filename_ 的默认值为 `'<input>'`， _symbol_ 则为 `'single'`。 可能发生以下情况之一：

  * 输入不正确；`compile_command()` 引发了一个异常 ([`SyntaxError`](exceptions.md#SyntaxError "SyntaxError") 或 [`OverflowError`](exceptions.md#OverflowError "OverflowError"))。 将通过调用 `showsyntaxerror()` 方法打印语法回溯信息。 `runsource()` 返回 `False`。

  * 输入不完整，需要更多输入；函数 `compile_command()` 返回 `None` 。方法 `runsource()` 返回 `True` 。

  * 输入完整；`compile_command()` 返回了一个代码对象。 将通过调用 `runcode()` 执行代码（该方法也会处理运行时异常，[`SystemExit`](exceptions.md#SystemExit "SystemExit") 除外）。 `runsource()` 返回 `False`。

该返回值用于决定使用 `sys.ps1` 还是 `sys.ps2` 来作为下一行的输入提示符。

InteractiveInterpreter.runcode( _code_ )¶
~~~
    

~~~
执行一个代码对象。当发生异常时，调用 `showtraceback()` 来显示回溯。除 [`SystemExit`](exceptions.md#SystemExit "SystemExit") （允许传播）以外的所有异常都会被捕获。

有关 [`KeyboardInterrupt`](exceptions.md#KeyboardInterrupt "KeyboardInterrupt") 的说明，该异常可能发生于此代码的其他位置，并且并不总能被捕获。 调用者应当准备好处理它。

InteractiveInterpreter.showsyntaxerror( _filename =None_)¶
~~~
    

~~~
显示刚发生的语法错误。 这不会显示堆栈回溯因为语法错误并无此种信息。 如果给出了 _filename_ ，它会被放入异常来替代 Python 解析器所提供的默认文件名，因为它在从一个字符串读取时总是会使用 `'<string>'`。 输出将由 `write()` 方法来写入。

InteractiveInterpreter.showtraceback()¶
~~~
    

~~~
显示刚发生的异常。 我们移除了第一个堆栈条目因为它从属于解释器对象的实现。 输出将由 `write()` 方法来写入。

在 3.5 版本发生变更: 将显示完整的链式回溯，而不只是主回溯。

InteractiveInterpreter.write( _data_ )¶
~~~
    

~~~
将一个字符串写入到标准错误流 (`sys.stderr`)。 所有派生类都应重载此方法以提供必要的正确输出处理。

## 交互式控制台对象¶

`InteractiveConsole` 类是 `InteractiveInterpreter` 的子类，因此它提供了解释器对象的所有方法，还有以下的额外方法。

InteractiveConsole.interact( _banner =None_, _exitmsg =None_)¶
~~~
    

~~~
近似地模拟交互式 Python 终端。 可选的 _banner_ 参数指定要在第一次交互前打印的条幅；默认情况下会类似于标准 Python 解释器所打印的内容，并附上外加圆括号的终端对象类名（这样就不会与真正的解释器混淆 —— 因为确实太像了！）

可选的 _exitmsg_ 参数指定要在退出时打印的退出消息。 传入空字符串可以屏蔽退出消息。 如果 _exitmsg_ 未给出或为 `None`，则将打印默认消息。

在 3.4 版本发生变更: 要禁止打印任何条幅消息，请传递一个空字符串。

在 3.6 版本发生变更: 退出时打印退出消息。

InteractiveConsole.push( _line_ )¶
~~~
    

~~~
将一行源代码文本推入解释器。 行内容不应带有末尾换行符；它可以有内部换行符。 行内容会被添加到一缓冲区然后调用解释器的 `runsource()` 方法并附带缓冲区内容的拼接结果作为源文本。 如果提示命令已执行或不合法，缓冲区将被重置；在其他情况下，则命令结束，缓冲区将在添加行后保持原样。 如果需要更多的输入则返回值为 `True`，如果行已按某种方式被处理则返回值为 `False` (这与 `runsource()` 相同)。

InteractiveConsole.resetbuffer()¶
~~~
    

~~~
从输入缓冲区中删除所有未处理的内容。

InteractiveConsole.raw_input( _prompt =''_)¶
~~~
    

~~~