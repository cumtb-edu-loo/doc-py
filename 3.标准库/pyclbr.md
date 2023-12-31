# `pyclbr` \--- Python 模块浏览器支持¶

**源代码:** [Lib/pyclbr.py](https://github.com/python/cpython/tree/3.12/Lib/pyclbr.py)

* * *

`pyclbr` 模块提供了对于以 Python 编写的模块中定义的函数、类和方法的受限信息。 这种信息足够用来实现一个模块浏览器。 这种信息是从 Python 源代码中直接提取而非通过导入模块，因此该模块可以安全地用于不受信任的代码。 此限制使得非 Python 实现的模块无法使用此模块，包括所有标准和可选的扩展模块。

pyclbr.readmodule( _module_ , _path =None_)¶

    

~~~
返回一个将模块层级的类名映射到类描述器的字典。 如果可能，将会包括已导入基类的描述器。 形参 _module_ 为要读取模块名称的字符串；它可能是某个包内部的模块名称。 _path_ 如果给出则为添加到 `sys.path` 开头的目录路径序列，它会被用于定位模块的源代码。

此函数为原始接口，仅保留用于向下兼容。 它会返回以下内容的过滤版本。

pyclbr.readmodule_ex( _module_ , _path =None_)¶
~~~
    

~~~
返回一个基于字典的树，其中包含与模块中每个用 `def` 或 `class` 语句定义的函数和类相对应的函数和类描述器。 被返回的字典会将模块层级的函数和类名映射到它们的描述器。 嵌套的对象会被输入到它们的上级子目录中。 与 readmodule 一样， _module_ 指明要读取的模块而 _path_ 会被添加到 sys.path。 如果被读取的模块是一个包，则返回的字典将具有 `'__path__'` 键，其值是一个包含包搜索路径的列表。

在 3.7 版本加入: 嵌套定义的描述器。 它们通过新的子属性来访问。 每个定义都会有一个新的上级属性。

这些函数所返回的描述器是 Function 和 Class 类的实例。 用户不应自行创建这些类的实例。

## Function 对象¶

`Function` 类的实例描述了由 def 语句所定义的函数。 它们具有下列属性:

Function.file¶
~~~
    

~~~
函数定义所在的文件名称。

Function.module¶
~~~
    

~~~
定义了所描述函数的模块名称。

Function.name¶
~~~
    

~~~
函数名称。

Function.lineno¶
~~~
    

~~~
定义在文件中起始位置的行号。

Function.parent¶
~~~
    

~~~
对于最高层级函数为 None。 对于嵌套函数则为上级函数。

在 3.7 版本加入.

Function.children¶
~~~
    

~~~
将名称映射到嵌套函数和类描述器的字典。

在 3.7 版本加入.

Function.is_async¶
~~~
    

~~~
`True` 针对使用 `async` 前缀定义的函数，其他情况下为 `False`。

在 3.10 版本加入.

## Class 对象¶

`Class` 类的实例描述了由 class 语句所定义的类。 它们具有与 Function 对象相同的属性以及两个额外属性。

Class.file¶
~~~
    

~~~
类定义所在的文件名称。

Class.module¶
~~~
    

~~~
定义了所描述类的模块名称。

Class.name¶
~~~
    

~~~
类名称。

Class.lineno¶
~~~
    

~~~
定义在文件中起始位置的行号。

Class.parent¶
~~~
    

~~~
对于最高层级类为 None。 对于嵌套类则为上级类。

在 3.7 版本加入.

Class.children¶
~~~
    

~~~
将名称映射到嵌套函数和类描述器的字典。

在 3.7 版本加入.

Class.super¶
~~~
    

~~~
一个 `Class` 对象的列表，它们描述了所描述类的直接基类。 被命名为超类但无法被 `readmodule_ex()` 发现的类会作为类名字符串而非 `Class` 对象列出。

Class.methods¶
~~~
    

~~~
