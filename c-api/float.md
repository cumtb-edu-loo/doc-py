# 浮点数对象¶

type PyFloatObject¶  

    

~~~
这个C类型 [`PyObject`](structures.md#c.PyObject "PyObject") 的子类型代表一个Python浮点数对象。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyFloat_Type¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这是个属于C类型 [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") 的代表Python浮点类型的实例。在Python层面的类型 [`float`](../library/functions.md#float "float") 是同一个对象。

int PyFloat_Check([PyObject](structures.md#c.PyObject "PyObject") *p)¶  
~~~
    

~~~
如果它的参数是一个 `PyFloatObject` 或者 `PyFloatObject` 的子类型则返回真值。 此函数总是会成功执行。

int PyFloat_CheckExact([PyObject](structures.md#c.PyObject "PyObject") *p)¶  
~~~
    

~~~
如果它的参数是一个 `PyFloatObject` 但不是 `PyFloatObject` 的子类型则返回真值。 此函数总是会成功执行。

[PyObject](structures.md#c.PyObject "PyObject") *PyFloat_FromString([PyObject](structures.md#c.PyObject "PyObject") *str)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

根据字符串 _str_ 的值创建一个 `PyFloatObject`，失败时返回 `NULL`。

[PyObject](structures.md#c.PyObject "PyObject") *PyFloat_FromDouble(double v)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

根据 _v_ 创建一个 `PyFloatObject` 对象，失败时返回 `NULL`。

double PyFloat_AsDouble([PyObject](structures.md#c.PyObject "PyObject") *pyfloat)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回 _pyfloat_ 的内容的 C double 表示形式。 如果 _pyfloat_ 不是一个 Python 浮点数对象但是具有 [`__float__()`](../reference/datamodel.md#object.__float__ "object.__float__") 方法，则会先调用此方法来将 _pyfloat_ 转换为浮点数。 如果 `__float__()` 未定义则将回退至 [`__index__()`](../reference/datamodel.md#object.__index__ "object.__index__")。 此方法在失败时将返回 `-1.0`，因此开发者应当调用 [`PyErr_Occurred()`](exceptions.md#c.PyErr_Occurred "PyErr_Occurred") 来检测错误。

在 3.8 版本发生变更: 如果可能将使用 [`__index__()`](../reference/datamodel.md#object.__index__ "object.__index__")。

double PyFloat_AS_DOUBLE([PyObject](structures.md#c.PyObject "PyObject") *pyfloat)¶  
~~~
    

~~~
返回 _pyfloat_ 的 C double 表示形式，但不带错误检测。

[PyObject](structures.md#c.PyObject "PyObject") *PyFloat_GetInfo(void)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回一个 structseq 实例，其中包含有关 float 的精度、最小值和最大值的信息。 它是头文件 `float.h` 的一个简单包装。

double PyFloat_GetMax()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回 C double 形式的最大可表示有限浮点数 _DBL_MAX_ 。

double PyFloat_GetMin()¶  

    _Part of the[ Stable ABI](stable.md#stable)._

返回 C double 形式的最小正规化正浮点数 _DBL_MIN_ 。

## 打包与解包函数¶

打包与解包函数提供了独立于平台的高效方式来将浮点数值存储为字节串。 Pack 例程根据 C double 产生字节串，而 Unpack 例程根据这样的字节串产生 C double。 后缀 (2, 4 or 8) 指明字节串中的字节数。

在明显使用 IEEE 754 格式的平台上这些函数是通过拷贝比特位来实现的。 在其他平台上，2 字节格式与 IEEE 754 binary16 半精度格式相同，4 字节格式 (32 位) 与 IEEE 754 binary32 单精度格式相同，而 8 字节格式则与 IEEE 754 双精度格式相同，不过 INF 和 NaN (如果平台存在这两种值) 未得到正确处理，而试图对包含 IEEE INF 或 NaN 的字节串执行解包将会引发一个异常。

在具有比 IEEE 754 所支持的更高精度，或更大动态范围的非 IEEE 平台上，不是所有的值都能被打包；在具有更低精度，或更小动态范围的非 IEEE 平台上，则不是所有的值都能被解包。 在这种情况下发生的事情有一部分将是偶然的（无奈）。

在 3.11 版本加入.

### 打包函数¶

打包例程会写入 2, 4 或 8 个字节，从 _p_ 开始。 _le_ 是一个 int 参数，如果你想要字节串为小端序格式 (指数部分放在后面，位于 `p+1`, `p+3` 或 `p+6` `p+7`) 则其应为非零值，如果你想要大端序格式 (指数部分放在前面，位于 _p_ ) 则其应为零。 `PY_BIG_ENDIAN` 常量可被用于使用本机端序：在大端序处理器上等于 `1`，在小端序处理器上则等于 `0`。

返回值: 如果一切正常则为 `0`，如果出错则为 `-1` (并会设置一个异常，最大可能为 [`OverflowError`](../library/exceptions.md#OverflowError "OverflowError"))。

在非 IEEE 平台上存在两个问题:

  * 如果 _x_ 为 NaN 或无穷大则此函数的行为是未定义的。

  * `-0.0` 和 `+0.0` 将产生相同的字节串。

int PyFloat_Pack2(double x, unsigned char *p, int le)¶  
~~~
    

~~~
将 C double 打包为 IEEE 754 binary16 半精度格式。

int PyFloat_Pack4(double x, unsigned char *p, int le)¶  
~~~
    

~~~
将 C double 打包为 IEEE 754 binary32 单精度格式。

int PyFloat_Pack8(double x, unsigned char *p, int le)¶  
~~~
    

~~~
将 C double 打包为 IEEE 754 binary64 双精度格式。

### 解包函数¶

解包例程会读取 2, 4 或 8 个字节，从 _p_ 开始。 _le_ 是一个 int 参数，如果字节串为小端序格式 (指数部门放在后面，位于 `p+1`, `p+3` 或 `p+6` 和 `p+7`) 则其应为非零值，如果为大端序格式 (指数部分放在前面，位于 _p_ ) 则其应为零。 `PY_BIG_ENDIAN` 常量可被用于使用本机端序：在大端序处理器上等于 `1`，在小端序处理器上则等于 `0`。

返回值: 解包后的 double。 出错时，返回值为 `-1.0` 且 [`PyErr_Occurred()`](exceptions.md#c.PyErr_Occurred "PyErr_Occurred") 为真值 (并且会设置一个异常，最大可能为 [`OverflowError`](../library/exceptions.md#OverflowError "OverflowError"))。

请注意在非 IEEE 平台上此函数将拒绝解包表示 NaN 或无穷大的字节串。

double PyFloat_Unpack2(const unsigned char *p, int le)¶  
~~~
    

~~~
将 IEEE 754 binary16 半精度格式解包为 C double。

double PyFloat_Unpack4(const unsigned char *p, int le)¶  
~~~
    

~~~
将 IEEE 754 binary32 单精度格式解包为 C double。

double PyFloat_Unpack8(const unsigned char *p, int le)¶  
~~~
    

~~~
