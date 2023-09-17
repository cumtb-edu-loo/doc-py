# `marshal` \--- 内部 Python 对象序列化¶

* * *

此模块包含一此能以二进制格式来读写 Python 值的函数。 这种格式是 Python 专属的，但是独立于特定的机器架构（即你可以在一台 PC 上写入某个 Python 值，将文件传到一台 Sun 上并在那里读取它）。 这种格式的细节有意不带文档说明；它可能在不同 Python 版本中发生改变（但这种情况极少发生）。 [1]

这不是一个通用的“持久化”模块。 对于通用的持久化以及通过 RPC 调用传递 Python 对象，请参阅 [`pickle`](pickle.md#module-pickle "pickle: Convert Python objects to streams of bytes and back.") 和 [`shelve`](shelve.md#module-shelve "shelve: Python object persistence.") 等模块。 `marshal` 模块主要是为了支持读写 `.pyc` 文件形式“伪编译”代码的 Python 模块。 因此，Python 维护者保留在必要时以不向下兼容的方式修改 marshal 格式的权利。 如果你要序列化和反序列化 Python 对象，请改用 [`pickle`](pickle.md#module-pickle "pickle: Convert Python objects to streams of bytes and back.") 模块 -- 其执行效率相当，版本独立性有保证，并且 pickle 还支持比 marshal 更多样的对象类型。

警告

`marshal` 模块对于错误或恶意构建的数据来说是不安全的。 永远不要 unmarshal 来自不受信任的或未经验证的来源的数据。

不是所有 Python 对象类型都受支持；一般来说，此模块只能写入和读取不依赖于特定 Python 调用的对象。 下列类型是受支持的：布尔值、整数、浮点数、复数、字符串、字节串、字节数组、元组、列表、集合、冻结集合、字典和代码对象，需要了解的一点是元组、列表、集合、冻结集合和字典只在其所包含的值也是这些值时才受支持。 单例对象 [`None`](constants.md#None "None"), [`Ellipsis`](constants.md#Ellipsis "Ellipsis") and [`StopIteration`](3.标准库/exceptions.md#StopIteration "StopIteration") 也可以被 marshal 和 unmarshal。 对于 _version_ 低于 3 的格式，递归列表、集合和字典无法被写入（见下文）。

有些函数可以读/写文件，还有些函数可以操作字节类对象。

这个模块定义了以下函数：

marshal.dump( _value_ , _file_ [, _version_ ])¶

    

~~~
向打开的文件写入值。 值必须为受支持的类型。 文件必须为可写的 [binary file](../glossary.md#term-binary-file)。

如果值具有（或所包含的对象具有）不受支持的类型，则会引发 [`ValueError`](exceptions.md#ValueError "ValueError") \--- 但是将向文件写入垃圾数据。 对象也将不能正确地通过 `load()` 重新读取。

_version_ 参数指明 `dump` 应当使用的数据格式（见下文）。

引发一个 [审计事件](sys.md#auditing) `marshal.dumps`，附带参数 `value`, `version`。

marshal.load( _file_ )¶
~~~
    

~~~
从打开的文件读取一个值并返回。 如果读不到有效的值（例如由于数据为不同 Python 版本的不兼容 marshal 格式），则会引发 [`EOFError`](exceptions.md#EOFError "EOFError"), [`ValueError`](exceptions.md#ValueError "ValueError") 或 [`TypeError`](exceptions.md#TypeError "TypeError")。 文件必须为可读的 [binary file](../glossary.md#term-binary-file)。

引发一个 [审计事件](sys.md#auditing) `marshal.load`，没有附带参数。

备注

如果通过 `dump()` marshal 了一个包含不受支持类型的对象，`load()` 将为不可 marshal 的类型替换 `None`。

在 3.10 版本发生变更: 使用此调用为每个代码对象引发一个 `code.__new__` 审计事件。 现在它会为整个载入操作引发单个 `marshal.load` 事件。

marshal.dumps( _value_ [, _version_ ])¶
~~~
    

~~~
返回将通过 `dump(value, file)` 被写入一个文件的字节串对象。 值必须属于受支持的类型。 如果值属于（或包含的对象属于）不受支持的类型则会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

_version_ 参数指明 `dumps` 应当使用的数据类型（见下文）。

引发一个 [审计事件](sys.md#auditing) `marshal.dumps`，附带参数 `value`, `version`。

marshal.loads( _bytes_ )¶
~~~
    

~~~
将 [bytes-like object](../glossary.md#term-bytes-like-object) 转换为一个值。 如果找不到有效的值，则会引发 [`EOFError`](exceptions.md#EOFError "EOFError"), [`ValueError`](exceptions.md#ValueError "ValueError") 或 [`TypeError`](exceptions.md#TypeError "TypeError")。 输入的额外字节串会被忽略。

引发一个 [审计事件](sys.md#auditing) `marshal.loads`，附带参数 `bytes`。

在 3.10 版本发生变更: 使用此调用为每个代码对象引发一个 `code.__new__` 审计事件。 现在它会为整个载入操作引发单个 `marshal.loads` 事件。

此外，还定义了以下常量：

marshal.version¶
~~~
    

~~~
