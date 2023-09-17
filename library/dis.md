# `dis` \--- Python 字节码反汇编器¶

**Source code:** [Lib/dis.py](https://github.com/python/cpython/tree/3.12/Lib/dis.py)

* * *

`dis` 模块通过反汇编支持CPython的 [bytecode](../glossary.md#term-bytecode) 分析。该模块作为输入的 CPython 字节码在文件 `Include/opcode.h` 中定义，并由编译器和解释器使用。

**CPython 实现细节：** 字节码是 CPython 解释器的实现细节。不保证不会在Python版本之间添加、删除或更改字节码。不应考虑将此模块的跨 Python VM 或 Python 版本的使用。

在 3.6 版本发生变更: 每条指令使用2个字节。以前字节数因指令而异。

在 3.10 版本发生变更: 跳转、异常处理和循环指令的参数现在将为指令偏移量而不是字节偏移量。

在 3.11 版本发生变更: 有些指令带有一个或多个内联缓存条目，它们是采用 `CACHE` 指令的形式。 这些指令默认是隐藏的，但可以通过将 `show_caches=True` 传给任何 `dis` 工具对象来显示。 此外，解释器现在会适配字节码以使其能针对不同的运行时条件实现专门化。 适配的字节码可通过传入 `adaptive=True` 来显示。

示例：给定函数 `myfunc()`:

    
    
~~~python
def myfunc(alist):
    return len(alist)
~~~

可以使用以下命令显示 `myfunc()` 的反汇编：

    
    
~~~shell
>>> dis.dis(myfunc)
  2           0 RESUME                   0

  3           2 LOAD_GLOBAL              1 (len + NULL)
             12 LOAD_FAST                0 (alist)
             14 CALL                     1
             22 RETURN_VALUE
~~~

("2" 是行号)。

## 字节码分析¶

在 3.4 版本加入.

字节码分析 API 允许将 Python 代码片段包装在 `Bytecode` 对象中，以便轻松访问已编译代码的详细信息。

_class _dis.Bytecode( _x_ , _*_ , _first_line =None_, _current_offset =None_, _show_caches =False_, _adaptive =False_)¶

    

~~~
分析的字节码对应于函数、生成器、异步生成器、协程、方法、源代码字符串或代码对象（由 [`compile()`](functions.md#compile "compile") 返回）。

这是下面列出的许多函数的便利包装，最值得注意的是 `get_instructions()` ，迭代于 `Bytecode` 的实例产生字节码操作 `Instruction` 的实例。

如果 _first_line_ 不是 `None` ，则表示应该为反汇编代码中的第一个源代码行报告的行号。否则，源行信息（如果有的话）直接来自反汇编的代码对象。

如果 _current_offset_ 不是 `None` ，则它指的是反汇编代码中的指令偏移量。设置它意味着 `dis()` 将针对指定的操作码显示“当前指令”标记。

如果 _show_caches_ 为 `True`，`dis()` 将显示解释器用来专门化字节码的内联缓存条目。

如果 _adaptive_ 为 `True`，`dis()` 将显示可能不同于原始字节码的专门化字节码。

_classmethod _from_traceback( _tb_ , _*_ , _show_caches =False_)¶
~~~
    

~~~
从给定回溯构造一个 `Bytecode` 实例，将设置 _current_offset_ 为异常负责的指令。

codeobj¶
~~~
    

~~~
已编译的代码对象。

first_line¶
~~~
    

~~~
代码对象的第一个源代码行（如果可用）

dis()¶
~~~
    

~~~
返回字节码操作的格式化视图（与 `dis.dis()` 打印相同，但作为多行字符串返回）。

info()¶
~~~
    

~~~
返回带有关于代码对象的详细信息的格式化多行字符串，如 `code_info()` 。

在 3.7 版本发生变更: 现在可以处理协程和异步生成器对象。

在 3.11 版本发生变更: 增加了 _show_caches_ 和 _adaptive_ 形参。

示例:
~~~
    
    
~~~shell
>>> bytecode = dis.Bytecode(myfunc)
>>> for instr in bytecode:
...     print(instr.opname)
...
RESUME
LOAD_GLOBAL
LOAD_FAST
CALL
RETURN_VALUE
~~~

## 分析函数¶

`dis` 模块还定义了以下分析函数，它们将输入直接转换为所需的输出。如果只执行单个操作，它们可能很有用，因此中间分析对象没用：

dis.code_info( _x_ )¶

    

~~~
返回格式化的多行字符串，其包含详细代码对象信息的用于被提供的函数、生成器、异步生成器、协程、方法、源代码字符串或代码对象。

请注意，代码信息字符串的确切内容是高度依赖于实现的，它们可能会在Python VM或Python版本中任意更改。

在 3.2 版本加入.

在 3.7 版本发生变更: 现在可以处理协程和异步生成器对象。

dis.show_code( _x_ , _*_ , _file =None_)¶
~~~
    

~~~
将提供的函数、方法。源代码字符串或代码对象的详细代码对象信息打印到 _file_ （如果未指定 _file_ ，则为 `sys.stdout` ）。

这是 `print(code_info(x), file=file)` 的便捷简写，用于在解释器提示符下进行交互式探索。

在 3.2 版本加入.

在 3.4 版本发生变更: 添加 _file_ 形参。

dis.dis( _x =None_, _*_ , _file =None_, _depth =None_, _show_caches =False_, _adaptive =False_)¶
~~~
    

~~~
反汇编 _x_ 对象。 _x_ 可以表示模块、类、方法、函数、生成器、异步生成器、协程、代码对象、源代码字符串或原始字节码的字节序列。 对于模块，它会反汇编所有函数。 对于一个类，它会反汇编所有方法（包括类方法和静态方法）。 对于代码对象或原始字节码序列，它会为每条字节码指令打印一行。 它还会递归地反汇编嵌套代码对象。 这些对象包括生成器表达式、嵌套函数、嵌套类的语句体以及用于 [标注作用域](../reference/executionmodel.md#annotation-scopes) 的代码对象。 在反汇编之前，首先使用 [`compile()`](functions.md#compile "compile") 内置函数将字符串编译为代码对象。 如果未提供任何对象，则该函数将反汇编最后一次回溯。

如果提供的话，反汇编将作为文本写入提供的 _file_ 参数，否则写入 `sys.stdout` 。

递归的最大深度受 _depth_ 限制，除非它是 `None` 。 `depth=0` 表示没有递归。

如果 _show_caches_ 为 `True`，此函数将显示解释器用来专门化字节码的内联缓存条目。

如果 _adaptive_ 为 `True`，此函数将显示可能不同于原始字节码的专门化字节码。

在 3.4 版本发生变更: 添加 _file_ 形参。

在 3.7 版本发生变更: 实现了递归反汇编并添加了 _depth_ 参数。

在 3.7 版本发生变更: 现在可以处理协程和异步生成器对象。

在 3.11 版本发生变更: 增加了 _show_caches_ 和 _adaptive_ 形参。

dis.distb( _tb =None_, _*_ , _file =None_, _show_caches =False_, _adaptive =False_)¶
~~~
    

~~~
如果没有传递，则使用最后一个回溯来反汇编回溯的堆栈顶部函数。 指示了导致异常的指令。

如果提供的话，反汇编将作为文本写入提供的 _file_ 参数，否则写入 `sys.stdout` 。

在 3.4 版本发生变更: 添加 _file_ 形参。

在 3.11 版本发生变更: 增加了 _show_caches_ 和 _adaptive_ 形参。

dis.disassemble( _code_ , _lasti =-1_, _*_ , _file =None_, _show_caches =False_, _adaptive =False_)¶

dis.disco( _code_ , _lasti =-1_, _*_ , _file =None_, _show_caches =False_, _adaptive =False_)¶
~~~
    

~~~
反汇编代码对象，如果提供了 _lasti_ ，则指示最后一条指令。输出分为以下几列：

  1. 行号，用于每行的第一条指令

  2. 当前指令，表示为 `-->` ，

  3. 一个标记的指令，用 `>>` 表示，

  4. 指令的地址，

  5. 操作码名称，

  6. 操作参数，和

  7. 括号中参数的解释。

参数解释识别本地和全局变量名称、常量值、分支目标和比较运算符。

如果提供的话，反汇编将作为文本写入提供的 _file_ 参数，否则写入 `sys.stdout` 。

在 3.4 版本发生变更: 添加 _file_ 形参。

在 3.11 版本发生变更: 增加了 _show_caches_ 和 _adaptive_ 形参。

dis.get_instructions( _x_ , _*_ , _first_line =None_, _show_caches =False_, _adaptive =False_)¶
~~~
    

~~~
在所提供的函数、方法、源代码字符串或代码对象中的指令上返回一个迭代器。

迭代器生成一系列 `Instruction` ，命名为元组，提供所提供代码中每个操作的详细信息。

如果 _first_line_ 不是 `None` ，则表示应该为反汇编代码中的第一个源代码行报告的行号。否则，源行信息（如果有的话）直接来自反汇编的代码对象。

_show_caches_ 和 _adaptive_ 形参的作用与 `dis()` 中的同名形参相同。

在 3.4 版本加入.

在 3.11 版本发生变更: 增加了 _show_caches_ 和 _adaptive_ 形参。

dis.findlinestarts( _code_ )¶
~~~
    

~~~
这个生成器函数使用代码对象 _code_ 的 `co_lines` 方法来查找源代码中行开头的偏移量。 它们将作为 `(offset, lineno)` 对被生成。

在 3.6 版本发生变更: 行号可能会减少。 以前，他们总是在增加。

在 3.10 版本发生变更: 使用 [**PEP 626**](https://peps.python.org/pep-0626/) `co_lines` 方法而不是代码对象的 `co_firstlineno` 和 `co_lnotab` 属性。

在 3.13 版本发生变更: Line numbers can be `None` for bytecode that does not map to source lines.

dis.findlabels( _code_ )¶
~~~
    

~~~
检测作为跳转目标的原始编译后字节码字符串 _code_ 中的所有偏移量，并返回这些偏移量的列表。

dis.stack_effect( _opcode_ , _oparg =None_, _*_ , _jump =None_)¶
~~~
    

~~~
使用参数 _oparg_ 计算 _opcode_ 的堆栈效果。

如果代码有一个跳转目标并且 _jump_ 是 `True` ，则 `drag_effect()` 将返回跳转的堆栈效果。如果 _jump_ 是 `False` ，它将返回不跳跃的堆栈效果。如果 _jump_ 是 `None` （默认值），它将返回两种情况的最大堆栈效果。

在 3.4 版本加入.

在 3.8 版本发生变更: 添加 _jump_ 参数。

在 3.13 版本发生变更: If `oparg` is omitted (or `None`), the stack effect is now returned for `oparg=0`. Previously this was an error for opcodes that use their arg. It is also no longer an error to pass an integer `oparg` when the `opcode` does not use it; the `oparg` in this case is ignored.

## Python字节码说明¶

`get_instructions()` 函数和 `Bytecode` 类提供字节码指令的详细信息的 `Instruction` 实例：

_class _dis.Instruction¶
~~~
    

~~~
字节码操作的详细信息

opcode¶
~~~
    

~~~
操作的数字代码，对应于下面列出的操作码值和 操作码集合 中的字节码值。

opname¶
~~~
    

~~~
人类可读的操作名称

baseopcode¶
~~~
    

~~~
numeric code for the base operation if operation is specialized; otherwise equal to `opcode`

baseopname¶
~~~
    

~~~
human readable name for the base operation if operation is specialized; otherwise equal to `opname`

arg¶
~~~
    

~~~
操作的数字参数（如果有的话），否则为 `None`

oparg¶
~~~
    

~~~
alias for `arg`

argval¶
~~~
    

~~~
已解析的 arg 值（如果有的话），否则为 `None`

argrepr¶
~~~
    

~~~
人类可读的操作参数（如果存在）的描述，否则为空字符串。

offset¶
~~~
    

~~~
在字节码序列中启动操作索引

start_offset¶
~~~
    

~~~
start index of operation within bytecode sequence, including prefixed `EXTENDED_ARG` operations if present; otherwise equal to `offset`

cache_offset¶
~~~
    

~~~
start index of the cache entries following the operation

end_offset¶
~~~
    

~~~
end index of the cache entries following the operation

starts_line¶
~~~
    

~~~
`True` if this opcode starts a source line, otherwise `False`

line_number¶
~~~
    

~~~
source line number associated with this opcode (if any), otherwise `None`

is_jump_target¶
~~~
    

~~~
如果其他代码跳到这里，则为 `True` ，否则为 `False`

jump_target¶
~~~
    

~~~
bytecode index of the jump target if this is a jump operation, otherwise `None`

positions¶
~~~
    

~~~
`dis.Positions` 对象保存了这条指令所涵盖的起始和结束位置。

在 3.4 版本加入.

在 3.11 版本发生变更: 增加了 `positions` 字段。

在 3.13 版本发生变更: Changed field `starts_line`.

Added fields `start_offset`, `cache_offset`, `end_offset`, `baseopname`, `baseopcode`, `jump_target`, `oparg`, and `line_number`.

_class _dis.Positions¶
~~~
    

~~~
考虑到此信息不可用的情况，某些字段可能为 `None`。

lineno¶
~~~
    

~~~
end_lineno¶
~~~
    

~~~
col_offset¶
~~~
    

~~~
end_col_offset¶
~~~
    

~~~
在 3.11 版本加入.

Python编译器当前生成以下字节码指令。

**一般指令**

在下文中，我们将把解释器栈称为 `STACK` 并像描述 Python 列表一样描述对它的操作。 栈顶对应于该语言中的 `STACK[-1]`。

NOP¶
~~~
    

~~~
无操作代码。 被字节码优化器用作占位符，以及生成行追踪事件。

POP_TOP¶
~~~
    

~~~
Removes the top-of-stack item:
~~~
    
    
~~~
STACK.pop()
~~~

END_FOR¶

    

~~~
Removes the top two values from the stack. Equivalent to `POP_TOP`; `POP_TOP`. Used to clean up at the end of loops, hence the name.

在 3.12 版本加入.

COPY( _i_ )¶
~~~
    

~~~
Push the i-th item to the top of the stack without removing it from its original location:
~~~
    
    
~~~
assert i > 0
STACK.append(STACK[-i])
~~~

在 3.11 版本加入.

SWAP( _i_ )¶

    

~~~
Swap the top of the stack with the i-th element:
~~~
    
    
~~~
STACK[-i], STACK[-1] = stack[-1], STACK[-i]
~~~

在 3.11 版本加入.

CACHE¶

    

~~~
此操作码不是真正的指令，它被用来为解释器标记额外空间以便在字节码中直接缓存有用的数据。 它会被所有 `dis` 工具自动隐藏，但可以通过 `show_caches=True` 来查看。

从逻辑上说，此空间是之前的指令的组成部分。 许多操作码都预期带有固定数量的缓存，并会指示解释器在运行时跳过它们。

被填充的缓存看起来可以像是任意的指令，因此在读取或修改包含快取数据的原始自适应字节码时应当非常小心。

在 3.11 版本加入.

**一元操作**

一元操作获取堆栈顶部元素，应用操作，并将结果推回堆栈。

UNARY_NEGATIVE¶
~~~
    

~~~
Implements `STACK[-1] = -STACK[-1]`.

UNARY_NOT¶
~~~
    

~~~
Implements `STACK[-1] = not STACK[-1]`.

在 3.13 版本发生变更: This instruction now requires an exact [`bool`](functions.md#bool "bool") operand.

UNARY_INVERT¶
~~~
    

~~~
Implements `STACK[-1] = ~STACK[-1]`.

GET_ITER¶
~~~
    

~~~
Implements `STACK[-1] = iter(STACK[-1])`.

GET_YIELD_FROM_ITER¶
~~~
    

~~~
If `STACK[-1]` is a [generator iterator](../glossary.md#term-generator-iterator) or [coroutine](../glossary.md#term-coroutine) object it is left as is. Otherwise, implements `STACK[-1] = iter(STACK[-1])`.

在 3.5 版本加入.

TO_BOOL¶
~~~
    

~~~
Implements `STACK[-1] = bool(STACK[-1])`.

在 3.13 版本加入.

**双目和原地操作**

Binary operations remove the top two items from the stack (`STACK[-1]` and `STACK[-2]`). They perform the operation, then put the result back on the stack.

In-place operations are like binary operations, but the operation is done in-place when `STACK[-2]` supports it, and the resulting `STACK[-1]` may be (but does not have to be) the original `STACK[-2]`.

BINARY_OP( _op_ )¶
~~~
    

~~~
Implements the binary and in-place operators (depending on the value of _op_ ):
~~~
    
    
~~~
rhs = STACK.pop()
lhs = STACK.pop()
STACK.append(lhs op rhs)
~~~

在 3.11 版本加入.

BINARY_SUBSCR¶

    

~~~
Implements:
~~~
    
    
~~~
key = STACK.pop()
container = STACK.pop()
STACK.append(container[key])
~~~

STORE_SUBSCR¶

    

~~~
Implements:
~~~
    
    
~~~
key = STACK.pop()
container = STACK.pop()
value = STACK.pop()
container[key] = value
~~~

DELETE_SUBSCR¶

    

~~~
Implements:
~~~
    
    
~~~
key = STACK.pop()
container = STACK.pop()
del container[key]
~~~

BINARY_SLICE¶

    

~~~
Implements:
~~~
    
    
~~~
end = STACK.pop()
start = STACK.pop()
container = STACK.pop()
STACK.append(container[start:end])
~~~

在 3.12 版本加入.

STORE_SLICE¶

    

~~~
Implements:
~~~
    
    
~~~
end = STACK.pop()
start = STACK.pop()
container = STACK.pop()
values = STACK.pop()
container[start:end] = value
~~~

在 3.12 版本加入.

**协程操作码**

GET_AWAITABLE( _where_ )¶

    

~~~
Implements `STACK[-1] = get_awaitable(STACK[-1])`, where `get_awaitable(o)` returns `o` if `o` is a coroutine object or a generator object with the [`CO_ITERABLE_COROUTINE`](inspect.md#inspect.CO_ITERABLE_COROUTINE "inspect.CO_ITERABLE_COROUTINE") flag, or resolves `o.__await__`.

> 如果 `where` 操作数为非零值，则表示指令所在的位置:
>
>   * `1`: After a call to `__aenter__`
>
>   * `2`: After a call to `__aexit__`
>
>

在 3.5 版本加入.

在 3.11 版本发生变更: 在之前版本中，该指令没有 oparg。

GET_AITER¶
~~~
    

~~~
Implements `STACK[-1] = STACK[-1].__aiter__()`.

在 3.5 版本加入.

在 3.7 版本发生变更: 已经不再支持从 `__aiter__` 返回可等待对象。

GET_ANEXT¶
~~~
    

~~~
Implement `STACK.append(get_awaitable(STACK[-1].__anext__()))` to the stack. See `GET_AWAITABLE` for details about `get_awaitable`.

在 3.5 版本加入.

END_ASYNC_FOR¶
~~~
    

~~~
Terminates an [`async for`](../reference/compound_stmts.md#async-for) loop. Handles an exception raised when awaiting a next item. The stack contains the async iterable in `STACK[-2]` and the raised exception in `STACK[-1]`. Both are popped. If the exception is not [`StopAsyncIteration`](exceptions.md#StopAsyncIteration "StopAsyncIteration"), it is re-raised.

在 3.8 版本加入.

在 3.11 版本发生变更: 栈中的异常表示形式现在将由一个而不是三个条目组成。

CLEANUP_THROW¶
~~~
    

~~~
Handles an exception raised during a [`throw()`](../reference/expressions.md#generator.throw "generator.throw") or [`close()`](../reference/expressions.md#generator.close "generator.close") call through the current frame. If `STACK[-1]` is an instance of [`StopIteration`](exceptions.md#StopIteration "StopIteration"), pop three values from the stack and push its `value` member. Otherwise, re-raise `STACK[-1]`.

在 3.12 版本加入.

BEFORE_ASYNC_WITH¶
~~~
    

~~~
Resolves `__aenter__` and `__aexit__` from `STACK[-1]`. Pushes `__aexit__` and result of `__aenter__()` to the stack:
~~~
    
    
~~~
STACK.extend((__aexit__, __aenter__())
~~~

在 3.5 版本加入.

**其他操作码**

SET_ADD( _i_ )¶

    

~~~
Implements:
~~~
    
    
~~~
item = STACK.pop()
set.add(STACK[-i], item)
~~~

Used to implement set comprehensions.

LIST_APPEND( _i_ )¶

    

~~~
Implements:
~~~
    
    
~~~
item = STACK.pop()
list.append(STACK[-i], item)
~~~

Used to implement list comprehensions.

MAP_ADD( _i_ )¶

    

~~~
Implements:
~~~
    
    
~~~
value = STACK.pop()
key = STACK.pop()
dict.__setitem__(STACK[-i], key, value)
~~~

Used to implement dict comprehensions.

在 3.1 版本加入.

在 3.8 版本发生变更: Map value is `STACK[-1]` and map key is `STACK[-2]`. Before, those were reversed.

对于所有 `SET_ADD` 、 `LIST_APPEND` 和 `MAP_ADD` 指令，当弹出添加的值或键值对时，容器对象保留在堆栈上，以便它可用于循环的进一步迭代。

RETURN_VALUE¶

    

~~~
Returns with `STACK[-1]` to the caller of the function.

RETURN_CONST( _consti_ )¶
~~~
    

~~~
Returns with `co_consts[consti]` to the caller of the function.

在 3.12 版本加入.

YIELD_VALUE¶
~~~
    

~~~
Yields `STACK.pop()` from a [generator](../glossary.md#term-generator).

在 3.11 版本发生变更: oparg set to be the stack depth.

在 3.12 版本发生变更: oparg set to be the exception block depth, for efficient closing of generators.

SETUP_ANNOTATIONS¶
~~~
    

~~~
检查 `__annotations__` 是否在 `locals()` 中定义，如果没有，它被设置为空 `dict` 。只有在类或模块体静态地包含 [variable annotations](../glossary.md#term-variable-annotation) 时才会发出此操作码。

在 3.6 版本加入.

POP_EXCEPT¶
~~~
    

~~~
从栈中弹出一个值，它将被用来恢复异常状态。

在 3.11 版本发生变更: 栈中的异常表示形式现在将由一个而不是三个条目组成。

RERAISE¶
~~~
    

~~~
重新引发当前位于栈顶的异常。 如果 oparg 为非零值，则将从栈中弹出一个额外的值，它会被用来设置当前帧的 `f_lasti`。

在 3.9 版本加入.

在 3.11 版本发生变更: 栈中的异常表示形式现在将由一个而不是三个条目组成。

PUSH_EXC_INFO¶
~~~
    

~~~
从栈中弹出一个值。 将当前异常推入栈顶。 将原先被弹出的值推回栈。 在异常处理句柄中使用。

在 3.11 版本加入.

CHECK_EXC_MATCH¶
~~~
    

~~~
Performs exception matching for `except`. Tests whether the `STACK[-2]` is an exception matching `STACK[-1]`. Pops `STACK[-1]` and pushes the boolean result of the test.

在 3.11 版本加入.

CHECK_EG_MATCH¶
~~~
    

~~~
Performs exception matching for `except*`. Applies `split(STACK[-1])` on the exception group representing `STACK[-2]`.

在匹配的情况下，从栈中弹出两项并推入不匹配的子分组 (如完全匹配则为 `None`) 以及匹配的子分组。 当没有任何匹配时，则弹出一项 (匹配类型) 并推入 `None`。

在 3.11 版本加入.

WITH_EXCEPT_START¶
~~~
    

~~~
调用栈中 4 号位置上的函数并附带代表位于栈顶的异常的参数 (type, val, tb)。 用于在 [`with`](../reference/compound_stmts.md#with) 语句内发生异常时实现调用 `context_manager.__exit__(*exc_info())`。

在 3.9 版本加入.

在 3.11 版本发生变更: `__exit__` 函数位于栈的 4 号位而不是 7 号位。 栈中的异常表示形式现在由一项而不是三项组成。

LOAD_ASSERTION_ERROR¶
~~~
    

~~~
将 [`AssertionError`](exceptions.md#AssertionError "AssertionError") 推入栈顶。 由 [`assert`](../reference/simple_stmts.md#assert) 语句使用。

在 3.9 版本加入.

LOAD_BUILD_CLASS¶
~~~
    

~~~
Pushes `builtins.__build_class__()` onto the stack. It is later called to construct a class.

BEFORE_WITH¶
~~~
    

~~~
此操作码会在 with 代码块开始之前执行多个操作。 首先，它将从上下文管理器加载 [`__exit__()`](../reference/datamodel.md#object.__exit__ "object.__exit__") 并将其推入栈顶以供 `WITH_EXCEPT_START` 后续使用。 然后，将调用 [`__enter__()`](../reference/datamodel.md#object.__enter__ "object.__enter__")。 最后，将调用 `__enter__()` 方法的结果推入栈顶。

在 3.11 版本加入.

GET_LEN¶
~~~
    

~~~
Perform `STACK.append(len(STACK[-1]))`.

在 3.10 版本加入.

MATCH_MAPPING¶
~~~
    

~~~
If `STACK[-1]` is an instance of [`collections.abc.Mapping`](collections.abc.md#collections.abc.Mapping "collections.abc.Mapping") (or, more technically: if it has the [`Py_TPFLAGS_MAPPING`](../c-api/typeobj.md#c.Py_TPFLAGS_MAPPING "Py_TPFLAGS_MAPPING") flag set in its [`tp_flags`](../c-api/typeobj.md#c.PyTypeObject.tp_flags "PyTypeObject.tp_flags")), push `True` onto the stack. Otherwise, push `False`.

在 3.10 版本加入.

MATCH_SEQUENCE¶
~~~
    

~~~
If `STACK[-1]` is an instance of [`collections.abc.Sequence`](collections.abc.md#collections.abc.Sequence "collections.abc.Sequence") and is _not_ an instance of [`str`](stdtypes.md#str "str")/[`bytes`](stdtypes.md#bytes "bytes")/[`bytearray`](stdtypes.md#bytearray "bytearray") (or, more technically: if it has the [`Py_TPFLAGS_SEQUENCE`](../c-api/typeobj.md#c.Py_TPFLAGS_SEQUENCE "Py_TPFLAGS_SEQUENCE") flag set in its [`tp_flags`](../c-api/typeobj.md#c.PyTypeObject.tp_flags "PyTypeObject.tp_flags")), push `True` onto the stack. Otherwise, push `False`.

在 3.10 版本加入.

MATCH_KEYS¶
~~~
    

~~~
`STACK[-1]` is a tuple of mapping keys, and `STACK[-2]` is the match subject. If `STACK[-2]` contains all of the keys in `STACK[-1]`, push a [`tuple`](stdtypes.md#tuple "tuple") containing the corresponding values. Otherwise, push `None`.

在 3.10 版本加入.

在 3.11 版本发生变更: 在之前的版本中，该指令还会推入一个表示成功 (`True`) 或失败 (`False`) 的布尔值。

STORE_NAME( _namei_ )¶
~~~
    

~~~
Implements `name = STACK.pop()`. _namei_ is the index of _name_ in the attribute `co_names` of the [code object](../reference/datamodel.md#code-objects). The compiler tries to use `STORE_FAST` or `STORE_GLOBAL` if possible.

DELETE_NAME( _namei_ )¶
~~~
    

~~~
Implements `del name`, where _namei_ is the index into `co_names` attribute of the [code object](../reference/datamodel.md#code-objects).

UNPACK_SEQUENCE( _count_ )¶
~~~
    

~~~
Unpacks `STACK[-1]` into _count_ individual values, which are put onto the stack right-to-left. Require there to be exactly _count_ values.:
~~~
    
    
~~~
assert(len(STACK[-1]) == count)
STACK.extend(STACK.pop()[:-count-1:-1])
~~~

UNPACK_EX( _counts_ )¶

    

~~~
Implements assignment with a starred target: Unpacks an iterable in `STACK[-1]` into individual values, where the total number of values can be smaller than the number of items in the iterable: one of the new values will be a list of all leftover items.

The number of values before and after the list value is limited to 255.

The number of values before the list value is encoded in the argument of the opcode. The number of values after the list if any is encoded using an `EXTENDED_ARG`. As a consequence, the argument can be seen as a two bytes values where the low byte of _counts_ is the number of values before the list value, the high byte of _counts_ the number of values after it.

The extracted values are put onto the stack right-to-left, i.e. `a, *b, c = d` will be stored after execution as `STACK.extend((a, b, c))`.

STORE_ATTR( _namei_ )¶
~~~
    

~~~
Implements:
~~~
    
    
~~~
obj = STACK.pop()
value = STACK.pop()
obj.name = value
~~~

where _namei_ is the index of name in `co_names` of the [code object](../reference/datamodel.md#code-objects).

DELETE_ATTR( _namei_ )¶

    

~~~
Implements:
~~~
    
    
~~~
obj = STACK.pop()
del obj.name
~~~

where _namei_ is the index of name into `co_names` of the [code object](../reference/datamodel.md#code-objects).

STORE_GLOBAL( _namei_ )¶

    

~~~
类似于 `STORE_NAME` 但会将 name 存储为全局变量。

DELETE_GLOBAL( _namei_ )¶
~~~
    

~~~
类似于 `DELETE_NAME` 但会删除一个全局变量。

LOAD_CONST( _consti_ )¶
~~~
    

~~~
将 `co_consts[consti]` 推入栈顶。

LOAD_NAME( _namei_ )¶
~~~
    

~~~
Pushes the value associated with `co_names[namei]` onto the stack. The name is looked up within the locals, then the globals, then the builtins.

LOAD_LOCALS¶
~~~
    

~~~
Pushes a reference to the locals dictionary onto the stack. This is used to prepare namespace dictionaries for `LOAD_FROM_DICT_OR_DEREF` and `LOAD_FROM_DICT_OR_GLOBALS`.

在 3.12 版本加入.

LOAD_FROM_DICT_OR_GLOBALS( _i_ )¶
~~~
    

~~~
Pops a mapping off the stack and looks up the value for `co_names[namei]`. If the name is not found there, looks it up in the globals and then the builtins, similar to `LOAD_GLOBAL`. This is used for loading global variables in [annotation scopes](../reference/executionmodel.md#annotation-scopes) within class bodies.

在 3.12 版本加入.

BUILD_TUPLE( _count_ )¶
~~~
    

~~~
Creates a tuple consuming _count_ items from the stack, and pushes the resulting tuple onto the stack.:
~~~
    
    
~~~
assert count > 0
STACK, values = STACK[:-count], STACK[-count:]
STACK.append(tuple(values))
~~~

BUILD_LIST( _count_ )¶

    

~~~
类似于 `BUILD_TUPLE` 但会创建一个列表。

BUILD_SET( _count_ )¶
~~~
    

~~~
类似于 `BUILD_TUPLE` 但会创建一个集合。

BUILD_MAP( _count_ )¶
~~~
    

~~~
Pushes a new dictionary object onto the stack. Pops `2 * count` items so that the dictionary holds _count_ entries: `{..., STACK[-4]: STACK[-3], STACK[-2]: STACK[-1]}`.

在 3.5 版本发生变更: 字典是根据栈中的项创建而不是创建一个预设大小包含 _count_ 项的空字典。

BUILD_CONST_KEY_MAP( _count_ )¶
~~~
    

~~~
The version of `BUILD_MAP` specialized for constant keys. Pops the top element on the stack which contains a tuple of keys, then starting from `STACK[-2]`, pops _count_ values to form values in the built dictionary.

在 3.6 版本加入.

BUILD_STRING( _count_ )¶
~~~
    

~~~
拼接 _count_ 个来自栈的字符串并将结果字符串推入栈顶。

在 3.6 版本加入.

LIST_EXTEND( _i_ )¶
~~~
    

~~~
Implements:
~~~
    
    
~~~
seq = STACK.pop()
list.extend(STACK[-i], seq)
~~~

Used to build lists.

在 3.9 版本加入.

SET_UPDATE( _i_ )¶

    

~~~
Implements:
~~~
    
    
~~~
seq = STACK.pop()
set.update(STACK[-i], seq)
~~~

Used to build sets.

在 3.9 版本加入.

DICT_UPDATE( _i_ )¶

    

~~~
Implements:
~~~
    
    
~~~
map = STACK.pop()
dict.update(STACK[-i], map)
~~~

Used to build dicts.

在 3.9 版本加入.

DICT_MERGE( _i_ )¶

    

~~~
类似于 `DICT_UPDATE` 但对于重复的键会引发异常。

在 3.9 版本加入.

LOAD_ATTR( _namei_ )¶
~~~
    

~~~
If the low bit of `namei` is not set, this replaces `STACK[-1]` with `getattr(STACK[-1], co_names[namei>>1])`.

If the low bit of `namei` is set, this will attempt to load a method named `co_names[namei>>1]` from the `STACK[-1]` object. `STACK[-1]` is popped. This bytecode distinguishes two cases: if `STACK[-1]` has a method with the correct name, the bytecode pushes the unbound method and `STACK[-1]`. `STACK[-1]` will be used as the first argument (`self`) by `CALL` or `CALL_KW` when calling the unbound method. Otherwise, `NULL` and the object returned by the attribute lookup are pushed.

在 3.12 版本发生变更: If the low bit of `namei` is set, then a `NULL` or `self` is pushed to the stack before the attribute or unbound method respectively.

LOAD_SUPER_ATTR( _namei_ )¶
~~~
    

~~~
This opcode implements [`super()`](functions.md#super "super") (e.g. `super().method()` and `super().attr`). It works the same as `LOAD_ATTR`, except that `namei` is shifted left by 2 bits instead of 1, and instead of expecting a single receiver on the stack, it expects three objects (from top of stack down): `self` (the first argument to the current method), `cls` (the class within which the current method was defined), and the global `super`.

The low bit of `namei` signals to attempt a method load, as with `LOAD_ATTR`.

The second-low bit of `namei`, if set, means that this was a two-argument call to [`super()`](functions.md#super "super") (unset means zero-argument).

在 3.12 版本加入.

COMPARE_OP( _opname_ )¶
~~~
    

~~~
Performs a Boolean operation. The operation name can be found in `cmp_op[opname >> 5]`. If the fifth-lowest bit of `opname` is set (`opname & 16`), the result should be coerced to `bool`.

在 3.13 版本发生变更: The fifth-lowest bit of the oparg now indicates a forced conversion to [`bool`](functions.md#bool "bool").

IS_OP( _invert_ )¶
~~~
    

~~~
执行 `is` 比较，或者如果 `invert` 为 1 则执行 `is not`。

在 3.9 版本加入.

CONTAINS_OP( _invert_ )¶
~~~
    

~~~
执行 `in` 比较，或者如果 `invert` 为 1 则执行 `not in`。

在 3.9 版本加入.

IMPORT_NAME( _namei_ )¶
~~~
    

~~~
Imports the module `co_names[namei]`. `STACK[-1]` and `STACK[-2]` are popped and provide the _fromlist_ and _level_ arguments of [`__import__()`](functions.md#import__ "__import__"). The module object is pushed onto the stack. The current namespace is not affected: for a proper import statement, a subsequent `STORE_FAST` instruction modifies the namespace.

IMPORT_FROM( _namei_ )¶
~~~
    

~~~
Loads the attribute `co_names[namei]` from the module found in `STACK[-1]`. The resulting object is pushed onto the stack, to be subsequently stored by a `STORE_FAST` instruction.

JUMP_FORWARD( _delta_ )¶
~~~
    

~~~
将字节码计数器的值增加 _delta_ 。

JUMP_BACKWARD( _delta_ )¶
~~~
    

~~~
将字节码计数器减少 _delta_ 。 检查中断。

在 3.11 版本加入.

JUMP_BACKWARD_NO_INTERRUPT( _delta_ )¶
~~~
    

~~~
将字节码计数器减少 _delta_ 。 不检查中断。

在 3.11 版本加入.

POP_JUMP_IF_TRUE( _delta_ )¶
~~~
    

~~~
If `STACK[-1]` is true, increments the bytecode counter by _delta_. `STACK[-1]` is popped.

在 3.11 版本发生变更: The oparg is now a relative delta rather than an absolute target. This opcode is a pseudo-instruction, replaced in final bytecode by the directed versions (forward/backward).

在 3.12 版本发生变更: This is no longer a pseudo-instruction.

在 3.13 版本发生变更: This instruction now requires an exact [`bool`](functions.md#bool "bool") operand.

POP_JUMP_IF_FALSE( _delta_ )¶
~~~
    

~~~
If `STACK[-1]` is false, increments the bytecode counter by _delta_. `STACK[-1]` is popped.

在 3.11 版本发生变更: The oparg is now a relative delta rather than an absolute target. This opcode is a pseudo-instruction, replaced in final bytecode by the directed versions (forward/backward).

在 3.12 版本发生变更: This is no longer a pseudo-instruction.

在 3.13 版本发生变更: This instruction now requires an exact [`bool`](functions.md#bool "bool") operand.

POP_JUMP_IF_NOT_NONE( _delta_ )¶
~~~
    

~~~
If `STACK[-1]` is not `None`, increments the bytecode counter by _delta_. `STACK[-1]` is popped.

This opcode is a pseudo-instruction, replaced in final bytecode by the directed versions (forward/backward).

在 3.11 版本加入.

在 3.12 版本发生变更: This is no longer a pseudo-instruction.

POP_JUMP_IF_NONE( _delta_ )¶
~~~
    

~~~
If `STACK[-1]` is `None`, increments the bytecode counter by _delta_. `STACK[-1]` is popped.

This opcode is a pseudo-instruction, replaced in final bytecode by the directed versions (forward/backward).

在 3.11 版本加入.

在 3.12 版本发生变更: This is no longer a pseudo-instruction.

FOR_ITER( _delta_ )¶
~~~
    

~~~
`STACK[-1]` is an [iterator](../glossary.md#term-iterator). Call its [`__next__()`](stdtypes.md#iterator.__next__ "iterator.__next__") method. If this yields a new value, push it on the stack (leaving the iterator below it). If the iterator indicates it is exhausted then the byte code counter is incremented by _delta_.

在 3.12 版本发生变更: Up until 3.11 the iterator was popped when it was exhausted.

LOAD_GLOBAL( _namei_ )¶
~~~
    

~~~
将名为 `co_names[namei>>1]` 的全局对象加载到栈顶。

在 3.11 版本发生变更: 如果设置了 `namei` 的低比特位，则会在全局变量前将一个 `NULL` 推入栈。

LOAD_FAST( _var_num_ )¶
~~~
    

~~~
将指向局部对象 `co_varnames[var_num]` 的引用推入栈顶。

在 3.12 版本发生变更: This opcode is now only used in situations where the local variable is guaranteed to be initialized. It cannot raise [`UnboundLocalError`](exceptions.md#UnboundLocalError "UnboundLocalError").

LOAD_FAST_CHECK( _var_num_ )¶
~~~
    

~~~
Pushes a reference to the local `co_varnames[var_num]` onto the stack, raising an [`UnboundLocalError`](exceptions.md#UnboundLocalError "UnboundLocalError") if the local variable has not been initialized.

在 3.12 版本加入.

LOAD_FAST_AND_CLEAR( _var_num_ )¶
~~~
    

~~~
Pushes a reference to the local `co_varnames[var_num]` onto the stack (or pushes `NULL` onto the stack if the local variable has not been initialized) and sets `co_varnames[var_num]` to `NULL`.

在 3.12 版本加入.

STORE_FAST( _var_num_ )¶
~~~
    

~~~
Stores `STACK.pop()` into the local `co_varnames[var_num]`.

DELETE_FAST( _var_num_ )¶
~~~
    

~~~
移除局部对象 `co_varnames[var_num]`。

MAKE_CELL( _i_ )¶
~~~
    

~~~
在槽位 `i` 中创建一个新单元。 如果该槽位为非空则该值将存储到新单元中。

在 3.11 版本加入.

LOAD_DEREF( _i_ )¶
~~~
    

~~~
加载包含在 "fast locals" 存储的 `i` 号槽位中的单元。 将一个指向该单元所包含对象的引用推入栈。

在 3.11 版本发生变更: `i` 不再是长度为 `co_varnames` 的偏移量。

LOAD_FROM_DICT_OR_DEREF( _i_ )¶
~~~
    

~~~
Pops a mapping off the stack and looks up the name associated with slot `i` of the "fast locals" storage in this mapping. If the name is not found there, loads it from the cell contained in slot `i`, similar to `LOAD_DEREF`. This is used for loading free variables in class bodies (which previously used `LOAD_CLASSDEREF`) and in [annotation scopes](../reference/executionmodel.md#annotation-scopes) within class bodies.

在 3.12 版本加入.

STORE_DEREF( _i_ )¶
~~~
    

~~~
Stores `STACK.pop()` into the cell contained in slot `i` of the "fast locals" storage.

在 3.11 版本发生变更: `i` 不再是长度为 `co_varnames` 的偏移量。

DELETE_DEREF( _i_ )¶
~~~
    

~~~
清空 "fast locals" 存储中包含在 `i` 号槽位的单元。 被用于 [`del`](../reference/simple_stmts.md#del) 语句。

在 3.2 版本加入.

在 3.11 版本发生变更: `i` 不再是长度为 `co_varnames` 的偏移量。

COPY_FREE_VARS( _n_ )¶
~~~
    

~~~
将 `n` 个自由变量从闭包拷贝到帧中。 当调用闭包时不再需要调用方添加特殊的代码。

在 3.11 版本加入.

RAISE_VARARGS( _argc_ )¶
~~~
    

~~~
使用 `raise` 语句的 3 种形式之一引发异常，具体形式取决于 _argc_ 的值：

  * 0: `raise` (重新引发之前的异常)

  * 1: `raise STACK[-1]` (raise exception instance or type at `STACK[-1]`)

  * 2: `raise STACK[-2] from STACK[-1]` (raise exception instance or type at `STACK[-2]` with `__cause__` set to `STACK[-1]`)

CALL( _argc_ )¶
~~~
    

~~~
Calls a callable object with the number of arguments specified by `argc`. On the stack are (in ascending order):

  * 可调用对象

  * `self` or `NULL`

  * 其余的位置参数

`argc` is the total of the positional arguments, excluding `self`.

`CALL` 将把所有参数和可调用对象弹出栈，附带这些参数调用该可调用对象，并将该可调用对象的返回值推入栈。

在 3.11 版本加入.

在 3.13 版本发生变更: The callable now always appears at the same position on the stack.

在 3.13 版本发生变更: Calls with keyword arguments are now handled by `CALL_KW`.

CALL_KW( _argc_ )¶
~~~
    

~~~
Calls a callable object with the number of arguments specified by `argc`, including one or more named arguments. On the stack are (in ascending order):

  * 可调用对象

  * `self` or `NULL`

  * 其余的位置参数

  * 关键字参数

  * A [`tuple`](stdtypes.md#tuple "tuple") of keyword argument names

`argc` is the total of the positional and named arguments, excluding `self`. The length of the tuple of keyword argument names is the number of named arguments.

`CALL_KW` pops all arguments, the keyword names, and the callable object off the stack, calls the callable object with those arguments, and pushes the return value returned by the callable object.

在 3.13 版本加入.

CALL_FUNCTION_EX( _flags_ )¶
~~~
    

~~~
调用一个可调用对象并附带位置参数和关键字参数变量集合。 如果设置了 _flags_ 的最低位，则栈顶包含一个由额外关键字参数组成的映射对象。 在调用该可调用对象之前，映射对象和可迭代对象会被分别“解包”并将它们的内容分别作为关键字参数和位置参数传入。 `CALL_FUNCTION_EX` 会中栈中弹出所有参数及可调用对象，附带这些参数调用该可调用对象，并将可调用对象所返回的返回值推入栈顶。

在 3.6 版本加入.

PUSH_NULL¶
~~~
    

~~~
将一个 `NULL` 推入栈。 在调用序列中用来匹配 `LOAD_METHOD` 针对非方法调用推入栈的 `NULL`。

在 3.11 版本加入.

MAKE_FUNCTION¶
~~~
    

~~~
Pushes a new function object on the stack built from the code object at `STACK[1]`.

在 3.10 版本发生变更: 旗标值 `0x04` 是一个字符串元组而非字典。

在 3.11 版本发生变更: Qualified name at `STACK[-1]` was removed.

在 3.13 版本发生变更: Extra function attributes on the stack, signaled by oparg flags, were removed. They now use `SET_FUNCTION_ATTRIBUTE`.

SET_FUNCTION_ATTRIBUTE( _flag_ )¶
~~~
    

~~~
Sets an attribute on a function object. Expects the function at `STACK[-1]` and the attribute value to set at `STACK[-2]`; consumes both and leaves the function at `STACK[-1]`. The flag determines which attribute to set:

  * `0x01` 一个默认值的元组，用于按位置排序的仅限位置形参以及位置或关键字形参

  * `0x02` 一个仅限关键字形参的默认值的字典

  * `0x04` 一个包含形参标注的字符串元组。

  * `0x08` 一个包含用于自由变量的单元的元组，生成一个闭包

在 3.13 版本加入.

BUILD_SLICE( _argc_ )¶
~~~
    

~~~
Pushes a slice object on the stack. _argc_ must be 2 or 3. If it is 2, implements:
~~~
    
    
~~~
end = STACK.pop()
start = STACK.pop()
STACK.append(slice(start, stop))
~~~

if it is 3, implements:

    
    
~~~
step = STACK.pop()
end = STACK.pop()
start = STACK.pop()
STACK.append(slice(start, end, step))
~~~

See the [`slice()`](functions.md#slice "slice") built-in function for more information.

EXTENDED_ARG( _ext_ )¶

    

~~~
为任意带有大到无法放入默认的单字节的参数的操作码添加前缀。 _ext_ 存放一个附加字节作为参数中的高比特位。 对于每个操作码，最多允许三个 `EXTENDED_ARG` 前缀，构成两字节到三字节的参数。

CONVERT_VALUE( _oparg_ )¶
~~~
    

~~~
Convert value to a string, depending on `oparg`:
~~~
    
    
~~~
value = STACK.pop()
result = func(value)
STACK.push(result)
~~~

  * `oparg == 1`: call [`str()`](stdtypes.md#str "str") on _value_

  * `oparg == 2`: call [`repr()`](functions.md#repr "repr") on _value_

  * `oparg == 3`: call [`ascii()`](functions.md#ascii "ascii") on _value_

Used for implementing formatted literal strings (f-strings).

在 3.13 版本加入.

FORMAT_SIMPLE¶

    

~~~
Formats the value on top of stack:
~~~
    
    
~~~
value = STACK.pop()
result = value.__format__("")
STACK.push(result)
~~~

Used for implementing formatted literal strings (f-strings).

在 3.13 版本加入.

FORMAT_SPEC¶

    

~~~
Formats the given value with the given format spec:
~~~
    
    
~~~
spec = STACK.pop()
value = STACK.pop()
result = value.__format__(spec)
STACK.push(result)
~~~

Used for implementing formatted literal strings (f-strings).

在 3.13 版本加入.

MATCH_CLASS( _count_ )¶

    

~~~
`STACK[-1]` is a tuple of keyword attribute names, `STACK[-2]` is the class being matched against, and `STACK[-3]` is the match subject. _count_ is the number of positional sub-patterns.

Pop `STACK[-1]`, `STACK[-2]`, and `STACK[-3]`. If `STACK[-3]` is an instance of `STACK[-2]` and has the positional and keyword attributes required by _count_ and `STACK[-1]`, push a tuple of extracted attributes. Otherwise, push `None`.

在 3.10 版本加入.

在 3.11 版本发生变更: 在之前的版本中，该指令还会推入一个表示成功 (`True`) 或失败 (`False`) 的布尔值。

RESUME( _where_ )¶
~~~
    

~~~
空操作。 执行内部追踪、调试和优化检查。

`where` 操作数标记 `RESUME` 在哪里发生:

  * `0` The start of a function, which is neither a generator, coroutine nor an async generator

  * `1` 在 `yield` 表达式之后

  * `2` 在 `yield from` 表达式之后

  * `3` 在 `await` 表达式之后

在 3.11 版本加入.

RETURN_GENERATOR¶
~~~
    

~~~
Create a generator, coroutine, or async generator from the current frame. Used as first opcode of in code object for the above mentioned callables. Clear the current frame and return the newly created generator.

在 3.11 版本加入.

SEND( _delta_ )¶
~~~
    

~~~
Equivalent to `STACK[-1] = STACK[-2].send(STACK[-1])`. Used in `yield from` and `await` statements.

If the call raises [`StopIteration`](exceptions.md#StopIteration "StopIteration"), pop both items, push the exception's `value` attribute, and increment the bytecode counter by _delta_.

在 3.11 版本加入.

HAVE_ARGUMENT¶
~~~
    

~~~
This is not really an opcode. It identifies the dividing line between opcodes in the range [0,255] which don't use their argument and those that do (`< HAVE_ARGUMENT` and `>= HAVE_ARGUMENT`, respectively).

If your application uses pseudo instructions or specialized instructions, use the `hasarg` collection instead.

在 3.6 版本发生变更: 现在每条指令都带有参数，但操作码 `< HAVE_ARGUMENT` 会忽略它。 之前仅限操作码 `>= HAVE_ARGUMENT` 带有参数。

在 3.12 版本发生变更: Pseudo instructions were added to the `dis` module, and for them it is not true that comparison with `HAVE_ARGUMENT` indicates whether they use their arg.

自 3.13 版本弃用: Use `hasarg` instead.

CALL_INTRINSIC_1¶
~~~
    

~~~
Calls an intrinsic function with one argument. Passes `STACK[-1]` as the argument and sets `STACK[-1]` to the result. Used to implement functionality that is necessary but not performance critical.

The operand determines which intrinsic function is called:

Operand

|

描述  
  
---|---  
  
`INTRINSIC_1_INVALID`

|

Not valid  
  
`INTRINSIC_PRINT`

|

Prints the argument to standard out. Used in the REPL.  
  
`INTRINSIC_IMPORT_STAR`

|

Performs `import *` for the named module.  
  
`INTRINSIC_STOPITERATION_ERROR`

|

Extracts the return value from a `StopIteration` exception.  
  
`INTRINSIC_ASYNC_GEN_WRAP`

|

Wraps an aync generator value  
  
`INTRINSIC_UNARY_POSITIVE`

|

Performs the unary `+` operation  
  
`INTRINSIC_LIST_TO_TUPLE`

|

Converts a list to a tuple  
  
`INTRINSIC_TYPEVAR`

|

Creates a [`typing.TypeVar`](typing.md#typing.TypeVar "typing.TypeVar")  
  
`INTRINSIC_PARAMSPEC`

|

Creates a [`typing.ParamSpec`](typing.md#typing.ParamSpec "typing.ParamSpec")  
  
`INTRINSIC_TYPEVARTUPLE`

|

Creates a [`typing.TypeVarTuple`](typing.md#typing.TypeVarTuple "typing.TypeVarTuple")  
  
`INTRINSIC_SUBSCRIPT_GENERIC`

|

Returns [`typing.Generic`](typing.md#typing.Generic "typing.Generic") subscripted with the argument  
  
`INTRINSIC_TYPEALIAS`

|

Creates a [`typing.TypeAliasType`](typing.md#typing.TypeAliasType "typing.TypeAliasType"); used in the [`type`](../reference/simple_stmts.md#type) statement. The argument is a tuple of the type alias's name, type parameters, and value.  
  
在 3.12 版本加入.

CALL_INTRINSIC_2¶
~~~
    

~~~
Calls an intrinsic function with two arguments. Passes `STACK[-2]`, `STACK[-1]` as the arguments and sets `STACK[-1]` to the result. Used to implement functionality that is necessary but not performance critical.

The operand determines which intrinsic function is called:

Operand

|

描述  
  
---|---  
  
`INTRINSIC_2_INVALID`

|

Not valid  
  
`INTRINSIC_PREP_RERAISE_STAR`

|

Calculates the [`ExceptionGroup`](exceptions.md#ExceptionGroup "ExceptionGroup") to raise from a `try-except*`.  
  
`INTRINSIC_TYPEVAR_WITH_BOUND`

|

Creates a [`typing.TypeVar`](typing.md#typing.TypeVar "typing.TypeVar") with a bound.  
  
`INTRINSIC_TYPEVAR_WITH_CONSTRAINTS`

|

Creates a [`typing.TypeVar`](typing.md#typing.TypeVar "typing.TypeVar") with constraints.  
  
`INTRINSIC_SET_FUNCTION_TYPE_PARAMS`

|

Sets the `__type_params__` attribute of a function.  
  
在 3.12 版本加入.

**Pseudo-instructions**

These opcodes do not appear in Python bytecode. They are used by the compiler but are replaced by real opcodes or removed before bytecode is generated.

SETUP_FINALLY( _target_ )¶
~~~
    

~~~
Set up an exception handler for the following code block. If an exception occurs, the value stack level is restored to its current state and control is transferred to the exception handler at `target`.

SETUP_CLEANUP( _target_ )¶
~~~
    

~~~
Like `SETUP_FINALLY`, but in case of an exception also pushes the last instruction (`lasti`) to the stack so that `RERAISE` can restore it. If an exception occurs, the value stack level and the last instruction on the frame are restored to their current state, and control is transferred to the exception handler at `target`.

SETUP_WITH( _target_ )¶
~~~
    

~~~
Like `SETUP_CLEANUP`, but in case of an exception one more item is popped from the stack before control is transferred to the exception handler at `target`.

This variant is used in [`with`](../reference/compound_stmts.md#with) and [`async with`](../reference/compound_stmts.md#async-with) constructs, which push the return value of the context manager's [`__enter__()`](../reference/datamodel.md#object.__enter__ "object.__enter__") or [`__aenter__()`](../reference/datamodel.md#object.__aenter__ "object.__aenter__") to the stack.

POP_BLOCK¶
~~~
    

~~~
Marks the end of the code block associated with the last `SETUP_FINALLY`, `SETUP_CLEANUP` or `SETUP_WITH`.

JUMP¶
~~~
    

~~~
JUMP_NO_INTERRUPT¶
~~~
    

~~~
Undirected relative jump instructions which are replaced by their directed (forward/backward) counterparts by the assembler.

LOAD_CLOSURE( _i_ )¶
~~~
    

~~~
Pushes a reference to the cell contained in slot `i` of the "fast locals" storage.

Note that `LOAD_CLOSURE` is replaced with `LOAD_FAST` in the assembler.

在 3.13 版本发生变更: This opcode is now a pseudo-instruction.

LOAD_METHOD¶
~~~
    

~~~
Optimized unbound method lookup. Emitted as a `LOAD_ATTR` opcode with a flag set in the arg.

## 操作码集合¶

提供这些集合用于字节码指令的自动内省：

在 3.12 版本发生变更: The collections now contain pseudo instructions as well. These are opcodes with values `>= MIN_PSEUDO_OPCODE`.

dis.opname¶
~~~
    

~~~
操作名称的序列，可使用字节码来索引。

dis.opmap¶
~~~
    

~~~
映射操作名称到字节码的字典

dis.cmp_op¶
~~~
    

~~~
所有比较操作名称的序列。

dis.hasarg¶
~~~
    

~~~
Sequence of bytecodes that use their argument.

在 3.12 版本加入.

dis.hasconst¶
~~~
    

~~~
访问常量的字节码序列。

dis.hasfree¶
~~~
    

~~~
Sequence of bytecodes that access a free variable. 'free' in this context refers to names in the current scope that are referenced by inner scopes or names in outer scopes that are referenced from this scope. It does _not_ include references to global or builtin scopes.

dis.hasname¶
~~~
    

~~~
按名称访问属性的字节码序列。

dis.hasjump¶
~~~
    

~~~
Sequence of bytecodes that have a jump target. All jumps are relative.

在 3.13 版本加入.

dis.haslocal¶
~~~
    

~~~
访问局部变量的字节码序列。

dis.hascompare¶
~~~
    

~~~
布尔运算的字节码序列。

dis.hasexc¶
~~~
    

~~~
Sequence of bytecodes that set an exception handler.

在 3.12 版本加入.

dis.hasjrel¶
~~~
    

~~~
具有相对跳转目标的字节码序列。

自 3.13 版本弃用: All jumps are now relative. Use `hasjump`.

dis.hasjabs¶
~~~
    

~~~
