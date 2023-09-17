# 实例方法对象¶

实例方法是 [`PyCFunction`](structures.md#c.PyCFunction "PyCFunction") 的包装器，也是将 [`PyCFunction`](structures.md#c.PyCFunction "PyCFunction") 与类对象绑定的新方法。 它取代了以前的调用 `PyMethod_New(func, NULL, class)`。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyInstanceMethod_Type¶  

    

~~~
这个 [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") 实例代表 Python 实例方法类型。 它并不对 Python 程序公开。

int PyInstanceMethod_Check([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
如果 _o_ 是一个实例方法对象 (类型为 `PyInstanceMethod_Type`) 则返回真值。 形参必须不为 `NULL`。 此函数总是会成功执行。

[PyObject](structures.md#c.PyObject "PyObject") *PyInstanceMethod_New([PyObject](structures.md#c.PyObject "PyObject") *func)¶  

    _返回值：新的引用。_

返回一个新的实例方法对象， _func_ 应为任意可调用对象。 _func_ 将在实例方法被调用时作为函数被调用。

[PyObject](structures.md#c.PyObject "PyObject") *PyInstanceMethod_Function([PyObject](structures.md#c.PyObject "PyObject") *im)¶  

    _返回值：借入的引用。_

返回关联到实例方法 _im_ 的函数对象。

[PyObject](structures.md#c.PyObject "PyObject") *PyInstanceMethod_GET_FUNCTION([PyObject](structures.md#c.PyObject "PyObject") *im)¶  

    _返回值：借入的引用。_

宏版本的 `PyInstanceMethod_Function()`，略去了错误检测。

# 方法对象¶

方法是绑定的函数对象。 方法总是会被绑定到一个用户自定义类的实例。 未绑定方法（绑定到一个类的方法）已不再可用。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyMethod_Type¶  
~~~
    

~~~
这个 [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") 实例代表 Python 方法类型。 它作为 `types.MethodType` 向 Python 程序公开。

int PyMethod_Check([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
