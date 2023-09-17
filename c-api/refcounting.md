# 引用计数¶

本节介绍的函数和宏被用于管理 Python 对象的引用计数。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") Py_REFCNT([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    

~~~
获取 Python 对象 _o_ 的引用计数。

请注意返回的值可能并不真正反映对象的实际持有的引用数。 例如，有些对象是“永生”的并具有非常高的 refcount 值，这并不反映实际的引用数。 因此，除了 0 或 1 这两个值，不要依赖返回值的准确性。

使用 `Py_SET_REFCNT()` 函数来设置一个对象引用计数。

在 3.11 版本发生变更: 形参类型不再是 const [PyObject](structures.md#c.PyObject "PyObject")*。

在 3.10 版本发生变更: `Py_REFCNT()` 被改为内联的静态函数。

void Py_SET_REFCNT([PyObject](structures.md#c.PyObject "PyObject") *o, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") refcnt)¶  
~~~
    

~~~
将对象 _o_ 的引用计数器设为 _refcnt_ 。

请注意此函数不会影响 [永生](https://peps.python.org/pep-0683/) 对象。

在 3.9 版本加入.

在 3.12 版本发生变更: 永生对象不会被修改。

void Py_INCREF([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
表示为对象 _o_ 获取一个新的 [strong reference](../glossary.md#term-strong-reference)，指明该对象正在被使用且不应被销毁。

此函数通常被用来将 [borrowed reference](../glossary.md#term-borrowed-reference) 原地转换为 [strong reference](../glossary.md#term-strong-reference)。 `Py_NewRef()` 函数可被用来创建新的 [strong reference](../glossary.md#term-strong-reference)。

When done using the object, release is by calling `Py_DECREF()`.

此对象必须不为 `NULL`；如果你不能确定它不为 `NULL`，请使用 `Py_XINCREF()`。

不要预期此函数会以任何方式实际地改变 _o_ 。 至少对 [某些对象](https://peps.python.org/pep-0683/) 来说，此函数将没有任何效果。

在 3.12 版本发生变更: 永生对象不会被修改。

void Py_XINCREF([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
与 `Py_INCREF()` 类似，但对象 _o_ 可以为 `NULL`，在这种情况下此函数将没有任何效果。

另请参阅 `Py_XNewRef()`。

[PyObject](structures.md#c.PyObject "PyObject") *Py_NewRef([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.10._

为对象创建一个新的 [strong reference](../glossary.md#term-strong-reference): 在 _o_ 上调用 `Py_INCREF()` 并返回对象 _o_ 。

当不再需要这个 [strong reference](../glossary.md#term-strong-reference) 时，应当在其上调用 `Py_DECREF()` 来释放引用。

对象 _o_ 必须不为 `NULL`；如果 _o_ 可以为 `NULL` 则应改用 `Py_XNewRef()`。

例如：
~~~
    
    
~~~
Py_INCREF(obj);
self->attr = obj;
~~~

可以写成:

    
    
~~~
self->attr = Py_NewRef(obj);
~~~

另请参阅 `Py_INCREF()`。

在 3.10 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *Py_XNewRef([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.10._

类似于 `Py_NewRef()`，但对象 _o_ 可以为 NULL。

如果对象 _o_ 为 `NULL`，该函数也·将返回 `NULL`。

在 3.10 版本加入.

void Py_DECREF([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    

~~~
释放一个指向对象 _o_ 的 [strong reference](../glossary.md#term-strong-reference)，表明该引用不再被使用。

当最后一个 [strong reference](../glossary.md#term-strong-reference) 被释放时 (即对象的引用计数变为 0)，将会发起调用该对象所属类型的 deallocation 函数 (它必须不为 `NULL`)。

此函数通常被用于在退出作用域之前删除一个 [strong reference](../glossary.md#term-strong-reference)。

此对象必须不为 `NULL`；如果你不能确定它不为 `NULL`，请使用 `Py_XDECREF()`。

不要预期此函数会以任何方式实际地改变 _o_ 。 至少对 [某些对象](https://peps.python.org/pep-0683/) 来说，此函数将没有任何效果。

警告

释放函数会导致任意 Python 代码被发起调用（例如当一个带有 [`__del__()`](../reference/datamodel.md#object.__del__ "object.__del__") 方法的类实例被释放时就是如此）。 虽然这些代码中的异常不会被传播，但被执行的代码能够自由访问所有 Python 全局变量。 这意味着在调用 `Py_DECREF()` 之前任何可通过全局变量获取的对象都应该处于完好的状态。 例如，从一个列表中删除对象的代码应该将被删除对象的引用拷贝到一个临时变量中，更新列表数据结构，然后再为临时变量调用 `Py_DECREF()`。

在 3.12 版本发生变更: 永生对象不会被修改。

void Py_XDECREF([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
与 `Py_DECREF()` 类似，但对象 _o_ 可以为 `NULL`，在这种情况下此函数将没有任何效果。 来自 `Py_DECREF()` 的警告同样适用于此处。

void Py_CLEAR([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
释放一个指向对象 _o_ 的 [strong reference](../glossary.md#term-strong-reference)。 对象可以为 `NULL`，在此情况下该宏将没有任何效果；在其他情况下其效果与 `Py_DECREF()` 相同，区别在于其参数也会被设为 `NULL`。 针对 `Py_DECREF()` 的警告不适用于所传递的对象，因为该宏会细心地使用一个临时变量并在释放引用之前将参数设为 `NULL`。

当需要释放指向一个在垃圾回收期间可能被会遍历的对象的引用时使用该宏是一个好主意。

在 3.12 版本发生变更: 该宏参数现在只会被求值一次。 如果该参数具有附带影响，它们将不会再被复制。

void Py_IncRef([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

表示获取一个指向对象 _o_ 的新 [strong reference](../glossary.md#term-strong-reference)。 `Py_XINCREF()` 的函数版本。 它可被用于 Python 的运行时动态嵌入。

void Py_DecRef([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

释放一个指向对象 _o_ 的 [strong reference](../glossary.md#term-strong-reference)。 `Py_XDECREF()` 的函数版本。 它可被用于 Python 的运行时动态嵌入。

Py_SETREF(dst, src)¶  
~~~
    

~~~
该宏可安全地释放一个指向对象 _dst_ 的 [strong reference](../glossary.md#term-strong-reference)，并将 _dst_ 设为 _src_ 。

在 `Py_CLEAR()` 的情况中，这样“直观”的代码可能会是致命的:
~~~
    
    
~~~
Py_DECREF(dst);
dst = src;
~~~

安全的方式是这样:

    
    
~~~
Py_SETREF(dst, src);
~~~

这样使得在释放对旧 _dst_ 值的引用 _之前_ 将 _dst_ 设为 _src_ ，从而让任何因 _dst_ 被去除而触发的代码不再相信 _dst_ 指向一个有效的对象。

在 3.6 版本加入.

在 3.12 版本发生变更: 该宏参数现在只会被求值一次。 如果某个参数具有附带影响，它们将不会再被复制。

Py_XSETREF(dst, src)¶  

    

~~~
