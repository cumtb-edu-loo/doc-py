# `inspect` \--- 检查对象¶

**源代码:** [Lib/inspect.py](https://github.com/python/cpython/tree/3.12/Lib/inspect.py)

* * *

`inspect` 模块提供了一些有用的函数帮助获取对象的信息，例如模块、类、方法、函数、回溯、帧对象以及代码对象。例如它可以帮助你检查类的内容，获取某个方法的源代码，取得并格式化某个函数的参数列表，或者获取你需要显示的回溯的详细信息。

该模块提供了4种主要的功能：类型检查、获取源代码、检查类与函数、检查解释器的调用堆栈。

## 类型和成员¶

`getmembers()` 函数获取对象如类或模块的成员。 名称以“is”打头的函数主要是作为传给 `getmembers()` 的第二个参数的便捷选项提供的。 它们还可帮助你确定你是否能找到下列特殊属性（请参阅 [导入相关的模块属性](4.语言参考/import.md#import-mod-attrs) 了解有关模块属性的详情）:

类型

|

属性

|

描述  
  
---|---|---  
  
class -- 类

|

__doc__

|

文档字符串  
  
|

__name__

|

类定义时所使用的名称  
  
|

__qualname__

|

qualified name -- 限定名称  
  
|

__module__

|

该类型被定义时所在的模块的名称  
  
method -- 方法

|

__doc__

|

文档字符串  
  
|

__name__

|

该方法定义时所使用的名称  
  
|

__qualname__

|

qualified name -- 限定名称  
  
|

__func__

|

实现该方法的函数对象  
  
|

__self__

|

该方法被绑定的实例，若没有绑定则为 `None`  
  
|

__module__

|

定义此方法的模块的名称  
  
function -- 函数

|

__doc__

|

文档字符串  
  
|

__name__

|

用于定义此函数的名称  
  
|

__qualname__

|

qualified name -- 限定名称  
  
|

__code__

|

包含已编译函数的代码对象 [bytecode](../glossary.md#term-bytecode)  
  
|

__defaults__

|

所有位置或关键字参数的默认值的元组  
  
|

__kwdefaults__

|

保存仅关键字参数的所有默认值的映射  
  
|

__globals__

|

此函数定义所在的全局命名空间  
  
|

__builtins__

|

builtins 命名空间  
  
|

__annotations__

|

参数名称到注解的映射；保留键 `"return"` 用于返回值注解。  
  
|

__module__

|

此函数定义所在的模块名称  
  
traceback -- 回溯

|

tb_frame

|

此层的帧对象  
  
|

tb_lasti

|

在字节码中最后尝试的指令的索引  
  
|

tb_lineno

|

当前行在 Python 源代码中的行号  
  
|

tb_next

|

下一个内部回溯对象（由本层调用）  
  
frame -- 帧

|

f_back

|

下一个外部帧对象（此帧的调用者）  
  
|

f_builtins

|

此帧执行时所在的 builtins 命名空间  
  
|

f_code

|

在此帧中执行的代码对象  
  
|

f_globals

|

此帧执行时所在的全局命名空间  
  
|

f_lasti

|

在字节码中最后尝试的指令的索引  
  
|

f_lineno

|

当前行在 Python 源代码中的行号  
  
|

f_locals

|

此帧所看到的局部命名空间  
  
|

f_trace

|

此帧的追踪函数，或 `None`  
  
code -- 代码

|

co_argcount

|

参数数量（不包括仅关键字参数、* 或 ** 参数）  
  
|

co_code

|

字符串形式的原始字节码  
  
|

co_cellvars

|

单元变量名称的元组(通过包含作用域引用)  
  
|

co_consts

|

字节码中使用的常量元组  
  
|

co_filename

|

创建此代码对象的文件的名称  
  
|

co_firstlineno

|

第一行在Python源代码中的行号  
  
|

co_flags

|

`CO_*` 标志的位图，详见 此处  
  
|

co_lnotab

|

编码的行号到字节码索引的映射  
  
|

co_freevars

|

自由变量的名字组成的元组（通过函数闭包引用）  
  
|

co_posonlyargcount

|

仅限位置参数的数量  
  
|

co_kwonlyargcount

|

仅限关键字参数的数量（不包括 ** 参数）  
  
|

co_name

|

定义此代码对象的名称  
  
|

co_qualname

|

定义此代码对象的完整限定名称  
  
|

co_names

|

除参数和函数局部变量之外的名称元组  
  
|

co_nlocals

|

局部变量的数量  
  
|

co_stacksize

|

需要虚拟机堆栈空间  
  
|

co_varnames

|

参数名和局部变量的元组  
  
generator -- 生成器

|

__name__

|

名称  
  
|

__qualname__

|

qualified name -- 限定名称  
  
|

gi_frame

|

frame -- 帧  
  
|

gi_running

|

生成器在运行吗？  
  
|

gi_code

|

code -- 代码  
  
|

gi_yieldfrom

|

通过 `yield from``迭代的对象，或``None`  
  
coroutine -- 协程

|

__name__

|

名称  
  
|

__qualname__

|

qualified name -- 限定名称  
  
|

cr_await

|

正在等待的对象，或 `None`  
  
|

cr_frame

|

frame -- 帧  
  
|

cr_running

|

这个协程正在运行吗？  
  
|

cr_code

|

code -- 代码  
  
|

cr_origin

|

协程被创建的位置，或 `None`。参见 [`sys.set_coroutine_origin_tracking_depth()`](3.标准库/sys.md#sys.set_coroutine_origin_tracking_depth "sys.set_coroutine_origin_tracking_depth")  
  
builtin

|

__doc__

|

文档字符串  
  
|

__name__

|

此函数或方法的原始名称  
  
|

__qualname__

|

qualified name -- 限定名称  
  
|

__self__

|

方法绑定到的实例，或 `None`  
  
在 3.5 版本发生变更: 为生成器添加 `__qualname__` 和 `gi_yieldfrom` 属性。

生成器的 `__name__` 属性现在由函数名称设置，而不是代码对象名称，并且现在可以被修改。

在 3.7 版本发生变更: 为协程添加 `cr_origin` 属性。

在 3.10 版本发生变更: 为函数添加 `__builtins__` 属性。

inspect.getmembers( _object_ [, _predicate_ ])¶

    

~~~
返回一个对象上的所有成员，组成以 `(名称, 值)` 对为元素的列表，按名称排序。如果提供了可选的 _predicate_ 参数（会对每个成员的 `值` 对象进行一次调用），则仅包含该断言为真的成员。

备注

如果要让 `getmembers()` 返回元类中定义的类属性，那么实参须为一个类且这些属性在元类的自定义方法 `__dir__()` 中列出。

inspect.getmembers_static( _object_ [, _predicate_ ])¶
~~~
    

~~~
将一个对象的所有成员作为由 `(name, value)` 对组成并按名称排序的列表返回而不触发通过描述器协议 __getattr__ 或 __getattribute__ 执行的动态查找。 或是作为可选项，只返回满足给定预期的成员。

备注

`getmembers_static()` 可能无法获得 getmembers 所能获取的所有成员（如动态创建的属性）并且可能会找到一些 getmembers 所不能找到的成员（如会引发 AttributeError 的描述器）。 在某些情况下它还能返回描述器对象而不是实例成员。

在 3.11 版本加入.

inspect.getmodulename( _path_ )¶
~~~
    

~~~
返回由文件名 _path_ 表示的模块名字，但不包括外层的包名。文件扩展名会检查是否在 [`importlib.machinery.all_suffixes()`](importlib.md#importlib.machinery.all_suffixes "importlib.machinery.all_suffixes") 列出的条目中。如果符合，则文件路径的最后一个组成部分会去掉后缀名后被返回；否则返回 `None`。

值得注意的是，这个函数 _仅_ 返回可以作为 Python 模块的名字，而有可能指向一个 Python 包的路径仍然会返回 `None`。

在 3.3 版本发生变更: 该函数直接基于 [`importlib`](importlib.md#module-importlib "importlib: The implementation of the import machinery.")。

inspect.ismodule( _object_ )¶
~~~
    

~~~
当该对象是一个模块时返回 `True`。

inspect.isclass( _object_ )¶
~~~
    

~~~
当该对象是一个类时返回 `True`，无论是内置类或者 Python 代码中定义的类。

inspect.ismethod( _object_ )¶
~~~
    

~~~
当该对象是一个 Python 写成的绑定方法时返回 `True`。

inspect.isfunction( _object_ )¶
~~~
    

~~~
当该对象是一个 Python 函数时（包括使用 [lambda](../glossary.md#term-lambda) 表达式创造的函数），返回 `True`。

inspect.isgeneratorfunction( _object_ )¶
~~~
    

~~~
当该对象是一个 Python 生成器函数时返回 `True`。

在 3.8 版本发生变更: 对于使用 [`functools.partial()`](functools.md#functools.partial "functools.partial") 封装的函数，如果被封装的函数是一个 Python 生成器函数，现在也会返回 `True`。

inspect.isgenerator( _object_ )¶
~~~
    

~~~
当该对象是一个生成器时返回 `True`。

inspect.iscoroutinefunction( _object_ )¶
~~~
    

~~~
如果该对象为 [coroutine function](../glossary.md#term-coroutine-function) (使用 [`async def`](../reference/compound_stmts.md#async-def) 语法定义的函数), 包装了 [coroutine function](../glossary.md#term-coroutine-function) 的 [`functools.partial()`](functools.md#functools.partial "functools.partial") 或使用 `markcoroutinefunction()` 标记的同步函数则返回 `True`。

在 3.5 版本加入.

在 3.8 版本发生变更: 对于使用 [`functools.partial()`](functools.md#functools.partial "functools.partial") 封装的函数，如果被封装的函数是一个 [协程函数](../glossary.md#term-coroutine-function) ，现在也会返回 `True`。

在 3.12 版本发生变更: 使用 `markcoroutinefunction()` 标记的同步函数现在将返回 `True`。

inspect.markcoroutinefunction( _func_ )¶
~~~
    

~~~
将一个可调用对象标记为 [coroutine function](../glossary.md#term-coroutine-function) 的装饰器，如果它不会被 `iscoroutinefunction()` 检测到的话。

这可被用于返回 [coroutine](../glossary.md#term-coroutine) 的同步函数，如果该函数被传给需要 `iscoroutinefunction()` 的 API 的话。

在可能的情况下，更推荐使用 [`async def`](../reference/compound_stmts.md#async-def) 函数。 调用该函数并使用 `iscoroutine()` 来检测其返回值也是可接受的。

在 3.12 版本加入.

inspect.iscoroutine( _object_ )¶
~~~
    

~~~
当该对象是一个由 [`async def`](../reference/compound_stmts.md#async-def) 函数创建的 [协程](../glossary.md#term-coroutine) 时返回 `True`。

在 3.5 版本加入.

inspect.isawaitable( _object_ )¶
~~~
    

~~~
如果该对象可以在 [`await`](../reference/expressions.md#await) 表达式中使用时返回 `True`。

也可以用于区分基于生成器的协程和常规的生成器：
~~~
    
    
~~~python
def gen():
    yield
@types.coroutine
def gen_coro():
    yield

assert not isawaitable(gen())
assert isawaitable(gen_coro())
~~~

在 3.5 版本加入.

inspect.isasyncgenfunction( _object_ )¶

    

~~~
如果该对象是一个 [异步生成器](../glossary.md#term-asynchronous-generator) 函数则返回 `True`，例如：
~~~
    
    
~~~shell
>>> async def agen():
...     yield 1
...
>>> inspect.isasyncgenfunction(agen)
True
~~~

在 3.6 版本加入.

在 3.8 版本发生变更: 对于使用 [`functools.partial()`](functools.md#functools.partial "functools.partial") 封装的函数，如果被封装的函数是一个 [异步生成器](../glossary.md#term-asynchronous-generator) ，现在也会返回 `True`。

inspect.isasyncgen( _object_ )¶

    

~~~
如果该对象是一个由 [异步生成器](../glossary.md#term-asynchronous-generator) 函数创建的 [异步生成器迭代器](../glossary.md#term-asynchronous-generator-iterator)，则返回 `True`。

在 3.6 版本加入.

inspect.istraceback( _object_ )¶
~~~
    

~~~
如果该对象是一个回溯则返回 `True`。

inspect.isframe( _object_ )¶
~~~
    

~~~
如果该对象是一个帧对象则返回 `True`。

inspect.iscode( _object_ )¶
~~~
    

~~~
如果该对象是一个代码对象则返回 `True`。

inspect.isbuiltin( _object_ )¶
~~~
    

~~~
如果该对象是一个内置函数或一个绑定的内置方法，则返回 `True`。

inspect.ismethodwrapper( _object_ )¶
~~~
    

~~~
如果对象类型为 [`MethodWrapperType`](types.md#types.MethodWrapperType "types.MethodWrapperType") 则返回 `True`。

这些是 [`MethodWrapperType`](types.md#types.MethodWrapperType "types.MethodWrapperType") 的实例，如 [`__str__()`](../reference/datamodel.md#object.__str__ "object.__str__"), [`__eq__()`](../reference/datamodel.md#object.__eq__ "object.__eq__") 和 [`__repr__()`](../reference/datamodel.md#object.__repr__ "object.__repr__")。

在 3.11 版本加入.

inspect.isroutine( _object_ )¶
~~~
    

~~~
如果该对象是一个用户定义的或内置的函数或者方法，则返回 `True`。

inspect.isabstract( _object_ )¶
~~~
    

~~~
如果该对象是一个抽象基类则返回 `True`。

inspect.ismethoddescriptor( _object_ )¶
~~~
    

~~~
如果该对象是一个方法描述器，但 `ismethod()` 、 `isclass()` 、 `isfunction()` 及 `isbuiltin()` 均不为真，则返回 `True`。

例如，该函数对于 `int.__add__` 为真。一个通过此测试的对象可以有 [`__get__()`](../reference/datamodel.md#object.__get__ "object.__get__") 方法，但不能有 [`__set__()`](../reference/datamodel.md#object.__set__ "object.__set__") 方法，除此以外的属性集合是可变的。一个 [`__name__`](stdtypes.md#definition.__name__ "definition.__name__") 属性通常是合理的，而 `__doc__` 属性常常也是。

即使是通过描述器实现的函数，如果通过其他某一个测试，则 `ismethoddescriptor()` 测试则会返回 `False`。这单纯是因为其他测试提供了更多保证。比如，当一个对象通过 `ismethod()` 测试时，你可以使用 `__func__` 等属性。

inspect.isdatadescriptor( _object_ )¶
~~~
    

~~~
如果该对象是一个数据描述器则返回 `True`。

数据描述器拥有 [`__set__`](../reference/datamodel.md#object.__set__ "object.__set__") 或 [`__delete__`](../reference/datamodel.md#object.__delete__ "object.__delete__") 方法。比如属性（通过 Python 定义）、getset 和成员。后二者是通过 C 定义的，并且也有对应的更具体的测试，并且在不同的 Python 实现中也是健壮的。典型地，数据描述器会拥有 [`__name__`](stdtypes.md#definition.__name__ "definition.__name__") 和 `__doc__` 属性（属性、getset 和成员都包含这两个属性），但这并无保证。

inspect.isgetsetdescriptor( _object_ )¶
~~~
    

~~~
如果该对象是一个 getset 描述器则返回 `True`。

**CPython 实现细节：** getset 是在扩展模块中通过 [`PyGetSetDef`](../c-api/structures.md#c.PyGetSetDef "PyGetSetDef") 结构体定义的属性。对于不包含这种类型的 Python 实现，这个方法将永远返回 `False`。

inspect.ismemberdescriptor( _object_ )¶
~~~
    

~~~
如果该对象是一个成员描述器则返回 `True`。

**CPython 实现细节：** 成员描述器是在扩展模块中通过 [`PyMemberDef`](../c-api/structures.md#c.PyMemberDef "PyMemberDef") 结构体定义的属性。对于不包含这种类型的 Python 实现，这个方法将永远返回 `False`。

## 获取源代码¶

inspect.getdoc( _object_ )¶
~~~
    

~~~
获取对象的文档字符串并通过 `cleandoc()` 进行清理。如果对象本身并未提供文档字符串并且这个对象是一个类、一个方法、一个特性或者一个描述器，将通过继承层次结构获取文档字符串。如果文档字符串无效或缺失，则返回 `None`。

在 3.5 版本发生变更: 文档字符串没有被重写的话现在会被继承。

inspect.getcomments( _object_ )¶
~~~
    

~~~
任意多行注释作为单一一个字符串返回。对于类、函数和方法，选取紧贴在该对象的源代码之前的注释；对于模块，选取 Python 源文件顶部的注释。如果对象的源代码不可获得，返回 `None`。这可能是因为对象是 C 语言中或者是在交互式命令行中定义的。

inspect.getfile( _object_ )¶
~~~
    

~~~
返回定义了这个对象的文件名（包括文本文件或二进制文件）。如果该对象是一个内置模块、类或函数则会失败并引发一个 [`TypeError`](exceptions.md#TypeError "TypeError")。

inspect.getmodule( _object_ )¶
~~~
    

~~~
尝试猜测一个对象是在哪个模块中定义的。 如果无法确定模块则返回 `None`。

inspect.getsourcefile( _object_ )¶
~~~
    

~~~
返回一个对象定义所在 Python 源文件的名称，如果无法获取源文件则返回 `None`。 如果对象是一个内置模块、类或函数则将失败并引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

inspect.getsourcelines( _object_ )¶
~~~
    

~~~
返回一个源代码行的列表和对象的起始行号。 参数可以是一个模块、类、方法、函数、回溯或者代码对象。 源代码将以与该对象所对应的行的列表的形式返回并且行号指明其中第一行代码出现在初始源文件的那个位置。 如果源代码无法被获取则会引发 [`OSError`](exceptions.md#OSError "OSError")。 如果对象是一个内置模块、类或函数则会引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

在 3.3 版本发生变更: 现在会引发 [`OSError`](exceptions.md#OSError "OSError") 而不是 [`IOError`](exceptions.md#IOError "IOError")，后者现在是前者的一个别名。

inspect.getsource( _object_ )¶
~~~
    

~~~
返回对象的源代码文本。 参数可以是一个模块、类、方法、函数、回溯帧或代码对象。 源代码将以单个字符串的形式被返回。 如果源代码无法被获取则会引发 [`OSError`](exceptions.md#OSError "OSError")。 如果对象是一个内置模块、类或函数则会引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

在 3.3 版本发生变更: 现在会引发 [`OSError`](exceptions.md#OSError "OSError") 而不是 [`IOError`](exceptions.md#IOError "IOError")，后者现在是前者的一个别名。

inspect.cleandoc( _doc_ )¶
~~~
    

~~~
清理文档字符串中为对齐当前代码块进行的缩进

第一行的所有前缀空白符会被移除。从第二行开始所有可以被统一去除的空白符也会被去除。之后，首尾的空白行也会被移除。同时，所有制表符会被展开到空格。

## 使用 Signature 对象对可调用对象进行内省¶

在 3.3 版本加入.

Signature 对象代表了一个可调用对象的调用签名和返回值标注。要获取一个 Signature 对象，使用 `signature()` 函数。

inspect.signature( _callable_ , _*_ , _follow_wrapped =True_, _globals =None_, _locals =None_, _eval_str =False_)¶
~~~
    

~~~
返回给定的 `callable` 的一个 `Signature` 对象：
~~~
    
    
~~~shell
>>> from inspect import signature
>>> def foo(a, *, b:int, **kwargs):
...     pass

>>> sig = signature(foo)

>>> str(sig)
'(a, *, b:int, **kwargs)'

>>> str(sig.parameters['b'])
'b:int'

>>> sig.parameters['b'].annotation
<class 'int'>
~~~

接受各类的 Python 可调用对象，包括单纯的函数、类，到 [`functools.partial()`](functools.md#functools.partial "functools.partial") 对象。

对于在字符串化标注模块（`from __future__ import annotations`）中定义的对象， `signature()` 会尝试使用 `inspect.get_annotations()` 自动地反字符串化这些标注。参数 `global` `locals` 和 `eval_str` 会在分析标注时被传入 `inspect.get_annotations()` 。如何运用这些参数请参见 `inspect.get_annotations()` 的文档。

如果没有签名可以提供，则引发 [`ValueError`](3.标准库/exceptions.md#ValueError "ValueError")；如果不支持该对象类型，则引发 [`TypeError`](3.标准库/exceptions.md#TypeError "TypeError")。同时，如果标注被反字符串化，并且 `eval_str` 不是假值，则 `eval()` 会被调用以反字符串化，此时有可能引发任何种类的异常。

函数签名中的斜杠（/）表示在它之前的参数是仅限位置的。详见 [编程常见问题中关于仅限位置参数的条目](programming.md#faq-positional-only-arguments)

在 3.5 版本加入: `follow_wrapped` 形参。传递 `False` 来获得特定关于 `callable` 的签名（`callable.__wrapped__` 将不会用来解包被修饰的可调用对象）。

在 3.10 版本加入: 添加参数 `globals` 、 `locals` 和 `eval_str`。

备注

一些可调用对象可能在特定 Python 实现中无法被内省。例如，在 CPython 中，部分通过 C 语言定义的内置函数不提供关于其参数的元数据。

_class _inspect.Signature( _parameters =None_, _*_ , _return_annotation =Signature.empty_)¶

    

~~~
一个 Signature 对象代表了一个函数的调用签名和返回值标注。对于函数接受的每个参数，它对应地在自身的 `parameters` 容器中存储一个 `Parameter` 对象。

可选参数 _parameters_ 是一个 `Parameter` 对象组成的序列，它会在之后被验证不存在名字重复的参数，并且参数处于正确的顺序，即仅限位置参数最前，之后紧接着可位置可关键字参数，并且有默认值参数在无默认值参数之前。

可选参数 _return_annotation_ 可以是任意 Python 对象，是该可调用对象中“返回值”的标注。

Signature objects are _immutable_. Use `Signature.replace()` or [`copy.replace()`](copy.md#copy.replace "copy.replace") to make a modified copy.

在 3.5 版本发生变更: Signature 对象是可封存的并且是 [hashable](../glossary.md#term-hashable)。

empty¶
~~~
    

~~~
该类的一个特殊标记来明确指出返回值标注缺失。

parameters¶
~~~
    

~~~
一个参数名字到对应 `Parameter` 对象的有序映射。参数以严格的定义顺序出现，包括仅关键字参数。

在 3.7 版本发生变更: Python 从 3.7 版起才显式地保证了它保持仅关键字参数的定义顺序，尽管实践上在 Python 3 中一直保持了这个顺序。

return_annotation¶
~~~
    

~~~
可调用对象的“返回值”标注。如果可调用对象没有“返回值”标注，这个属性会被设置为 `Signature.empty`。

bind( _* args_, _** kwargs_)¶
~~~
    

~~~
构造一个位置和关键字实参到形参的映射。如果 `*args` 和 `**kwargs` 符合签名，则返回一个 `BoundArguments`；否则引发一个 [`TypeError`](exceptions.md#TypeError "TypeError") 。

bind_partial( _* args_, _** kwargs_)¶
~~~
    

~~~
与 `Signature.bind()` 作用方式相同，但允许省略部分必要的参数（模仿 [`functools.partial()`](functools.md#functools.partial "functools.partial") 的行为）。返回 `BoundArguments`，或者在传入参数不符合签名的情况下，引发一个 [`TypeError`](exceptions.md#TypeError "TypeError")。

replace( _*[, parameters][, return_annotation]_)¶
~~~
    

~~~
根据发起调用 `replace()` 的实例创建一个新的 Signature 实例。 可以通过传入不同的 `parameters` 和/或 `return_annotation` 来覆盖基类签名的相应属性。 要从复制的 Signature 中移除 return_annotation，可以传入 `Signature.empty`。
~~~
    
    
~~~shell
>>> def test(a, b):
...     pass
...
>>> sig = signature(test)
>>> new_sig = sig.replace(return_annotation="new return anno")
>>> str(new_sig)
"(a, b) -> 'new return anno'"
~~~

Signature objects are also supported by generic function [`copy.replace()`](copy.md#copy.replace "copy.replace").

_classmethod _from_callable( _obj_ , _*_ , _follow_wrapped =True_, _globalns =None_, _localns =None_)¶

    

~~~
返回给定可调用对象 `obj` 的 `Signature` 对象（或其子类）。传入 `follow_wrapped=False` 来直接得到 `obj` 的签名，而不通过 `__wrapped__` 链来解析包装。在解析标注时会将 `globalns` 和 `localns` 用作命名空间。

该函数简化了创建 `Signature` 的子类的过程：
~~~
    
    
~~~
class MySignature(Signature):
    pass
sig = MySignature.from_callable(min)
assert isinstance(sig, MySignature)
~~~

在 3.5 版本加入.

在 3.10 版本加入: 添加了参数 `globalns` 和 `localns`。

_class _inspect.Parameter( _name_ , _kind_ , _*_ , _default =Parameter.empty_, _annotation =Parameter.empty_)¶

    

~~~
Parameter objects are _immutable_. Instead of modifying a Parameter object, you can use `Parameter.replace()` or [`copy.replace()`](copy.md#copy.replace "copy.replace") to create a modified copy.

在 3.5 版本发生变更: Parameter 对象是可封存的并且是 [hashable](../glossary.md#term-hashable)。

empty¶
~~~
    

~~~
该类的一个特殊标记来明确指出默认值和标注的缺失。

name¶
~~~
    

~~~
参数的名字字符串。这个名字必须是一个合法的 Python 标识符。

**CPython 实现细节：** CPython 会为用于实现推导式和生成器表达式的代码对象构造形如 `.0` 的隐式形参名。

在 3.6 版本发生变更: 这些形参名会被此模块暴露为形如 `implicit0` 一样的名字。

default¶
~~~
    

~~~
该参数的默认值。如果该参数没有默认值，这个属性会被设置为 `Parameter.empty` 。

annotation¶
~~~
    

~~~
该参数的标注。如果该参数没有标注，这个属性会被设置为 `Parameter.empty` 。

kind¶
~~~
    

~~~
描述参数值要如何绑定到形参。 可能的取值可通过 `Parameter` 获得 (如 `Parameter.KEYWORD_ONLY`)，并支持比较与排序，基于以下顺序:

名称

|

含意  
  
---|---  
  
_POSITIONAL_ONLY_

|

值必须以位置参数的方式提供。仅限位置参数是在函数定义中出现在 `/` 之前（如果有）的条目。  
  
_POSITIONAL_OR_KEYWORD_

|

值既可以以关键字参数的形式提供，也可以以位置参数的形式提供（这是 Python 写成的函数的标准绑定行为的）。  
  
_VAR_POSITIONAL_

|

没有绑定到其他形参的位置实参组成的元组。这对应于 Python 函数定义中的 `*args` 形参。  
  
_KEYWORD_ONLY_

|

值必须以关键字参数的形式提供。仅限关键字形参是在 Python 函数定义中出现在 `*` 或 `*args` 之后的条目。  
  
_VAR_KEYWORD_

|

一个未绑定到其他形参的关键字参数的字典。这对应于 Python 函数定义中的 `**kwargs` 形参。  
  
例如，打印所有没有默认值的仅关键字参数：
~~~
    
    
~~~shell
>>> def foo(a, b, *, c, d=10):
...     pass

>>> sig = signature(foo)
>>> for param in sig.parameters.values():
...     if (param.kind == param.KEYWORD_ONLY and
...                        param.default is param.empty):
...         print('Parameter:', param)
Parameter: c
~~~

kind.description¶

    

~~~
描述 Parameter.kind 的枚举值。

在 3.8 版本加入.

例子：打印所有参数的描述：
~~~
    
    
~~~shell
>>> def foo(a, b, *, c, d=10):
...     pass

>>> sig = signature(foo)
>>> for param in sig.parameters.values():
...     print(param.kind.description)
positional or keyword
positional or keyword
keyword-only
keyword-only
~~~

replace( _*[, name][, kind][, default][, annotation]_)¶

    

~~~
基于 replace 函数被调用的目标，创建一个新的 Parameter 实例。要覆写 `Parameter` 的属性，传递对应的参数。要移除 Parameter 的默认值和/或标注，传递 `Parameter.empty`。
~~~
    
    
~~~shell
>>> from inspect import Parameter
>>> param = Parameter('foo', Parameter.KEYWORD_ONLY, default=42)
>>> str(param)
'foo=42'

>>> str(param.replace()) # Will create a shallow copy of 'param'
'foo=42'

>>> str(param.replace(default=Parameter.empty, annotation='spam'))
"foo:'spam'"
~~~

Parameter objects are also supported by generic function [`copy.replace()`](copy.md#copy.replace "copy.replace").

在 3.4 版本发生变更: 在 Python 3.3 中， Parameter 对象当 `kind` 为 `POSITIONAL_ONLY` 时允许 `name` 被设置为 `None`。这现在已不再被允许。

_class _inspect.BoundArguments¶

    

~~~
调用 `Signature.bind()` 或 `Signature.bind_partial()` 的结果。容纳实参到函数的形参的映射。

arguments¶
~~~
    

~~~
一个形参名到实参值的可变映射。仅包含显式绑定的参数。对 `arguments` 的修改会反映到 `args` 和 `kwargs` 上。

应当在任何参数处理目的中与 `Signature.parameters` 结合使用。

备注

`Signature.bind()` 和 `Signature.bind_partial()` 中采用默认值的参数被跳过。然而，如果有需要的话，可以使用 `BoundArguments.apply_defaults()` 来添加它们。

在 3.9 版本发生变更:  `arguments` 现在的类型是 [`dict`](stdtypes.md#dict "dict")。之前，它的类型是 [`collections.OrderedDict`](collections.md#collections.OrderedDict "collections.OrderedDict")。

args¶
~~~
    

~~~
位置参数的值的元组。由 `arguments` 属性动态计算。

kwargs¶
~~~
    

~~~
关键字参数值的字典。由 `arguments` 属性动态计算。

signature¶
~~~
    

~~~
向所属 `Signature` 对象的一个引用。

apply_defaults()¶
~~~
    

~~~
设置缺失的参数的默认值。

对于变长位置参数（`*args`），默认值是一个空元组。

对于变长关键字参数（`**kwargs`）默认值是一个空字典。
~~~
    
    
~~~shell
>>> def foo(a, b='ham', *args): pass
>>> ba = inspect.signature(foo).bind('spam')
>>> ba.apply_defaults()
>>> ba.arguments
{'a': 'spam', 'b': 'ham', 'args': ()}
~~~

在 3.5 版本加入.

`args` 和 `kwargs` 属性可以被用于调用函数：

    
    
~~~python
def test(a, *, b):
    ...

sig = signature(test)
ba = sig.bind(10, b=20)
test(*ba.args, **ba.kwargs)
~~~

参见

[**PEP 362**](https://peps.python.org/pep-0362/) \- 函数签名对象。

    

~~~
包含具体的规范，实现细节和样例。

## 类与函数¶

inspect.getclasstree( _classes_ , _unique =False_)¶
~~~
    

~~~
将给定的类的列表组织成嵌套列表的层级结构。每当一个内层列表出现时，它包含的类均派生自紧接着该列表之前的条目的类。每个条目均是一个二元组，包含一个类和它的基类组成的元组。如果 _unique_ 参数为真值，则给定列表中的每个类将恰有一个对应条目。否则，运用了多重继承的类和它们的后代将出现多次。

inspect.getfullargspec( _func_ )¶
~~~
    

~~~
获取一个 Python 函数的形参的名字和默认值。将返回一个 [具名元组](../glossary.md#term-named-tuple)：

`FullArgSpec(args, varargs, varkw, defaults, kwonlyargs, kwonlydefaults, annotations)`

_args_ 是一个位置参数的名字的列表。 _varargs_ 是 `*` 形参的名字或 `None` 表示不接受任意长位置参数时。 _varkw_ 是 `**` 参数的名字，或 `None` 表示不接受任意关键字参数。 _defaults_ 是一个包含了默认参数值的 _n_ 元组分别对应最后 _n_ 个位置参数，或 `None` 则表示没有默认值。 _kwonlyargs_ 是一个仅关键词参数列表，保持定义时的顺序。 _kwonlydefaults_ 是一个字典映射自 _kwonlyargs_ 中包含的形参名。 _annotations_ 是一个字典，包含形参值到标注的映射。其中包含一个特殊的键 `"return"` 代表函数返回值的标注（如果有的话）。

注意： `signature()` 和 Signature 对象 提供可调用对象内省更推荐的 API，并且支持扩展模块 API 中可能出现的额外的行为（比如仅限位置参数）。该函数被保留的主要原因是保持兼容 Python 2 的 `inspect` 模块 API。

在 3.4 版本发生变更: 该函数现在基于 `signature()` 但仍然忽略 `__wrapped__` 属性，并且在签名中包含绑定方法中的第一个绑定参数。

在 3.6 版本发生变更: 该方法在 Python 3.5 中曾因 `signature()` 被文档归为弃用。但该决定已被推翻以恢复一个明确受支持的标准接口，以便运用一份源码通用 Python 2/3 间遗留的 `getargspec()` API 的迁移。

在 3.7 版本发生变更: Python 从 3.7 版起才显式地保证了它保持仅关键字参数的定义顺序，尽管实践上在 Python 3 中一直保持了这个顺序。

inspect.getargvalues( _frame_ )¶
~~~
    

~~~
获取传入特定的帧的实参的信息。将返回一个 [具名元组](../glossary.md#term-named-tuple) `ArgInfo(args, varargs, keywords, locals)`。 _args_ 是一个参数名字的列表。 _varargs_ 和 _keyword_ 是 `*` 和 `**` 参数的名字或 `None`。 _locals_ 是给定的帧的局部环境字典。

备注

该函数因疏忽在 Python 3.5 中被错误地标记为弃用。

inspect.formatargvalues( _args_ [, _varargs_ , _varkw_ , _locals_ , _formatarg_ , _formatvarargs_ , _formatvarkw_ , _formatvalue_ ])¶
~~~
    

~~~
将 `getargvalues()` 返回的四个值格式化为美观的参数规格。 format* 的参数是对应的可选格式化函数以转化名字和值为字符串。

备注

该函数因疏忽在 Python 3.5 中被错误地标记为弃用。

inspect.getmro( _cls_ )¶
~~~
    

~~~
返回由类 cls 的全部基类按方法解析顺序组成的元组，包括 cls 本身。所有类不会在此元组中出现多于一次。注意方法解析顺序取决于 cls 的类型。除非使用一个非常奇怪的用户定义元类型，否则 cls 会是元组的第一个元素。

inspect.getcallargs( _func_ , _/_ , _* args_, _** kwds_)¶
~~~
    

~~~
仿照调用方式绑定 _args_ 和 _kwds_ 到 Python 函数或方法 _func_ 参数名。对于绑定方法，也绑定第一个参数（通常命名为 `self`）到关联的实例。返回一个字典，映射自参数名（包括可能存在的 `*` 和 `**` 参数）到他们对应于 _args_ 和 _kwds_ 中的值。假使 _func_ 被错误地调用，即是说 `func(*args, **kwds)` 会因函数签名不一致引发一个异常，那么也会引发一个相同种类的异常，并附上相同或类似的消息。例如：
~~~
    
    
~~~shell
>>> from inspect import getcallargs
>>> def f(a, b=1, *pos, **named):
...     pass
...
>>> getcallargs(f, 1, 2, 3) == {'a': 1, 'named': {}, 'b': 2, 'pos': (3,)}
True
>>> getcallargs(f, a=2, x=4) == {'a': 2, 'named': {'x': 4}, 'b': 1, 'pos': ()}
True
>>> getcallargs(f)
Traceback (most recent call last):
...
TypeError: f() missing 1 required positional argument: 'a'
~~~

在 3.2 版本加入.

自 3.5 版本弃用: 改使用 `Signature.bind()` 和 `Signature.bind_partial()`。

inspect.getclosurevars( _func_ )¶

    

~~~
获取自 Python 函数或方法 _func_ 引用的外部名字到它们的值的映射。返回一个 [具名元组](../glossary.md#term-named-tuple) `ClosureVars(nonlocals, globals, builtins, unbound)`。 _nonlocals_ 映射引用的名字到词法闭包变量， _globals_ 映射到函数的模块级全局， _builtins_ 映射到函数体内可见的内置变量。 _unbound_ 是在函数中引用但不能解析到给定的模块全局和内置变量的名字的集合。

如果 _func_ 不是 Python 函数或方法，将引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

在 3.3 版本加入.

inspect.unwrap( _func_ , _*_ , _stop =None_)¶
~~~
    

~~~
获取 _func_ 所包装的对象。它追踪 `__wrapped__` 属性链并返回最后一个对象。

_stop_ 是一个可选的回调，接受包装链的一个对象作为唯一参数，以允许通过让回调返回真值使解包装更早中止。如果回调不曾返回一个真值，将如常返回链中的最后一个对象。例如， `signature()` 使用该参数来在遇到具有 `__signature__` 参数的对象时停止解包装。

如果遇到循环，则引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

在 3.4 版本加入.

inspect.get_annotations( _obj_ , _*_ , _globals =None_, _locals =None_, _eval_str =False_)¶
~~~
    

~~~
计算一个对象的标注字典。

`obj` 可以是一个可调用对象、类或模块。传入其他类型的对象将引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

返回一个字典。 `get_annotations()` 每次调用均返回一个新的字典；对同一个对象调用两次将获得两个不同但相等的字典。

该函数帮助你处理若干细节：

  * 如果 `eval_str` 为真， `str` 类型的值将会通过 [`eval()`](functions.md#eval "eval") 来反字符串化。这被设计用于字符串化标注（`from __future__ import annotations`）。

  * 如果 `obj` 不包含一个标注字典，返回一个空字典。（函数和方法永远包含一个标注字典；类、模块和其他类型的可调用对象则可能没有。）

  * 对于类，忽略继承而来的标注。如果一个类不包含自己的标注字典，返回一个空字典。

  * 因安全原因，所有对于对象成员和字典值的访问将通过 `getattr()` 和 `dict.get()` 完成。

  * 请确保始终、始终、始终返回一个新创建的字典。

`eval_str` 控制 `str` 类型的值是否应该替换为对其调用 [`eval()`](functions.md#eval "eval") 的结果：

  * 如果 eval_str 为真， [`eval()`](functions.md#eval "eval") 会被调用于 `str` 类型。（注意 `get_annotations` 并不捕获异常；如果 [`eval()`](functions.md#eval "eval") 返回一个错误，它会将栈展开跳过 `get_annotations` 调用。）

  * 如果 eval_str 为假（默认值）， `str` 类型的值将不会被改变。

`globals` 和 `locals` 会被直接传入函数 [`eval()`](functions.md#eval "eval")，详见 [`eval()`](functions.md#eval "eval") 的文档。如果 `globals` 或者 `locals` 是 `None`，则改函数视 `type(obj)` 而定，可能将相应的值替换为一个上下文有关的默认值。

  * 如果 `obj` 是一个模块， `globals` 默认为 `obj.__dict__`。

  * 如果 `obj` 是一个类， `globals` 默认值为 `sys.modules[obj.__module__].__dict__` 并且 `locals` 默认值为 `obj` 的类命名空间。

  * 如果 `obj` 是一个可调用对象， `globals` 默认为 `obj.__globals__`，而如果 `obj` 是一个包装过的函数，则它会先被解包装。

调用 `get_annotations` 是获取任何对象的标注字典的最佳实践。关于标注的最佳实践的更多信息，参见 [对象注解属性的最佳实践](../howto/annotations.md#annotations-howto)。

在 3.10 版本加入.

## 解释器栈¶

下列函数有些将返回 `FrameInfo` 对象。 出于向下兼容性考虑这些对象允许在所有属性上执行元组类操作但 `positions` 除外。 此行为已被弃用并可能会在未来被移除。

_class _inspect.FrameInfo¶
~~~
    

~~~
frame¶
~~~
    

~~~
记录所对应的 [帧对象](../reference/datamodel.md#frame-objects)。

filename¶
~~~
    

~~~
关联到由此记录所对应的帧对象所执行的代码的文件名称。

lineno¶
~~~
    

~~~
关联到由此记录所对应的帧对象所执行的代码的当前行的行号。

function¶
~~~
    

~~~
由此记录所对应的帧所执行的函数的名称。

code_context¶
~~~
    

~~~
来自由此记录所对应的帧所执行的源代码的上下文行组成的列表。

index¶
~~~
    

~~~
在 `code_context` 列表中执行的当前行的索引号。

positions¶
~~~
    

~~~
包含关联到由此记录所对应的指令的起始行号，结束行号，起始列偏移量和结束列偏移量的 [`dis.Positions`](dis.md#dis.Positions "dis.Positions") 对象。

在 3.5 版本发生变更: 返回一个 [named tuple](../glossary.md#term-named-tuple) 而非 [`tuple`](stdtypes.md#tuple "tuple")。

在 3.11 版本发生变更: `FrameInfo` 现在是一个类实例（以便与之前的 [named tuple](../glossary.md#term-named-tuple) 保持向下兼容）。

_class _inspect.Traceback¶
~~~
    

~~~
filename¶
~~~
    

~~~
关联到由此回溯所对应的帧所执行的代码的文件名称。

lineno¶
~~~
    

~~~
关联到由此回溯所对应的帧所执行的代码的当前行的行号。

function¶
~~~
    

~~~
由此回溯所对应的帧所执行的函数的名称。

code_context¶
~~~
    

~~~
来自由此回溯所对应的帧所执行的源代码的上下文行组成的列表。

index¶
~~~
    

~~~
在 `code_context` 列表中执行的当前行的索引号。

positions¶
~~~
    

~~~
包含关联到此回溯所对应的帧所执行的指令的起始行号，结束行号，起始列偏移量和结束列偏移量的 [`dis.Positions`](dis.md#dis.Positions "dis.Positions") 对象。

在 3.11 版本发生变更: `Traceback` 现在是一个类实例（以便与之前的 [named tuple](../glossary.md#term-named-tuple) 保持向下兼容）。

备注

保留帧对象的引用（可见于这些函数返回的帧记录的第一个元素）会导致你的程序产生循环引用。每当一个循环引用被创建，所有可从产生循环的对象访问的对象的生命周期将会被大幅度延长，即便 Python 的可选的循环检测器被启用。如果这类循环必须被创建，确保它们会被显式地打破以避免对象销毁被延迟从而导致占用内存增加。

尽管循环检测器能够处理这种情况，这些帧（包括其局部变量）的销毁可以通过在 [`finally`](../reference/compound_stmts.md#finally) 子句中移除循环来产生确定的行为。对于循环检测器在编译 Python 时被禁用或者使用 [`gc.disable()`](gc.md#gc.disable "gc.disable") 时，这样处理更加尤为重要。比如：
~~~
    
    
~~~python
def handle_stackframe_without_leak():
    frame = inspect.currentframe()
    try:
        # do something with the frame
    finally:
        del frame
~~~

如果你希望保持帧更长的时间（比如在之后打印回溯），你也可以通过 [`frame.clear()`](datamodel.md#frame.clear "frame.clear") 方法打破循环引用。

大部分这些函数支持的可选的 _context_ 参数指定返回时包含的上下文的行数，以当前行为中心。

inspect.getframeinfo( _frame_ , _context =1_)¶

    

~~~
获取关于帧或回溯对象的信息。 将返回一个 `Traceback` 对象。

在 3.11 版本发生变更: 将返回一个 `Traceback` 对象而非具名元组。

inspect.getouterframes( _frame_ , _context =1_)¶
~~~
    

~~~
获取某个帧及其所有外部帧的 `FrameInfo` 对象的列表。 这些帧代表导致 _frame_ 被创建的一系列调用。 返回的列中的第一个条目代表 _frame_ ；最后一个条目代表在 _frame_ 的栈上的最外层调用。

在 3.5 版本发生变更: 返回一个 [具名元组](../glossary.md#term-named-tuple) `FrameInfo(frame, filename, lineno, function, code_context, index)` 的列表。

在 3.11 版本发生变更: 将返回一个 `FrameInfo` 对象的列表。

inspect.getinnerframes( _traceback_ , _context =1_)¶
~~~
    

~~~
获取一个回溯所在的帧及其所有内部帧的 `FrameInfo` 对象的列表。 这些帧代表作为 _frame_ 的后续所执行的调用。 列表中的第一个条目代表 _traceback_ ；最后一个条目代表引发异常的位置。

在 3.5 版本发生变更: 返回一个 [具名元组](../glossary.md#term-named-tuple) `FrameInfo(frame, filename, lineno, function, code_context, index)` 的列表。

在 3.11 版本发生变更: 将返回一个 `FrameInfo` 对象的列表。

inspect.currentframe()¶
~~~
    

~~~
返回调用者的栈帧对应的帧对象。

**CPython 实现细节：** 该函数依赖于 Python 解释器对于栈帧的支持，这并非在 Python 的所有实现中被保证。该函数在不支持Python 栈帧的实现中运行会返回 `None`。

inspect.stack( _context =1_)¶
~~~
    

~~~
返回调用者的栈的 `FrameInfo` 对象的列表。 返回的列表中的第一个条目代表调用者；最后一个条目代表栈上的最外层调用。

在 3.5 版本发生变更: 返回一个 [具名元组](../glossary.md#term-named-tuple) `FrameInfo(frame, filename, lineno, function, code_context, index)` 的列表。

在 3.11 版本发生变更: 将返回一个 `FrameInfo` 对象的列表。

inspect.trace( _context =1_)¶
~~~
    

~~~
返回介于当前帧和引发了当前正在处理的异常所在的帧之间的栈的 `FrameInfo` 对象的列表。 列表中的第一个条目代表调用者；最后一个条目代表引发异常的位置。

在 3.5 版本发生变更: 返回一个 [具名元组](../glossary.md#term-named-tuple) `FrameInfo(frame, filename, lineno, function, code_context, index)` 的列表。

在 3.11 版本发生变更: 将返回一个 `FrameInfo` 对象的列表。

## 静态地获取属性¶

[`getattr()`](functions.md#getattr "getattr") 和 [`hasattr()`](functions.md#hasattr "hasattr") 都可能会在获取或者判断属性是否存在时触发代码执行。描述符，就和特性一样，会被调用， `__getattr__()` 和 `__getattribute__()` 可能会被调用。

对于你想要静态地内省的情况，比如文档工具，这会显得不方便。 `getattr_static()` 拥有与 [`getattr()`](functions.md#getattr "getattr") 相同的签名，但避免了获取属性时执行代码。

inspect.getattr_static( _obj_ , _attr_ , _default =None_)¶
~~~
    

~~~
获取属性而不触发描述器协议的动态查找能力 `__getattr__()` 或 `__getattribute__()`。

注意：该函数可能无法获取 getattr 能获取的全部的属性（比如动态地创建的属性），并且可能发现一些 getattr 无法找到的属性（比如描述器会引发 AttributeError）。它也能够返回描述器对象本身而非实例成员。

如果实例的 [`__dict__`](stdtypes.md#object.__dict__ "object.__dict__") 被其他成员遮盖（比如一个特性）则该函数无法找到实例成员。

在 3.2 版本加入.

`getattr_static()` 不解析描述器。比如槽描述器或 C 语言中实现的 getset 描述器。该描述器对象会被直接返回，而不处理底层属性。

你可以用类似下方的代码的方法处理此事。注意，对于任意 getset 描述符，使用这段代码仍可能触发代码执行。
~~~
    
    
~~~
# example code for resolving the builtin descriptor types
class _foo:
    __slots__ = ['foo']

slot_descriptor = type(_foo.foo)
getset_descriptor = type(type(open(__file__)).name)
wrapper_descriptor = type(str.__dict__['__add__'])
descriptor_types = (slot_descriptor, getset_descriptor, wrapper_descriptor)

result = getattr_static(some_object, 'foo')
if type(result) in descriptor_types:
    try:
        result = result.__get__()
    except AttributeError:
        # descriptors can raise AttributeError to
        # indicate there is no underlying value
        # in which case the descriptor itself will
        # have to do
        pass
~~~

## 生成器、协程和异步生成器的当前状态¶

当实现协程调度器或其他更高级的生成器用途时，判断一个生成器是正在执行、等待启动或继续或执行，又或者已经被终止是非常有用的。 `getgeneratorstate()` 允许方便地判断一个生成器的当前状态。

inspect.getgeneratorstate( _generator_ )¶

    

~~~
获取生成器迭代器的当前状态。

可能的状态是：
~~~
    

~~~
  * GEN_CREATED：等待开始执行。

  * GEN_RUNNING：正在被解释器执行。

  * GEN_SUSPENDED：当前挂起于一个 yield 表达式。

  * GEN_CLOSED：执行已经完成。

在 3.2 版本加入.

inspect.getcoroutinestate( _coroutine_ )¶
~~~
    

~~~
获取协程对象的当前状态。该函数设计为用于使用 [`async def`](../reference/compound_stmts.md#async-def) 函数创建的协程函数，但也能接受任何包括 `cr_running` 和 `cr_frame` 的类似协程的对象。

可能的状态是：
~~~
    

~~~
  * CORO_CREATED：等待开始执行。

  * CORO_RUNNING：当前正在被解释器执行。

  * CORO_SUSPENDED：当前挂起于一个 await 表达式。

  * CORO_CLOSED：执行已经完成。

在 3.5 版本加入.

inspect.getasyncgenstate( _agen_ )¶
~~~
    

~~~
获取一个异步生成器对象的当前状态。 该函数设计为用于由 [`async def`](../reference/compound_stmts.md#async-def) 函数创建的使用 [`yield`](../reference/simple_stmts.md#yield) 语句的异步迭代器对象，但也能接受任何具有 `ag_running` 和 `ag_frame` 属性的类似异步生成器的对象。

可能的状态是：
~~~
    

~~~
  * AGEN_CREATED: 等待开始执行。

  * AGEN_RUNNING: 当前正在被解释器执行。

  * AGEN_SUSPENDED: 当前在 yield 表达式上挂起。

  * AGEN_CLOSED: 执行已经完成。

在 3.12 版本加入.

生成器当前的内部状态也可以被查询。这通常在测试目的中最为有用，来保证内部状态如预期一样被更新：

inspect.getgeneratorlocals( _generator_ )¶
~~~
    

~~~
获取 _generator_ 里的实时局部变量到当前值的映射。返回一个由名字映射到值的字典。这与在生成器的主体内调用 [`locals()`](functions.md#locals "locals") 是等效的，并且相同的警告也适用。

如果 _generator_ 是一个没有关联帧的 [生成器](../glossary.md#term-generator)，则返回一个空字典。如果 _generator_ 不是一个 Python 生成器对象，则引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

**CPython 实现细节：** 该函数依赖于生成器为内省暴露一个 Python 栈帧，这并非在 Python 的所有实现中被保证。在这种情况下，该函数将永远返回一个空字典。

在 3.3 版本加入.

inspect.getcoroutinelocals( _coroutine_ )¶
~~~
    

~~~
该函数可类比于 `getgeneratorlocals()`，只是作用于由 [`async def`](../reference/compound_stmts.md#async-def) 函数创建的协程。

在 3.5 版本加入.

inspect.getasyncgenlocals( _agen_ )¶
~~~
    

~~~
此函数类似于 `getgeneratorlocals()`，但只适用于由 [`async def`](../reference/compound_stmts.md#async-def) 函数创建的使用 [`yield`](../reference/simple_stmts.md#yield) 语句的异步生成器对象。

在 3.12 版本加入.

## 代码对象位标志¶

Python 代码对象有一个 `co_flags` 属性，它是下列标志的位图。

inspect.CO_OPTIMIZED¶
~~~
    

~~~
代码对象已经经过优化，会采用快速局部变量。

inspect.CO_NEWLOCALS¶
~~~
    

~~~
如果被置位，当代码对象被执行时会创建一个新的字典作为帧的 `f_locals`。

inspect.CO_VARARGS¶
~~~
    

~~~
代码对象拥有一个变长位置形参（类似 `*args`）。

inspect.CO_VARKEYWORDS¶
~~~
    

~~~
代码对象拥有一个可变关键字形参 (类似 `**kwrags`)。

inspect.CO_NESTED¶
~~~
    

~~~
该标志当代码对象是一个嵌套函数时被置位。

inspect.CO_GENERATOR¶
~~~
    

~~~
当代码对象是一个生成器函数，即调用时会返回一个生成器对象，则该标志被置位。

inspect.CO_COROUTINE¶
~~~
    

~~~
当代码对象是一个协程函数时被置位。当代码对象被执行时它返回一个协程。详见 [**PEP 492**](https://peps.python.org/pep-0492/)。

在 3.5 版本加入.

inspect.CO_ITERABLE_COROUTINE¶
~~~
    

~~~
该标志被用于将生成器转变为基于生成器的协程。包含此标志的生成器对象可以被用于 `await` 表达式，并可以 `yield from` 协程对象。详见 [**PEP 492**](https://peps.python.org/pep-0492/)。

在 3.5 版本加入.

inspect.CO_ASYNC_GENERATOR¶
~~~
    

~~~
当代码对象是一个异步生成器函数时该标志被置位。当代码对象被运行时它将返回一个异步生成器对象。详见 [**PEP 525**](https://peps.python.org/pep-0525/)。

在 3.6 版本加入.

备注

这些标志特指于 CPython，并且在其他 Python 实现中可能从未被定义。更进一步地说，这些标志是一种实现细节，并且可能在将来的 Python 发行中被移除或弃用。推荐使用 `inspect` 模块的公共 API 来进行任何内省需求。

## 缓冲区旗标¶

_class _inspect.BufferFlags¶
~~~
    

~~~
这是一个代表可被传给实现了 [缓冲区协议](../c-api/buffer.md#bufferobjects) 的对象的 [`__buffer__()`](../reference/datamodel.md#object.__buffer__ "object.__buffer__") 方法的旗标的 [`enum.IntFlag`](enum.md#enum.IntFlag "enum.IntFlag")。

这些旗标的含义的说明见 [缓冲区请求的类型](../c-api/buffer.md#buffer-request-types)。

SIMPLE¶
~~~
    

~~~
WRITABLE¶
~~~
    

~~~
FORMAT¶
~~~
    

~~~
ND¶
~~~
    

~~~
STRIDES¶
~~~
    

~~~
C_CONTIGUOUS¶
~~~
    

~~~
F_CONTIGUOUS¶
~~~
    

~~~
ANY_CONTIGUOUS¶
~~~
    

~~~
INDIRECT¶
~~~
    

~~~
CONTIG¶
~~~
    

~~~
CONTIG_RO¶
~~~
    

~~~
STRIDED¶
~~~
    

~~~
STRIDED_RO¶
~~~
    

~~~
RECORDS¶
~~~
    

~~~
RECORDS_RO¶
~~~
    

~~~
FULL¶
~~~
    

~~~
FULL_RO¶
~~~
    

~~~
READ¶
~~~
    

~~~
WRITE¶
~~~
    

~~~
在 3.12 版本加入.

## 命令行界面¶

`inspect` 模块也提供一个从命令行使用基本的内省能力。

默认地，命令行接受一个模块的名字并打印模块的源代码。也可通过后缀一个冒号和目标对象的限定名称来打印一个类或者一个函数。

\--details¶
~~~
    

~~~
