# `copyreg` \--- 注册配合 [`pickle`](pickle.md#module-pickle "pickle: Convert Python objects to streams of bytes and back.") 模块使用的函数¶

**源代码:** [Lib/copyreg.py](https://github.com/python/cpython/tree/3.12/Lib/copyreg.py)

* * *

`copyreg` 模块提供了可在封存特定对象时使用的一种定义函数方式。 [`pickle`](pickle.md#module-pickle "pickle: Convert Python objects to streams of bytes and back.") 和 [`copy`](copy.md#module-copy "copy: Shallow and deep copy operations.") 模块会在封存/拷贝特定对象时使用这些函数。 此模块提供了非类对象构造器的相关配置信息。 这样的构造器可以是工厂函数或类实例。

copyreg.constructor( _object_ )¶

    

~~~
将 _object_ 声明为一个有效的构造器。 如果 _object_ 是不可调用的（因而不是一个有效的构造器）则会引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

copyreg.pickle( _type_ , _function_ , _constructor_ob =None_)¶
~~~
    

~~~
声明 _function_ 应当被用作 _type_ 类型的对象的“归约”函数。 _function_ 必须返回一个字符串或包含二至六个元素的元组。 请参阅 [`dispatch_table`](pickle.md#pickle.Pickler.dispatch_table "pickle.Pickler.dispatch_table") 了解有关 _function_ 的接口的更多细节。

_constructor_ob_ 形参是一个旧式特性并且现在会被忽略，但如果传入则它必须为一个可调用对象。

请注意一个 pickler 或 [`pickle.Pickler`](pickle.md#pickle.Pickler "pickle.Pickler") 的子类的 [`dispatch_table`](pickle.md#pickle.Pickler.dispatch_table "pickle.Pickler.dispatch_table") 属性也可以被用来声明约归函数。

## 示例¶

以下示例将会显示如何注册一个封存函数，以及如何来使用它：
~~~
    
    
~~~shell
>>> import copyreg, copy, pickle
>>> class C:
...     def __init__(self, a):
...         self.a = a
...
>>> def pickle_c(c):
...     print("pickling a C instance...")
...     return C, (c.a,)
...
>>> copyreg.pickle(C, pickle_c)
>>> c = C(1)
>>> d = copy.copy(c)  
pickling a C instance...
>>> p = pickle.dumps(c)  
pickling a C instance...
~~~

