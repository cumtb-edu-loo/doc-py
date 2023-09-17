# 协程对象¶

在 3.5 版本加入.

协程对象是使用 `async` 关键字声明的函数返回的。

type PyCoroObject¶  

    

~~~
用于协程对象的C结构体。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyCoro_Type¶  
~~~
    

~~~
与协程对象对应的类型对​​象。

int PyCoro_CheckExact([PyObject](structures.md#c.PyObject "PyObject") *ob)¶  
~~~
    

~~~
