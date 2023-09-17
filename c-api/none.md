# `None` 对象¶

请注意，Python/C API 中并没有直接公开 `None` 的 [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject")。 由于 `None` 是一个单例，测试对象标识号（在 C 语言中使用 `==` 运算符）就足够了。 出于同样的原因也没有 `PyNone_Check()` 函数。

[PyObject](structures.md#c.PyObject "PyObject") *Py_None¶  

    

~~~
Python `None` 对象，表示空值。 该对象没有任何方法并且是 [永久性对象](https://peps.python.org/pep-0683/)。

在 3.12 版本发生变更: `Py_None` 是永久性对象。

Py_RETURN_NONE¶  
~~~
    

~~~
