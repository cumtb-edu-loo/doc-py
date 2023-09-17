# 反射¶

[PyObject](structures.md#c.PyObject "PyObject") *PyEval_GetBuiltins(void)¶  

    _返回值：借入的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回当前执行帧中内置函数的字典，如果当前没有帧正在执行，则返回线程状态的解释器。

[PyObject](structures.md#c.PyObject "PyObject") *PyEval_GetLocals(void)¶  

    _返回值：借入的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回当前执行帧中局部变量的字典，如果没有当前执行的帧则返回 `NULL`。

[PyObject](structures.md#c.PyObject "PyObject") *PyEval_GetGlobals(void)¶  

    _返回值：借入的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回当前执行帧中全局变量的字典，如果没有当前执行的帧则返回 `NULL`。

[PyFrameObject](frame.md#c.PyFrameObject "PyFrameObject") *PyEval_GetFrame(void)¶  

    _返回值：借入的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回当前线程状态的帧，如果没有当前执行的帧则返回 `NULL`。

另请参阅 [`PyThreadState_GetFrame()`](init.md#c.PyThreadState_GetFrame "PyThreadState_GetFrame")。

const char *PyEval_GetFuncName([PyObject](structures.md#c.PyObject "PyObject") *func)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

如果 _func_ 是函数、类或实例对象，则返回它的名称，否则返回 _func_ 的类型的名称。

const char *PyEval_GetFuncDesc([PyObject](structures.md#c.PyObject "PyObject") *func)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

根据 _func_ 的类型返回描述字符串。 返回值包括函数和方法的 "()", " constructor", " instance" 和 " object"。 与 `PyEval_GetFuncName()` 的结果连接，结果将是 _func_ 的描述。

