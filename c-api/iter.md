# 迭代器协议¶

迭代器有两个函数。

int PyIter_Check([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.8._

如果对象Return non-zero if the object _o_ 可以被安全把传给 `PyIter_Next()` 则返回非零值，否则返回 `0`。 此函数总是会成功执行。

int PyAIter_Check([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.10._

如果对象 _o_ 提供了 `AsyncIterator` 协议则返回非零值，否则返回 `0`。 此函数总是会成功执行。

在 3.10 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyIter_Next([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

从迭代器 _o_ 返回下一个值。 对象必须可被 `PyIter_Check()` 确认为迭代器（需要调用方来负责检查）。 如果没有剩余的值，则返回 `NULL` 并且不设置异常。 如果在获取条目时发生了错误，则返回 `NULL` 并且传递异常。

要为迭代器编写一个一个循环，C代码应该看起来像这样

    
    
~~~
PyObject *iterator = PyObject_GetIter(obj);
PyObject *item;

if (iterator == NULL) {
    /* propagate error */
}

while ((item = PyIter_Next(iterator))) {
    /* do something with item */
    ...
    /* release reference when done */
    Py_DECREF(item);
}

Py_DECREF(iterator);

if (PyErr_Occurred()) {
    /* propagate error */
}
else {
    /* continue doing useful work */
}
~~~

type PySendResult¶  

    

~~~
