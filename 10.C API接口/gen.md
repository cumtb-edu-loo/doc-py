# 生成器对象¶

生成器对象是Python用来实现生成器迭代器的对象。它们通常通过迭代产生值的函数来创建，而不是显式调用 `PyGen_New()` 或 `PyGen_NewWithQualName()`。

type PyGenObject¶  

    

~~~
用于生成器对象的C结构体。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyGen_Type¶  
~~~
    

~~~
与生成器对象对应的类型对​​象。

int PyGen_Check([PyObject](structures.md#c.PyObject "PyObject") *ob)¶  
~~~
    

~~~
如果 _ob_ 是一个 generator 对象则返回真值； _ob_ 必须不为 `NULL`。 此函数总是会成功执行。

int PyGen_CheckExact([PyObject](structures.md#c.PyObject "PyObject") *ob)¶  
~~~
    

~~~
