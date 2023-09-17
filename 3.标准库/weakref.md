# `weakref` \--- 弱引用¶

**源码：** [Lib/weakref.py](https://github.com/python/cpython/tree/3.12/Lib/weakref.py)

* * *

`weakref` 模块允许Python程序员创建对象的 _weak references_ 。

在下文中，术语 _referent_ 表示由弱引用引用的对象。

对对象的弱引用不能保证对象存活：当对像的引用只剩弱引用时， [garbage collection](../glossary.md#term-garbage-collection) 可以销毁引用并将其内存重用于其他内容。但是，在实际销毁对象之前，即使没有强引用，弱引用也一直能返回该对象。

弱引用的主要用途是实现保存大对象的高速缓存或映射，但又不希望大对象仅仅因为它出现在高速缓存或映射中而保持存活。

例如，如果您有许多大型二进制图像对象，则可能希望将名称与每个对象关联起来。如果您使用Python字典将名称映射到图像，或将图像映射到名称，则图像对象将保持活动状态，因为它们在字典中显示为值或键。 `weakref` 模块提供的 `WeakKeyDictionary` 和 `WeakValueDictionary` 类可以替代Python字典，使用弱引用来构造映射，这些映射不会仅仅因为它们出现在映射对象中而使对象保持存活。例如，如果一个图像对象是 `WeakValueDictionary` 中的值，那么当对该图像对象的剩余引用是弱映射对象所持有的弱引用时，垃圾回收可以回收该对象并将其在弱映射对象中相应的条目删除。

`WeakKeyDictionary` 和 `WeakValueDictionary` 在它们的实现中使用弱引用，在弱引用上设置回调函数，当键或值被垃圾回收回收时通知弱字典。 `WeakSet` 实现了 [`set`](stdtypes.md#set "set") 接口，但像 `WeakKeyDictionary` 一样，只持有其元素的弱引用。

`finalize` 提供了注册一个对象被垃圾收集时要调用的清理函数的方式。这比在原始弱引用上设置回调函数更简单，因为模块会自动确保对象被回收前终结器一直保持存活。

这些弱容器类型之一或者 `finalize` 就是大多数程序所需要的 - 通常不需要直接创建自己的弱引用。`weakref` 模块暴露了低级机制，以便于高级用途。

并非所有对象都可以被弱引用。 支持弱引用的对象包括类实例、用 Python（而非用 C）编写的函数、实例方法、集合、冻结集合、某些 [文件对象](../glossary.md#term-file-object)、[生成器](../glossary.md#term-generator)、类型对象、套接字、数组、双端队列、正则表达式模式对象以及代码对象。

在 3.2 版本发生变更: 添加了对thread.lock，threading.Lock和代码对象的支持。

几个内建类型如 [`list`](stdtypes.md#list "list") 和 [`dict`](stdtypes.md#dict "dict") 不直接支持弱引用，但可以通过子类化添加支持:

    
    
~~~
class Dict(dict):
    pass

obj = Dict(red=1, green=2, blue=3)   # this object is weak referenceable
~~~

**CPython 实现细节：** 其他内置类型例如 [`tuple`](stdtypes.md#tuple "tuple") 和 [`int`](functions.md#int "int") 不支持弱引用，即使通过子类化也不支持。

Extension types can easily be made to support weak references; see [弱引用支持](newtypes.md#weakref-support).

当为某个给定类型定义了 `__slots__` 时，弱引用支持会被禁用，除非将 `'__weakref__'` 字符串也加入到 `__slots__` 声明的字符串序列中。 请参阅 [__slots__ 文档](datamodel.md#slots) 了解详情。

_class _weakref.ref( _object_ [, _callback_ ])¶

    

~~~
返回对 _对象_ 的弱引用。如果原始对象仍然存活，则可以通过调用引用对象来检索原始对象；如果引用的原始对象不再存在，则调用引用对象将得到 [`None`](constants.md#None "None") 。如果提供了 _回调_ 而且值不是 [`None`](constants.md#None "None") ，并且返回的弱引用对象仍然存活，则在对象即将终结时将调用回调;弱引用对象将作为回调的唯一参数传递；指示物将不再可用。

许多弱引用也允许针对相同对象来构建。 为每个弱引用注册的回调将按从最近注册的回调到最早注册的回调的顺序被调用。

回调所引发的异常将记录于标准错误输出，但无法被传播；它们会按与对象的 `__del__()` 方法所引发的异常相同的方式被处理。

如果 _object_ 可哈希，则弱引用也为 [hashable](../glossary.md#term-hashable)。 即使在 _object_ 被删除之后它们仍将保持其哈希值。 如果 [`hash()`](functions.md#hash "hash") 在 _object_ 被删除之后才首次被调用，则该调用将引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

弱引用支持相等检测，但不支持排序比较。 如果被引用对象仍然存在，两个引用具有与它们的被引用对象一致的相等关系（无论 _callback_ 是否相同）。 如果删除了任一被引用对象，则仅在两个引用对象为同一对象时两者才相等。

这是一个可子类化的类型而非一个工厂函数。

__callback__¶
~~~
    

~~~
这个只读属性会返回当前关联到弱引用的回调。 如果回调不存在或弱引用的被引用对象已不存在，则此属性的值为 `None`。

在 3.4 版本发生变更: 添加了 `__callback__` 属性。

weakref.proxy( _object_ [, _callback_ ])¶
~~~
    

~~~
向 _object_ 返回一个使用弱引用的代理。 此函数支持在大多数上下文中使用代理而不要求显式地对所使用的弱引用对象解除引用。 返回的对象类型将为 `ProxyType` 或 `CallableProxyType`，具体取决于 _object_ 是否为可调用对象。 代理对象不属于 [hashable](../glossary.md#term-hashable) 对象，不管被引用的对象是否可哈希；这可以避免与它们的基本可变性质相关的多种问题，并可防止它们被用作字典键。 _callback_ 与 `ref()` 函数的同名形参含义相同。

在引用被作为垃圾回收后访问代理对象的属性将引发 [`ReferenceError`](exceptions.md#ReferenceError "ReferenceError")。

在 3.8 版本发生变更: 扩展代理对象所支持的运算符，包括矩阵乘法运算符 `@` 和 `@=`。

weakref.getweakrefcount( _object_ )¶
~~~
    

~~~
返回指向 _object_ 的弱引用和代理的数量。

weakref.getweakrefs( _object_ )¶
~~~
    

~~~
返回由指向 _object_ 的所有弱引用和代理构成的列表。

_class _weakref.WeakKeyDictionary([ _dict_ ])¶
~~~
    

~~~
弱引用键的映射类。 当不再存在对键的强引用时，字典中的条目将被丢弃。 这可被用来将额外数据关联到一个应用中其他部分所拥有的对象而无需在那些对象中添加属性。 这对于重载了属性访问的对象来说特别有用。

请注意当把一个对应值与现有键相等的键（但是标识号不相等）插入字典时，它会替换该值但不会替换现有的键。 由于这一点，当删除对原来的键的引用时，也将同时删除字典中的对应条目:
~~~
    
    
~~~shell
>>> class T(str): pass
...
>>> k1, k2 = T(), T()
>>> d = weakref.WeakKeyDictionary()
>>> d[k1] = 1   # d = {k1: 1}
>>> d[k2] = 2   # d = {k1: 2}
>>> del k1      # d = {}
~~~

一种变通做法是在重新赋值之前先移除键:

    
    
~~~shell
>>> class T(str): pass
...
>>> k1, k2 = T(), T()
>>> d = weakref.WeakKeyDictionary()
>>> d[k1] = 1   # d = {k1: 1}
>>> del d[k1]
>>> d[k2] = 2   # d = {k2: 2}
>>> del k1      # d = {k2: 2}
~~~

在 3.9 版本发生变更: 增加了对 `|` 和 `|=` 运算符的支持，相关说明见 [**PEP 584**](https://peps.python.org/pep-0584/)。

`WeakKeyDictionary` 对象具有一个额外方法可以直接公开内部引用。 这些引用不保证在它们被使用时仍然保持“存活”，因此这些引用的调用结果需要在使用前进行检测。 此方法可用于避免创建会导致垃圾回收器将保留键超出实际需要时长的引用。

WeakKeyDictionary.keyrefs()¶

    

~~~
返回包含对键的弱引用的可迭代对象。

_class _weakref.WeakValueDictionary([ _dict_ ])¶
~~~
    

~~~
弱引用值的映射类。 当不再存在对该值的强引用时，字典中的条目将被丢弃。

在 3.9 版本发生变更: 增加了对 `|` 和 `|=` 运算符的支持，相关说明见 [**PEP 584**](https://peps.python.org/pep-0584/)。

`WeakValueDictionary` 对象具有一个额外方法，此方法存在与 `WeakKeyDictionary` 对象的 `keyrefs()` 方法相同的问题。

WeakValueDictionary.valuerefs()¶
~~~
    

~~~
返回包含对值的弱引用的可迭代对象。

_class _weakref.WeakSet([ _elements_ ])¶
~~~
    

~~~
保持对其元素弱引用的集合类。 当不再有对某个元素的强引用时元素将被丢弃。

_class _weakref.WeakMethod( _method_ [, _callback_ ])¶
~~~
    

~~~
一个模拟对绑定方法（即在类中定义并在实例中查找的方法）进行弱引用的自定义 `ref` 子类。 由于绑定方法是临时性的，标准弱引用无法保持它。 `WeakMethod` 包含特别代码用来重新创建绑定方法，直到对象或初始函数被销毁:
~~~
    
    
~~~shell
>>> class C:
...     def method(self):
...         print("method called!")
...
>>> c = C()
>>> r = weakref.ref(c.method)
>>> r()
>>> r = weakref.WeakMethod(c.method)
>>> r()
<bound method C.method of <__main__.C object at 0x7fc859830220>>
>>> r()()
method called!
>>> del c
>>> gc.collect()
0
>>> r()
>>>
~~~

_callback_ 与 `ref()` 函数的同名形参含义相同。

在 3.4 版本加入.

_class _weakref.finalize( _obj_ , _func_ , _/_ , _* args_, _** kwargs_)¶

    

~~~
返回一个可调用的终结器对象，该对象将在 _obj_ 作为垃圾回收时被调用。 与普通的弱引用不同，终结器将总是存活，直到引用对象被回收，这极大地简化了生存期管理。

终结器总是被视为 _存活_ 直到它被调用（显式调用或在垃圾回收时隐式调用），调用之后它将 _死亡_ 。 调用存活的终结器将返回 `func(*arg, **kwargs)` 的求值结果，而调用死亡的终结器将返回 [`None`](constants.md#None "None")。

在垃圾收集期间由终结器回调所引发异常将显示于标准错误输出，但无法被传播。 它们会按与对象的 `__del__()` 方法或弱引用的回调所引发异常相同的方式被处理。

当程序退出时，剩余的存活终结器会被调用，除非它们的 [`atexit`](atexit.md#module-atexit "atexit: Register and execute cleanup functions.") 属性已被设为假值。 它们会按与创建时相反的顺序被调用。

终结器在 [interpreter shutdown](../glossary.md#term-interpreter-shutdown) 的后期绝不会发起调用其回调函数，此时模块全局变量很可能已被替换为 [`None`](constants.md#None "None")。

__call__()¶
~~~
    

~~~
如果 _self_ 为存活状态则将其标记为已死亡，并返回调用 `func(*args, **kwargs)` 的结果。 如果 _self_ 已死亡则返回 [`None`](constants.md#None "None")。

detach()¶
~~~
    

~~~
如果 _self_ 为存活状态则将其标记为已死亡，并返回元组 `(obj, func, args, kwargs)`。 如果 _self_ 已死亡则返 [`None`](constants.md#None "None")。

peek()¶
~~~
    

~~~
如果 _self_ 为存活状态则返回元组 `(obj, func, args, kwargs)`。 如果 _self_ 已死亡则返回 [`None`](constants.md#None "None")。

alive¶
~~~
    

~~~
如果终结器为存活状态则该特征属性为真值，否则为假值。

atexit¶
~~~
    

~~~
一个可写的布尔型特征属性，默认为真值。 当程序退出时，它会调用所有 `atexit` 为真值的剩余存活终结器。 它们会按与创建时相反的顺序被调用。

备注

很重要的一点是确保 _func_ , _args_ 和 _kwargs_ 不拥有任何对 _obj_ 的引用，无论是直接的或是间接的，否则的话 _obj_ 将永远不会被作为垃圾回收。 特别地， _func_ 不应当是 _obj_ 的一个绑定方法。

在 3.4 版本加入.

weakref.ReferenceType¶
~~~
    

~~~
弱引用对象的类型对象。

weakref.ProxyType¶
~~~
    

~~~
不可调用对象的代理的类型对象。

weakref.CallableProxyType¶
~~~
    

~~~
可调用对象的代理的类型对象。

weakref.ProxyTypes¶
~~~
    

~~~
包含所有代理的类型对象的序列。 这可以用于更方便地检测一个对象是否是代理，而不必依赖于两种代理对象的名称。

参见

[**PEP 205**](https://peps.python.org/pep-0205/) \- 弱引用
~~~
    

~~~
此特性的提议和理由，包括早期实现的链接和其他语言中类似特性的相关信息。

## 弱引用对象¶

弱引用对象没有 `ref.__callback__` 以外的方法和属性。 一个弱引用对象如果存在，就允许通过调用它来获取引用:
~~~
    
    
~~~shell
>>> import weakref
>>> class Object:
...     pass
...
>>> o = Object()
>>> r = weakref.ref(o)
>>> o2 = r()
>>> o is o2
True
~~~

如果引用已不存在，则调用引用对象将返回 [`None`](constants.md#None "None"):

    
    
~~~shell
>>> del o, o2
>>> print(r())
None
~~~

检测一个弱引用对象是否仍然存在应该使用表达式 `ref() is not None`。 通常，需要使用引用对象的应用代码应当遵循这样的模式:

    
    
~~~
# r is a weak reference object
o = r()
if o is None:
    # referent has been garbage collected
    print("Object has been deallocated; can't frobnicate.")
else:
    print("Object is still live!")
    o.do_something_useful()
~~~

使用单独的“存活”测试会在多线程应用中制造竞争条件；其他线程可能导致某个弱引用在该弱引用被调用前就失效；上述的写法在多线程应用和单线程应用中都是安全的。

特别版本的 `ref` 对象可以通过子类化来创建。 在 `WeakValueDictionary` 的实现中就使用了这种方式来减少映射中每个条目的内存开销。 这对于将附加信息关联到引用的情况最为适用，但也可以被用于在调用中插入额外处理来提取引用。

这个例子演示了如何将 `ref` 的一个子类用于存储有关对象的附加信息并在引用被访问时影响其所返回的值:

    
    
~~~
import weakref

class ExtendedRef(weakref.ref):
    def __init__(self, ob, callback=None, /, **annotations):
        super().__init__(ob, callback)
        self.__counter = 0
        for k, v in annotations.items():
            setattr(self, k, v)

    def __call__(self):
        """Return a pair containing the referent and the number of
        times the reference has been called.
        """
        ob = super().__call__()
        if ob is not None:
            self.__counter += 1
            ob = (ob, self.__counter)
        return ob
~~~

## 示例¶

这个简单的例子演示了一个应用如何使用对象 ID 来提取之前出现过的对象。 然后对象的 ID 可以在其它数据结构中使用，而无须强制对象保持存活，但处于存活状态的对象也仍然可以通过 ID 来提取。

    
    
~~~
import weakref

_id2obj_dict = weakref.WeakValueDictionary()

def remember(obj):
    oid = id(obj)
    _id2obj_dict[oid] = obj
    return oid

def id2obj(oid):
    return _id2obj_dict[oid]
~~~

## 终结器对象¶

使用 `finalize` 的主要好处在于它能更简便地注册回调函数，而无须保留所返回的终结器对象。 例如

    
    
~~~shell
>>> import weakref
>>> class Object:
...     pass
...
>>> kenny = Object()
>>> weakref.finalize(kenny, print, "You killed Kenny!")  
<finalize object at ...; for 'Object' at ...>
>>> del kenny
You killed Kenny!
~~~

终结器也可以被直接调用。 但是终结器最多只能对回调函数发起一次调用。

    
    
~~~shell
>>> def callback(x, y, z):
...     print("CALLBACK")
...     return x + y + z
...
>>> obj = Object()
>>> f = weakref.finalize(obj, callback, 1, 2, z=3)
>>> assert f.alive
>>> assert f() == 6
CALLBACK
>>> assert not f.alive
>>> f()                     # callback not called because finalizer dead
>>> del obj                 # callback not called because finalizer dead
~~~

你可以使用 `detach()` 方法来注销一个终结器。 该方法将销毁终结器并返回其被创建时传给构造器的参数。

    
    
~~~shell
>>> obj = Object()
>>> f = weakref.finalize(obj, callback, 1, 2, z=3)
>>> f.detach()                                           
(<...Object object ...>, <function callback ...>, (1, 2), {'z': 3})
>>> newobj, func, args, kwargs = _
>>> assert not f.alive
>>> assert newobj is obj
>>> assert func(*args, **kwargs) == 6
CALLBACK
~~~

除非你将 `atexit` 属性设为 [`False`](constants.md#False "False")，否则终结器在程序退出时如果仍然存活就将被调用。 例如

    
    
~~~shell
>>> obj = Object()
>>> weakref.finalize(obj, print, "obj dead or exiting")
<finalize object at ...; for 'Object' at ...>
>>> exit()
obj dead or exiting
~~~

## 比较终结器与 `__del__()` 方法¶

假设我们想创建一个类，用它的实例来代表临时目录。 当以下事件中的某一个发生时，这个目录应当与其内容一起被删除：

  * 对象被作为垃圾回收，

  * 对象的 `remove()` 方法被调用，或

  * 程序退出。

我们可以尝试使用 `__del__()` 方法来实现这个类，如下所示:

    
    
~~~
class TempDir:
    def __init__(self):
        self.name = tempfile.mkdtemp()

    def remove(self):
        if self.name is not None:
            shutil.rmtree(self.name)
            self.name = None

    @property
    def removed(self):
        return self.name is None

    def __del__(self):
        self.remove()
~~~

从 Python 3.4 开始，`__del__()` 方法不会再阻止循环引用被作为垃圾回收，并且模块全局变量在 [interpreter shutdown](../glossary.md#term-interpreter-shutdown) 期间不会被强制设为 [`None`](constants.md#None "None")。 因此这段代码在 CPython 上应该会正常运行而不会出现任何问题。

然而，`__del__()` 方法的处理会严重地受到具体实现的影响，因为它依赖于解释器垃圾回收实现方式的内部细节。

更健壮的替代方式可以是定义一个终结器，只引用它所需要的特定函数和对象，而不是获取对整个对象状态的访问权:

    
    
~~~
class TempDir:
    def __init__(self):
        self.name = tempfile.mkdtemp()
        self._finalizer = weakref.finalize(self, shutil.rmtree, self.name)

    def remove(self):
        self._finalizer()

    @property
    def removed(self):
        return not self._finalizer.alive
~~~

像这样定义后，我们的终结器将只接受一个对其完成正确清理目录任务所需细节的引用。 如果对象一直未被作为垃圾回收，终结器仍会在退出时被调用。

基于弱引用的终结器还具有另一项优势，就是它们可被用来为定义由第三方控制的类注册终结器，例如当一个模块被卸载时运行特定代码:

    
    
~~~
import weakref, sys
def unloading_module():
    # implicit reference to the module globals from the function body
weakref.finalize(sys.modules[__name__], unloading_module)
~~~

备注

如果当程序退出时你恰好在守护线程中创建终结器对象，则有可能该终结器不会在退出时被调用。 但是，在一个守护线程中 [`atexit.register()`](atexit.md#atexit.register "atexit.register"), `try: ... finally: ...` 和 `with: ...` 同样不能保证执行清理。

