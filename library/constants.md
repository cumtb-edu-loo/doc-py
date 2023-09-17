# 内置常量¶

有少数的常量存在于内置命名空间中。 它们是：

False¶

    

~~~
[`bool`](functions.md#bool "bool") 类型的假值。 给 `False` 赋值是非法的并会引发 [`SyntaxError`](exceptions.md#SyntaxError "SyntaxError")。

True¶
~~~
    

~~~
[`bool`](functions.md#bool "bool") 类型的真值。 给 `True` 赋值是非法的并会引发 [`SyntaxError`](exceptions.md#SyntaxError "SyntaxError")。

None¶
~~~
    

~~~
通常被用来代表空值的对象，例如未向某个函数传入默认参数时。 向 `None` 赋值是非法的并会引发 [`SyntaxError`](exceptions.md#SyntaxError "SyntaxError")。 `None` 是 [`NoneType`](types.md#types.NoneType "types.NoneType") 类型的唯一实例。

NotImplemented¶
~~~
    

~~~
一个应当由双目运算特殊方法（如 [`__eq__()`](../reference/datamodel.md#object.__eq__ "object.__eq__"), [`__lt__()`](../reference/datamodel.md#object.__lt__ "object.__lt__"), [`__add__()`](../reference/datamodel.md#object.__add__ "object.__add__"), [`__rsub__()`](../reference/datamodel.md#object.__rsub__ "object.__rsub__") 等）返回的特殊值，以表明该运算没有针对其他类型的实现；也可由原地双目运算特殊方法（如 [`__imul__()`](../reference/datamodel.md#object.__imul__ "object.__imul__"), [`__iand__()`](../reference/datamodel.md#object.__iand__ "object.__iand__") 等）出于同样的目的而返回。 `NotImplemented` 是 [`types.NotImplementedType`](types.md#types.NotImplementedType "types.NotImplementedType") 类型的唯一实例。

备注

当二进制（或就地）方法返回 `NotImplemented` 时，解释器将尝试对另一种类型（或其他一些回滚操作，取决于运算符）的反射操作。 如果所有尝试都返回 `NotImplemented` ，则解释器将引发适当的异常。 错误返回的 `NotImplemented` 将导致误导性错误消息或返回到Python代码中的 `NotImplemented` 值。

参见 [实现算术运算](numbers.md#implementing-the-arithmetic-operations) 为例。

备注

`NotImplementedError` 和 `NotImplemented` 不可互换，即使它们有相似的名称和用途。 有关何时使用它的详细信息，请参阅 [`NotImplementedError`](exceptions.md#NotImplementedError "NotImplementedError")。

在 3.9 版本发生变更: 作为布尔值来解读 `NotImplemented` 已被弃用。 虽然它目前会被解读为真值，但将同时发出 [`DeprecationWarning`](exceptions.md#DeprecationWarning "DeprecationWarning")。 它将在未来的 Python 版本中引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

Ellipsis¶
~~~
    

~~~
与省略号字面值 "`...`" 相同。 该特殊值主要是与用户定义的容器数据类型的扩展切片语法结合使用。 `Ellipsis` 是 [`types.EllipsisType`](types.md#types.EllipsisType "types.EllipsisType") 类型的唯一实例。

__debug__¶
~~~
    

~~~
如果 Python 没有以 [`-O`](../using/cmdline.md#cmdoption-O) 选项启动，则此常量为真值。 另请参见 [`assert`](../reference/simple_stmts.md#assert) 语句。

备注

变量名 `None`，`False`，`True` 和 `__ debug__` 无法重新赋值（赋值给它们，即使是属性名，将引发 [`SyntaxError`](exceptions.md#SyntaxError "SyntaxError") ），所以它们可以被认为是“真正的”常数。

## 由 [`site`](site.md#module-site "site: Module responsible for site-specific configuration.") 模块添加的常量¶

[`site`](site.md#module-site "site: Module responsible for site-specific configuration.") 模块（在启动期间自动导入，除非给出 [`-S`](../using/cmdline.md#cmdoption-S) 命令行选项）将几个常量添加到内置命名空间。 它们对交互式解释器 shell 很有用，并且不应在程序中使用。

quit( _code =None_)¶

exit( _code =None_)¶
~~~
    

~~~
当打印此对象时，会打印出一条消息，例如“Use quit() or Ctrl-D (i.e. EOF) to exit”，当调用此对象时，将使用指定的退出代码来引发 [`SystemExit`](exceptions.md#SystemExit "SystemExit")。

copyright¶

credits¶
~~~
    

~~~
打印或调用的对象分别打印版权或作者的文本。

license¶
~~~
    

~~~
