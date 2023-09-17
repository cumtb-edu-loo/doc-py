# `copy` \--- 浅层 (shallow) 和深层 (deep) 复制操作¶

**源代码:** [Lib/copy.py](https://github.com/python/cpython/tree/3.12/Lib/copy.py)

* * *

Python 的赋值语句不复制对象，而是创建目标和对象的绑定关系。对于自身可变，或包含可变项的集合，有时要生成副本用于改变操作，而不必改变原始对象。本模块提供了通用的浅层复制和深层复制操作，（如下所述）。

接口摘要：

copy.copy( _obj_ )¶

    

~~~
Return a shallow copy of _obj_.

copy.deepcopy( _obj_ [, _memo_ ])¶
~~~
    

~~~
Return a deep copy of _obj_.

copy.replace( _obj_ , _/_ , _** changes_)¶
~~~
    

~~~
Creates a new object of the same type as _obj_ , replacing fields with values from _changes_.

在 3.13 版本加入.

_exception _copy.Error¶
~~~
    

~~~
针对模块特定错误引发。

浅层与深层复制的区别仅与复合对象（即包含列表或类的实例等其他对象的对象）相关：

  * _浅层复制_ 构造一个新的复合对象，然后（在尽可能的范围内）将原始对象中找到的对象的 _引用_ 插入其中。

  * _深层复制_ 构造一个新的复合对象，然后，递归地将在原始对象里找到的对象的 _副本_ 插入其中。

深度复制操作通常存在两个问题, 而浅层复制操作并不存在这些问题：

  * 递归对象 (直接或间接包含对自身引用的复合对象) 可能会导致递归循环。

  * 由于深层复制会复制所有内容，因此可能会过多复制（例如本应该在副本之间共享的数据）。

`deepcopy()` 函数用以下方式避免了这些问题：

  * 保留在当前复制过程中已复制的对象的 "备忘录" （`memo`） 字典；以及

  * 允许用户定义的类重载复制操作或复制的组件集合。

此模块不会复制模块、方法、栈追踪、栈帧、文件、套接字、窗口以及任何相似的类型。 它会通过不加修改地返回原始对象来（浅层或深层地）“复制”函数和类；这与 [`pickle`](pickle.md#module-pickle "pickle: Convert Python objects to streams of bytes and back.") 模块处理这类问题的方式是兼容的。

制作字典的浅层复制可以使用 [`dict.copy()`](stdtypes.md#dict.copy "dict.copy") 方法，而制作列表的浅层复制可以通过赋值整个列表的切片完成，例如，`copied_list = original_list[:]`。

类可以使用与控制序列化（pickling）操作相同的接口来控制复制操作，关于这些方法的描述信息请参考 [`pickle`](pickle.md#module-pickle "pickle: Convert Python objects to streams of bytes and back.") 模块。实际上，`copy` 模块使用的正是从 [`copyreg`](copyreg.md#module-copyreg "copyreg: Register pickle support functions.") 模块中注册的 pickle 函数。

想要为一个类定义它自己的拷贝操作实现，可以通过定义特殊方法 `__copy__()` 和 `__deepcopy__()`。 调用前者以实现浅层拷贝操作；该方法不必传入额外参数。 调用后者以实现深层拷贝操作；它应转入一个参数，即 `memo` 字典。 如果 `__deepcopy__()` 实现需要创建一个组件的深层拷贝，它应当调用 `deepcopy()` 函数并以该组件作为第一个参数而以该 memo 字典作为第二个参数。。 memo 字典应当被当作不透明对象来处理。

Function `replace()` is more limited than `copy()` and `deepcopy()`, and only supports named tuples created by [`namedtuple()`](collections.md#collections.namedtuple "collections.namedtuple"), [`dataclasses`](dataclasses.md#module-dataclasses "dataclasses: Generate special methods on user-defined classes."), and other classes which define method `__replace__()`.

> copy.__replace__( _self_ , _/_ , _** changes_)
>  

`__replace__()` should create a new object of the same type, replacing fields with values from _changes_.

参见

模块 [`pickle`](pickle.md#module-pickle "pickle: Convert Python objects to streams of bytes and back.")
~~~
    

~~~
