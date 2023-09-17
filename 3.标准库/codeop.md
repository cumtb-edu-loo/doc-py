# `codeop` \--- 编译Python代码¶

**源代码：** [Lib/codeop.py](https://github.com/python/cpython/tree/3.12/Lib/codeop.py)

* * *

`codeop` 模块提供了可以模拟Python读取-执行-打印循环的实用程序，就像在 [`code`](3.标准库/code.md#module-code "code: Facilities to implement read-eval-print loops.") 模块中一样。因此，您可能不希望直接使用该模块；如果你想在程序中包含这样一个循环，你可能需要使用 [`code`](3.标准库/code.md#module-code "code: Facilities to implement read-eval-print loops.") 模块。

这个任务有两个部分:

  1. 能够判断一行输入是否完成了一条 Python 语句：简而言之，就是告诉我们接下来是要打印 '`>>>`' 还是 '`...`'。

  2. 记住用户已输入了哪些 future 语句，这样后续的输入可以在这些语句生效的状态下被编译。

`codeop` 模块提供了分别以及同时执行这两个部分的方式。

只执行前一部分：

codeop.compile_command( _source_ , _filename ='<input>'_, _symbol ='single'_)¶

    

~~~
尝试编译 _source_ ，这应当是一个 Python 代码字符串并且在 _source_ 是有效的 Python 代码时返回一个对象对象。 在此情况下，代码对象的 filename 属性将为 _filename_ ，其默认值为 `'<input>'`。 如果 _source_ 不是 _not_ 有效的 Python 代码，而是有效的 Python 代码的一个前缀时将返回 `None`。

如果 _source_ 存在问题，将引发异常。 如果存在无效的 Python 语法将引发 [`SyntaxError`](exceptions.md#SyntaxError "SyntaxError")，而如果存在无效的字面值则将引发 [`OverflowError`](exceptions.md#OverflowError "OverflowError") 或 [`ValueError`](exceptions.md#ValueError "ValueError")。

_symbol_ 参数确定 _source_ 是作为一条语句 (`'single'`，为默认值)，作为一个 [statement](../glossary.md#term-statement) 的序列 (`'exec'`) 还是作为一个 [expression](../glossary.md#term-expression) (`'eval'`) 来进行编译。 任何其他值都将导致引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

备注

解析器有可能（但很不常见）会在到达源码结尾之前停止解析并成功输出结果；在这种情况下，末尾的符号可能会被忽略而不是引发错误。 例如，一个反斜杠加两个换行符之后可以跟随任何无意义的符号。 一旦解析器 API 得到改进将修正这个问题。

_class _codeop.Compile¶
~~~
    

~~~
该类的实例具有 [`__call__()`](../reference/datamodel.md#object.__call__ "object.__call__") 方法，其签名与内置函数 [`compile()`](functions.md#compile "compile") 相似，区别在于如果该实例编译了包含 [`__future__`](__future__.md#module-__future__ "__future__: Future statement definitions") 语句的程序文本，则该实例会‘记住’并编译后续所有的所有包含该语句的程序文本。

_class _codeop.CommandCompiler¶
~~~
    

~~~
