# 描述符对象¶

“描述符”是描述对象的某些属性的对象。它们存在于类型对象的字典中。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyProperty_Type¶  

    _Part of the[ Stable ABI](stable.md#stable)._

内建描述符类型的类型对象。

[PyObject](structures.md#c.PyObject "PyObject") *PyDescr_NewGetSet([PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") *type, struct [PyGetSetDef](structures.md#c.PyGetSetDef "PyGetSetDef") *getset)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

[PyObject](structures.md#c.PyObject "PyObject") *PyDescr_NewMember([PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") *type, struct [PyMemberDef](structures.md#c.PyMemberDef "PyMemberDef") *meth)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

[PyObject](structures.md#c.PyObject "PyObject") *PyDescr_NewMethod([PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") *type, struct [PyMethodDef](structures.md#c.PyMethodDef "PyMethodDef") *meth)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

[PyObject](structures.md#c.PyObject "PyObject") *PyDescr_NewWrapper([PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") *type, struct wrapperbase *wrapper, void *wrapped)¶  

    _返回值：新的引用。_

[PyObject](structures.md#c.PyObject "PyObject") *PyDescr_NewClassMethod([PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") *type, [PyMethodDef](structures.md#c.PyMethodDef "PyMethodDef") *method)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

int PyDescr_IsData([PyObject](structures.md#c.PyObject "PyObject") *descr)¶  

    

~~~
