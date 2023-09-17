# `pdb` \--- Python 的调试器¶

**源代码：** [Lib/pdb.py](https://github.com/python/cpython/tree/3.12/Lib/pdb.py)

* * *

`pdb` 模块定义了一个交互式源代码调试器，用于 Python 程序。它支持在源码行间设置（有条件的）断点和单步执行，检视堆栈帧，列出源码列表，以及在任何堆栈帧的上下文中运行任意 Python 代码。它还支持事后调试，可以在程序控制下调用。

调试器是可扩展的——调试器实际被定义为 `Pdb` 类。该类目前没有文档，但通过阅读源码很容易理解它。扩展接口使用了 [`bdb`](bdb.md#module-bdb "bdb: Debugger framework.") 和 [`cmd`](cmd.md#module-cmd "cmd: Build line-oriented command interpreters.") 模块。

参见

模块 [`faulthandler`](faulthandler.md#module-faulthandler "faulthandler: Dump the Python traceback.")

    

~~~
用于在发生错误、超时或用户信号时显式地转储 Python 回溯信息。

模块 [`traceback`](traceback.md#module-traceback "traceback: Print or retrieve a stack traceback.")
~~~
    

~~~
提取、格式化和打印 Python 程序的栈回溯信息的标准接口。

中断进入调试器的典型用法是插入:
~~~
    
    
~~~
import pdb; pdb.set_trace()
~~~

或者:

    
    
~~~
breakpoint()
~~~

到你想进入调试器的位置，再运行程序。 然后你可以单步执行这条语句之后的代码，并使用 `continue` 命令来关闭调试器继续运行。

在 3.7 版本加入: 内置函数 [`breakpoint()`](functions.md#breakpoint "breakpoint")，当以默认参数调用它时，可以用来代替 `import pdb; pdb.set_trace()`。

    
    
~~~python
def double(x):
   breakpoint()
   return x * 2
val = 3
print(f"{val} * 2 is {double(val)}")
~~~

调试器的提示符为 `(Pdb)`，这指明你正处于调试模式下:

    
    
~~~
> ...(3)double()
-> return x * 2
(Pdb) p x
3
(Pdb) continue
3 * 2 is 6
~~~

在 3.3 版本发生变更: 由 [`readline`](readline.md#module-readline "readline: GNU readline support for Python. \(Unix\)") 模块实现的 Tab 补全可用于补全本模块的命令和命令的参数，例如，Tab 补全会提供当前的全局变量和局部变量，用作 `p` 命令的参数。

你还可以从命令行发起调用 `pdb` 来调试其他脚本。 例如:

    
    
~~~
python -m pdb myscript.py
~~~

当作为模块发起调用时，如果被调试的程序异常退出则 pdb 将自动进入事后调试。 在事后调试之后（或程序正常退出之后），pdb 将重启程序。 自动重启会保留 pdb 的状态（如断点）并且在大多数情况下这比在退出程序的同时退出调试器更实用。

在 3.2 版本加入: `-c` 被引入用来执行如同在 `.pdbrc` 文件中给出的命令，参见 调试器命令。

在 3.7 版本加入: `-m` 选项被引入用来以类似 `python -m` 的方式执行模块。 就像一个脚本那样，调试器将在模块的第一行之前暂停执行。

在调试器控制下执行一条语句的典型用法如下:

    
    
~~~shell
>>> import pdb
>>> def f(x):
...     print(1 / x)
>>> pdb.run("f(2)")
> <string>(1)<module>()
(Pdb) continue
0.5
>>>
~~~

检查已崩溃程序的典型用法是:

    
    
~~~shell
>>> import pdb
>>> def f(x):
...     print(1 / x)
...
>>> f(0)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 2, in f
ZeroDivisionError: division by zero
>>> pdb.pm()
> <stdin>(2)f()
(Pdb) p x
0
(Pdb)
~~~

本模块定义了下列函数，每个函数进入调试器的方式略有不同：

pdb.run( _statement_ , _globals =None_, _locals =None_)¶

    

~~~
在调试器控制范围内执行 _statement_ （以字符串或代码对象的形式提供）。调试器提示符会在执行代码前出现，你可以设置断点并键入 `continue`，也可以使用 `step` 或 `next` 逐步执行语句（上述所有命令在后文有说明）。可选参数 _globals_ 和 _locals_ 指定代码执行环境，默认时使用 [`__main__`](__main__.md#module-__main__ "__main__: The environment where top-level code is run. Covers command-line interfaces, import-time behavior, and ``__name__ == '__main__'``.") 模块的字典。（请参阅内置函数 [`exec()`](functions.md#exec "exec") 或 [`eval()`](functions.md#eval "eval") 的说明。）

pdb.runeval( _expression_ , _globals =None_, _locals =None_)¶
~~~
    

~~~
在调试器控制下对 _expression_ (以字符串或代码对象的形式给出) 求值。 当 `runeval()` 返回时，它将返回 _expression_ 的值。 在其他方面此函数与 `run()` 类似。

pdb.runcall( _function_ , _* args_, _** kwds_)¶
~~~
    

~~~
使用给定的参数调用 _function_ （以函数或方法对象的形式提供，不能是字符串）。`runcall()` 返回的是所调用函数的返回值。调试器提示符将在进入函数后立即出现。

pdb.set_trace( _*_ , _header =None_)¶
~~~
    

~~~
在调用本函数的堆栈帧处进入调试器。用于硬编码一个断点到程序中的固定点处，即使该代码不在调试状态（如断言失败时）。如果传入 _header_ ，它将在调试开始前被打印到控制台。

在 3.7 版本发生变更: 仅关键字参数 _header_ 。

pdb.post_mortem( _traceback =None_)¶
~~~
    

~~~
进入 _traceback_ 对象的事后调试。如果没有给定 _traceback_ ，默认使用当前正在处理的异常之一（默认时，必须存在正在处理的异常）。

pdb.pm()¶
~~~
    

~~~
Enter post-mortem debugging of the exception found in [`sys.last_exc`](sys.md#sys.last_exc "sys.last_exc").

`run*` 函数和 `set_trace()` 都是别名，用于实例化 `Pdb` 类和调用同名方法。如果要使用其他功能，则必须自己执行以下操作：

_class _pdb.Pdb( _completekey ='tab'_, _stdin =None_, _stdout =None_, _skip =None_, _nosigint =False_, _readrc =True_)¶
~~~
    

~~~
`Pdb` 是调试器类。

_completekey_ 、 _stdin_ 和 _stdout_ 参数都会传递给底层的 [`cmd.Cmd`](cmd.md#cmd.Cmd "cmd.Cmd") 类，请参考相应的描述。

如果给出 _skip_ 参数，则它必须是一个迭代器，可以迭代出 glob-style 样式的模块名称。如果遇到匹配上述样式的模块，调试器将不会进入来自该模块的堆栈帧。 [1]

默认情况下，当发出 `continue` 命令时，Pdb 将为 SIGINT 信号（信号当用户在控制台按 ``Ctrl`-`C`` 时发出的）设置一个处理句柄。 这使用户可以通过按 ``Ctrl`-`C`` 再次进入调试器。 如果你希望 Pdb 不要改变 SIGINT 处理句柄，请将 _nosigint_ 设为真值。to true.

_readrc_ 参数默认为 true，它控制 Pdb 是否从文件系统加载 .pdbrc 文件。

启用跟踪且带有 _skip_ 参数的调用示范:
~~~
    
    
~~~
import pdb; pdb.Pdb(skip=['django.*']).set_trace()
~~~

引发一个 [审计事件](3.标准库/sys.md#auditing) `pdb.Pdb`，没有附带参数。

在 3.1 版本加入: _skip_ 参数。

在 3.2 版本加入: _nosigint_ 参数。在这之前，Pdb 不为 SIGINT 设置处理程序。

在 3.6 版本发生变更: _readrc_ 参数。

run( _statement_ , _globals =None_, _locals =None_)¶

runeval( _expression_ , _globals =None_, _locals =None_)¶

runcall( _function_ , _* args_, _** kwds_)¶

set_trace()¶

    

~~~
请参阅上文解释同名函数的文档。

## 调试器命令¶

下方列出的是调试器可接受的命令。如下所示，大多数命令可以缩写为一个或两个字母。如 `h(elp)` 表示可以输入 `h` 或 `help` 来输入帮助命令（但不能输入 `he` 或 `hel`，也不能是 `H` 或 `Help` 或 `HELP`）。命令的参数必须用空格（空格符或制表符）分隔。在命令语法中，可选参数括在方括号 (`[]`) 中，使用时请勿输入方括号。命令语法中的选择项由竖线 (`|`) 分隔。

输入一个空白行将重复最后输入的命令。例外：如果最后一个命令是 `list` 命令，则会列出接下来的 11 行。

调试器无法识别的命令将被认为是 Python 语句，并在正在调试的程序的上下文中执行。Python 语句也可以用感叹号 (`!`) 作为前缀。这是检查正在调试的程序的强大方法，甚至可以修改变量或调用函数。当此类语句发生异常，将打印异常名称，但调试器的状态不会改变。

在 3.13 版本发生变更: Expressions/Statements whose prefix is a pdb command are now correctly identified and executed.

调试器支持 别名。别名可以有参数，使得调试器对被检查的上下文有一定程度的适应性。

在一行中可以输入多条命令，以 `;;` 分隔。 （不能使用单个 `;`，因为它已被用作传给 Python 解析器的一行中的多条命令的分隔符。） 命令切分所用的方式没有任何智能可言；输入总是会在第一个 `;;` 对上被切分，即使它位于带引号的字符串中。 对于带有双分号的字符串可以使用隐式字符串拼接 `';'';'` 或 `";"";"` 来变通处理。

要设置临时全局变量，请使用 _快捷变量_ 。 _快捷变量_ 是名称以 `$` 打头的变量。 例如，`$foo = 1` 将设置一个全局变量 `$foo` 供你在调试器会话中使用。 _快捷变量_ 会在程序恢复执行时被清空因此它不大可能像使用普通变量如 `foo = 1` 那样影响到你的程序。

存在三个预设的 _快捷变量_ :

  * `$_frame`: 你正在调试的当前帧

  * `$_retval`: 当帧返回时的返回值

  * `$_exception`: 当帧引发异常时的异常值

在 3.12 版本加入.

如果文件 `.pdbrc` 存在于用户主目录或当前目录中，则它将以 `'utf-8'` 编码格式被读入并执行，就像在调试器提示符下被键入一样。 这对于别名特别有用。 若两个文件都存在则首先读取主目录中的文件且在那里定义的别名可以被本地文件所覆盖。

在 3.11 版本发生变更: `.pdbrc` 现在将以 `'utf-8'` 编码格式来读取。 在之前版本中，它是以系统语言区域编码格式来读取的。

在 3.2 版本发生变更: `.pdbrc` 现在可以包含继续调试的命令，如 `continue` 或 `next`。文件中的这些命令以前是无效的。

h(elp) [command]¶
~~~
    

~~~
不带参数时，显示可用的命令列表。参数为 _command_ 时，打印有关该命令的帮助。`help pdb` 显示完整文档（即 `pdb` 模块的文档字符串）。由于 _command_ 参数必须是标识符，因此要获取 `!` 的帮助必须输入 `help exec`。

w(here)¶
~~~
    

~~~
抢鲜栈回溯，最新的帧位于底部。 有一个箭头 (`>`) 指明当前帧，该帧决定了大多数命令的上下文。

d(own) [count]¶
~~~
    

~~~
在堆栈回溯中，将当前帧向下移动 _count_ 级（默认为 1 级，移向更新的帧）。

u(p) [count]¶
~~~
    

~~~
在堆栈回溯中，将当前帧向上移动 _count_ 级（默认为 1 级，移向更老的帧）。

b(reak) [([filename:]lineno | function) [, condition]]¶
~~~
    

~~~
如果带有 _lineno_ 参数，则在当前文件相应行处设置一个断点。如果带有 _function_ 参数，则在该函数的第一条可执行语句处设置一个断点。行号可以加上文件名和冒号作为前缀，以在另一个文件（可能是尚未加载的文件）中设置一个断点。另一个文件将在 [`sys.path`](sys.md#sys.path "sys.path") 范围内搜索。请注意，每个断点都分配有一个编号，其他所有断点命令都引用该编号。

如果第二个参数存在，它应该是一个表达式，且它的计算值为 true 时断点才起作用。

如果不带参数执行，将列出所有中断，包括每个断点、命中该断点的次数、当前的忽略次数以及关联的条件（如果有）。

tbreak [([filename:]lineno | function) [, condition]]¶
~~~
    

~~~
临时断点，在第一次命中时会自动删除。它的参数与 `break` 相同。

cl(ear) [filename:lineno | bpnumber ...]¶
~~~
    

~~~
如果参数是 _filename:lineno_ ，则清除此行上的所有断点。如果参数是空格分隔的断点编号列表，则清除这些断点。如果不带参数，则清除所有断点（但会先提示确认）。

disable bpnumber [bpnumber ...]¶
~~~
    

~~~
禁用断点，断点以空格分隔的断点编号列表给出。禁用断点表示它不会导致程序停止执行，但是与清除断点不同，禁用的断点将保留在断点列表中并且可以（重新）启用。

enable bpnumber [bpnumber ...]¶
~~~
    

~~~
启用指定的断点。

ignore bpnumber [count]¶
~~~
    

~~~
为指定的断点编号设置忽略次数。 如果省略 _count_ ，则忽略次数将设置为 0。 当忽略次数为零时断点将变为活动状态。 如果为非零值，则在每次到达断点且断点未禁用且关联条件取真值时 _count_ 就像递减。

condition bpnumber [condition]¶
~~~
    

~~~
为断点设置一个新 _condition_ ，它是一个表达式，且它的计算值为 true 时断点才起作用。如果没有给出 _condition_ ，则删除现有条件，也就是将断点设为无条件。

commands [bpnumber]¶
~~~
    

~~~
为编号是 _bpnumber_ 的断点指定一系列命令。命令内容将显示在后续的几行中。输入仅包含 `end` 的行来结束命令列表。举个例子:
~~~
    
    
~~~
(Pdb) commands 1
(com) p some_variable
(com) end
(Pdb)
~~~

要删除断点上的所有命令，请输入 `commands` 并立即以 `end` 结尾，也就是不指定任何命令。

如果不带 _bpnumber_ 参数，`commands` 作用于最后一个被设置的断点。

可以为断点指定命令来重新启动程序。只需使用 `continue` 或 `step` 命令或其他可以继续运行程序的命令。

如果指定了某个继续运行程序的命令（目前包括 `continue`, `step`, `next`, `return`, `jump`, `quit` 及它们的缩写）将终止命令列表（就像该命令后紧跟着 end）。因为在任何时候继续运行下去（即使是简单的 next 或 step），都可能会遇到另一个断点，该断点可能具有自己的命令列表，这导致要执行的列表含糊不清。

如果在命令列表中使用 `silent` 命令，那么在断点处停下时就不会打印常规信息。 这正是要打印特定消息然后继续运行的断点所想要的。 如果没有其他命令来打印任何消息，则你将不会看到已到达断点的迹象。

s(tep)¶

    

~~~
运行当前行，在第一个可以停止的位置（在被调用的函数内部或在当前函数的下一行）停下。

n(ext)¶
~~~
    

~~~
继续运行，直到运行到当前函数的下一行，或当前函数返回为止。（ `next` 和 `step` 之间的区别在于，`step` 进入被调用函数内部并停止，而 `next` （几乎）全速运行被调用函数，仅在当前函数的下一行停止。）

unt(il) [lineno]¶
~~~
    

~~~
如果不带参数，则继续运行，直到行号比当前行大时停止。

如果带有 _lineno_ ，则继续执行直至行号大于或等于 _lineno_ 。 在这两种情况下，在当前帧返回时也将停止。

在 3.2 版本发生变更: 允许明确给定行号。

r(eturn)¶
~~~
    

~~~
继续运行，直到当前函数返回。

c(ont(inue))¶
~~~
    

~~~
继续运行，仅在遇到断点时停止。

j(ump) lineno¶
~~~
    

~~~
设置即将运行的下一行。仅可用于堆栈最底部的帧。它可以往回跳来再次运行代码，也可以往前跳来跳过不想运行的代码。

需要注意的是，不是所有的跳转都是允许的 -- 例如，不能跳转到 [`for`](../reference/compound_stmts.md#for) 循环的中间或跳出 [`finally`](../reference/compound_stmts.md#finally) 子句。

l(ist) [first[, last]]¶
~~~
    

~~~
列出当前文件的源代码。如果不带参数，则列出当前行周围的 11 行，或继续前一个列表。如果用 `.` 作为参数，则列出当前行周围的 11 行。如果带有一个参数，则列出那一行周围的 11 行。如果带有两个参数，则列出所给的范围中的代码；如果第二个参数小于第一个参数，则将其解释为列出行数的计数。

当前帧中的当前行用 `->` 标记。如果正在调试异常，且最早抛出或传递该异常的行不是当前行，则那一行用 `>>` 标记。

在 3.2 版本加入: `>>` 标记。

ll | longlist¶
~~~
    

~~~
列出当前函数或帧的所有源代码。相关行的标记与 `list` 相同。

在 3.2 版本加入.

a(rgs)¶
~~~
    

~~~
打印当前函数的参数及其当前的值。

p expression¶
~~~
    

~~~
在当前上下文中对 _expression_ 求值并打印该值。

备注

`print()` 也可以使用，但它不是一个调试器命令 --- 它执行 Python [`print()`](functions.md#print "print") 函数。

pp expression¶
~~~
    

~~~
与 `p` 命令类似，但 _expression_ 的值将使用 [`pprint`](pprint.md#module-pprint "pprint: Data pretty printer.") 模块美观地打印。

whatis expression¶
~~~
    

~~~
打印 _expression_ 的类型。

source expression¶
~~~
    

~~~
尝试获取 _expression_ 的源代码并显示它。

在 3.2 版本加入.

display [expression]¶
~~~
    

~~~
如果 _expression_ 的值发生变化则显示它的值，每次都会停止执行当前帧。

如果不带 _expression_ ，则列出当前帧的所有显示表达式。

备注

显示 _expression_ 的值并与 _expression_ 之前的求值结果进行比较，因此当结果可变时，显示可能无法体现变化。

示例：
~~~
    
    
~~~
lst = []
breakpoint()
pass
lst.append(1)
print(lst)
~~~

显示将不会发现 `lst` 已被改变因为求值结果在执行比较之前已被 `lst.append(1)` 原地修改了:

    
    
~~~
> example.py(3)<module>()
-> pass
(Pdb) display lst
display lst: []
(Pdb) n
> example.py(4)<module>()
-> lst.append(1)
(Pdb) n
> example.py(5)<module>()
-> print(lst)
(Pdb)
~~~

你可以通过拷贝机制巧妙地实现此功能:

    
    
~~~
> example.py(3)<module>()
-> pass
(Pdb) display lst[:]
display lst[:]: []
(Pdb) n
> example.py(4)<module>()
-> lst.append(1)
(Pdb) n
> example.py(5)<module>()
-> print(lst)
display lst[:]: [1]  [old: []]
(Pdb)
~~~

在 3.2 版本加入.

undisplay [expression]¶

    

~~~
不再显示当前帧中的 _expression_ 。 如果不带 _expression_ ，则清除当前帧的所有显示表达式。

在 3.2 版本加入.

interact¶
~~~
    

~~~
启动一个交互式解释器（使用 [`code`](code.md#module-code "code: Facilities to implement read-eval-print loops.") 模块），它的全局命名空间将包含当前作用域中的所有（全局和局部）名称。

在 3.2 版本加入.

alias [name [command]]¶
~~~
    

~~~
创建一个名为 _name_ 的别名用来执行 _command_ 。 _command_ 外边 _不可_ 带引号。 可替换形参可以用 `%1`, `%2` 等来指明，而 `%*` 将被所有形参所替换。 如果省略 _command_ ，则显示 _name_ 的当前别名。 如未给出任何参数，则列出所有别名。

别名允许嵌套并可包含能在 pdb 提示符下合法输入的任何内容。 请注意内部 pdb 命令 _可以_ 被别名所覆盖。 这样的命令将被隐藏直到别名被移除。 别名会递归地应用到命令行的第一个单词；行内的其他单词不会受影响。

作为示例，这里列出了两个有用的别名（特别适合放在 `.pdbrc` 文件中）:
~~~
    
    
~~~
# Print instance variables (usage "pi classInst")
alias pi for k in %1.__dict__.keys(): print(f"%1.{k} = {%1.__dict__[k]}")
# Print instance variables in self
alias ps pi self
~~~

unalias name¶

    

~~~
删除指定的别名 _name_ 。

! statement¶
~~~
    

~~~
在当前栈帧的上下文中执行 (单行的) _statement_ 。 感叹号可以被省略，除非第一个语句的第一个单词与某个调试器命名重名，例如:
~~~
    
    
~~~
(Pdb) ! n=42
(Pdb)
~~~

要设置全局变量，你可以在同一行上在赋值命令前添加 [`global`](7.%20简单语句.md#global) 语句，例如:

    
    
~~~
(Pdb) global list_options; list_options = ['-l']
(Pdb)
~~~

run [args ...]¶

restart [args ...]¶

    

~~~
重启被调试的 Python 程序。 如果提供了 _args_ ，它会用 [`shlex`](shlex.md#module-shlex "shlex: Simple lexical analysis for Unix shell-like languages.") 来拆分且拆分结果将被用作新的 [`sys.argv`](sys.md#sys.argv "sys.argv")。 历史、中断点、动作和调试器选项将被保留。 `restart` 是 `run` 的一个别名。

q(uit)¶
~~~
    

~~~
退出调试器。 被执行的程序将被中止。

debug code¶
~~~
    

~~~
进入一个对 _code_ 执行步进的递归调试器（该参数是在当前环境中执行的任意表达式或语句）。

retval¶
~~~
    

~~~
打印当前函数最后一次返回的返回值。

exceptions [excnumber]¶
~~~
    

~~~
List or jump between chained exceptions.

When using `pdb.pm()` or `Pdb.post_mortem(...)` with a chained exception instead of a traceback, it allows the user to move between the chained exceptions using `exceptions` command to list exceptions, and `exception <number>` to switch to that exception.

示例：
~~~
    
    
~~~python
def out():
    try:
        middle()
    except Exception as e:
        raise ValueError("reraise middle() error") from e

def middle():
    try:
        return inner(0)
    except Exception as e:
        raise ValueError("Middle fail")

def inner(x):
    1 / x

 out()
~~~

calling `pdb.pm()` will allow to move between exceptions:

    
    
~~~
> example.py(5)out()
-> raise ValueError("reraise middle() error") from e

(Pdb) exceptions
  0 ZeroDivisionError('division by zero')
  1 ValueError('Middle fail')
> 2 ValueError('reraise middle() error')

(Pdb) exceptions 0
> example.py(16)inner()
-> 1 / x

(Pdb) up
> example.py(10)middle()
-> return inner(0)
~~~

在 3.13 版本加入.

备注

[1]

一个帧是否会被认为源自特定模块是由帧全局变量 `__name__` 来决定的。

