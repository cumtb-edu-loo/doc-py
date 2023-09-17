# Cell 对象¶

“Cell”对象用于实现由多个作用域引用的变量。 对于每个这样的变量，一个“Cell”对象为了存储该值而被创建；引用该值的每个堆栈框架的局部变量包含同样使用该变量的对外部作用域的“Cell”引用。 访问该值时，将使用“Cell”中包含的值而不是单元格对象本身。 这种对“Cell”对象的非关联化的引用需要支持生成的字节码；访问时不会自动非关联化这些内容。 “Cell”对象在其他地方可能不太有用。

type PyCellObject¶  

    

~~~
用于Cell对象的C结构体。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyCell_Type¶  
~~~
    

~~~
与 Cell 对象对应的类型对​​象。

int PyCell_Check([PyObject](structures.md#c.PyObject "PyObject") *ob)¶  
~~~
    

~~~
如果 _ob_ 是一个 cell 对象则返回真值； _ob_ 必须不为 `NULL`。 此函数总是会成功执行。

[PyObject](structures.md#c.PyObject "PyObject") *PyCell_New([PyObject](structures.md#c.PyObject "PyObject") *ob)¶  

    _返回值：新的引用。_

创建并返回一个包含值 _ob_ 的新 cell 对象。形参可以为 `NULL`。

[PyObject](structures.md#c.PyObject "PyObject") *PyCell_Get([PyObject](structures.md#c.PyObject "PyObject") *cell)¶  

    _返回值：新的引用。_

返回 cell 对象 _cell_ 的内容。

[PyObject](structures.md#c.PyObject "PyObject") *PyCell_GET([PyObject](structures.md#c.PyObject "PyObject") *cell)¶  

    _返回值：借入的引用。_

返回 cell 对象 _cell_ 的内容，但是不检测 _cell_ 是否非 `NULL` 并且为一个 cell 对象。

int PyCell_Set([PyObject](structures.md#c.PyObject "PyObject") *cell, [PyObject](structures.md#c.PyObject "PyObject") *value)¶  
~~~
    

~~~
将 cell 对象 _cell_ 的内容设为 _value_ 。 这将释放任何对 cell 对象当前内容的引用。 _value_ 可以为 `NULL`。 _cell_ 必须为非 `NULL`；如果它不是一个 cell 对象则将返回 `-1`。 如果设置成功则将返回 `0`。

void PyCell_SET([PyObject](structures.md#c.PyObject "PyObject") *cell, [PyObject](structures.md#c.PyObject "PyObject") *value)¶  
~~~
    

~~~
