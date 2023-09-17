# 数字协议¶

int PyNumber_Check([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

如果对象 _o_ 提供数字的协议，返回真 `1`，否则返回假。这个函数不会调用失败。

在 3.8 版本发生变更: 如果 _o_ 是一个索引整数则返回 `1`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_Add([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _o1_ 、 _o2_ 相加的结果，如果失败，返回 `NULL`。等价于 Python 表达式 `o1 + o2`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_Subtract([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _o1_ 减去 _o2_ 的结果，如果失败，返回 `NULL`。等价于 Python 表达式 `o1 - o2`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_Multiply([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _o1_ 、 _o2_ 相乘的结果，如果失败，返回 `NULL`。等价于 Python 表达式 `o1 * o2`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_MatrixMultiply([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

返回 _o1_ 、 _o2_ 做矩阵乘法的结果，如果失败，返回 `NULL`。等价于 Python 表达式 `o1 @ o2`。

在 3.5 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_FloorDivide([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _o1_ 除以 _o2_ 向下取整的值，失败时返回 `NULL`。 这等价于 Python 表达式 `o1 // o2`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_TrueDivide([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _o1_ 除以 _o2_ 的数学值的合理近似值，或失败时返回 `NULL`。 返回的是 "近似值" 因为二进制浮点数本身就是近似值；不可能以二进制精确表示所有实数。 此函数可以在传入两个整数时返回一个浮点值。 此函数等价于 Python 表达式 `o1 / o2`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_Remainder([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _o1_ 除以 _o2_ 得到的余数，如果失败，返回 `NULL`。等价于 Python 表达式 `o1 % o2`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_Divmod([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

参考内置函数 [`divmod()`](../library/functions.md#divmod "divmod")。如果失败，返回 `NULL`。等价于 Python 表达式 `divmod(o1, o2)`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_Power([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2, [PyObject](structures.md#c.PyObject "PyObject") *o3)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

请参阅内置函数 [`pow()`](../library/functions.md#pow "pow")。 如果失败，返回 `NULL`。 等价于 Python 中的表达式 `pow(o1, o2, o3)`，其中 _o3_ 是可选的。如果要忽略 _o3_ ，则需传入 [`Py_None`](none.md#c.Py_None "Py_None") 作为代替（如果传入 `NULL` 会导致非法内存访问）。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_Negative([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _o_ 的负值，如果失败，返回 `NULL` 。等价于 Python 表达式 `-o`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_Positive([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _o_ ，如果失败，返回 `NULL` 。等价于 Python 表达式 `+o`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_Absolute([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _o_ 的绝对值，如果失败，返回 `NULL`。等价于 Python 表达式 `abs(o)`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_Invert([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _o_ 的按位取反后的结果，如果失败，返回 `NULL`。等价于 Python 表达式 `~o`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_Lshift([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _o1_ 左移 _o2_ 个比特后的结果，如果失败，返回 `NULL`。等价于 Python 表达式 `o1 << o2`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_Rshift([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _o1_ 右移 _o2_ 个比特后的结果，如果失败，返回 `NULL` 。等价于 Python 表达式 `o1 >> o2`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_And([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _o1_ 和 _o2_ “按位与”的结果，如果失败，返回 `NULL` 。等价于 Python 表达式 `o1 & o2`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_Xor([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _o1_ 和 _o2_ “按位异或”的结果，如果失败，返回 `NULL` 。等价于 Python 表达式 `o1 ^ o2`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_Or([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _o1_ 和 _o2_ “按位或”的结果，如果失败，返回 `NULL` 。等价于 Python 表达式 `o1 | o2`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_InPlaceAdd([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _o1_ 、 _o2_ 相加的结果，如果失败，返回 `NULL`。当 _o1_ 支持时，这个运算直接使用它储存结果。 等价于 Python 语句 `o1 += o2`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_InPlaceSubtract([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _o1_ 、 _o2_ 相减的结果，如果失败，返回 `NULL` 。当 _o1_ 支持时，这个运算直接使用它储存结果。 等价于 Python 语句 `o1 -= o2`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_InPlaceMultiply([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _o1_ 、 _o2*相乘的结果，如果失败，返回 ``NULL`` 。当 *o1_ 支持时，这个运算直接使用它储存结果。 等价于 Python 语句 `o1 *= o2`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_InPlaceMatrixMultiply([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

返回 _o1_ 、 _o2_ 做矩阵乘法后的结果，如果失败，返回 `NULL` 。当 _o1_ 支持时，这个运算直接使用它储存结果。 等价于 Python 语句 `o1 @= o2`。

在 3.5 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_InPlaceFloorDivide([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _o1_ 除以 _o2_ 后向下取整的结果，如果失败，返回 `NULL`。当 _o1_ 支持时，这个运算直接使用它储存结果。 等价于 Python 语句 `o1 //= o2`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_InPlaceTrueDivide([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _o1_ 除以 _o2_ 的数学值的合理近似值，或失败时返回 `NULL`。 返回的是 "近似值" 因为二进制浮点数本身就是近似值；不可能以二进制精确表示所有实数。 此函数可以在传入两个整数时返回一个浮点数。 此运算在 _o1_ 支持的时候会 _原地_ 执行。此函数等价于 Python 语句 `o1 /= o2`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_InPlaceRemainder([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _o1_ 除以 _o2_ 得到的余数，如果失败，返回 `NULL`。当 _o1_ 支持时，这个运算直接使用它储存结果。 等价于 Python 语句 `o1 %= o2`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_InPlacePower([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2, [PyObject](structures.md#c.PyObject "PyObject") *o3)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

请参阅内置函数 [`pow()`](../library/functions.md#pow "pow")。 如果失败，返回 `NULL`。当 _o1_ 支持时，这个运算直接使用它储存结果。当 _o3_ 是 [`Py_None`](none.md#c.Py_None "Py_None") 时，等价于 Python 语句 `o1 **= o2`；否则等价于在原来位置储存结果的 `pow(o1, o2, o3)` 。如果要忽略 _o3_ ，则需传入 [`Py_None`](none.md#c.Py_None "Py_None") （传入 `NULL` 会导致非法内存访问）。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_InPlaceLshift([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _o1_ 左移 _o2_ 个比特后的结果，如果失败，返回 `NULL`。当 _o1_ 支持时，这个运算直接使用它储存结果。 等价于 Python 语句 `o1 <<= o2`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_InPlaceRshift([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回 _o1_ 右移 _o2_ 个比特后的结果，如果失败，返回 `NULL`。当 _o1_ 支持时，这个运算直接使用它储存结果。 等价于 Python 语句 `o1 >>= o2`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_InPlaceAnd([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

成功时返回 _o1_ 和 _o2_ "按位与" 的结果，失败时返回 `NULL`。 在 _o1_ 支持的前提下该操作将 _原地_ 执行。 等价与 Python 语句 `o1 &= o2`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_InPlaceXor([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

成功时返回 _o1_ 和 _o2_ "按位异或的结果，失败时返回 `NULL`。 在 _o1_ 支持的前提下该操作将 _原地_ 执行。 等价与 Python 语句 `o1 ^= o2`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_InPlaceOr([PyObject](structures.md#c.PyObject "PyObject") *o1, [PyObject](structures.md#c.PyObject "PyObject") *o2)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

成功时返回 _o1_ 和 _o2_ "按位或" 的结果，失败时返回 `NULL`。 在 _o1_ 支持的前提下该操作将 _原地_ 执行。 等价于 Python 语句 `o1 |= o2`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_Long([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

成功时返回 _o_ 转换为整数对象后的结果，失败时返回 `NULL`。 等价于 Python 表达式 `int(o)`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_Float([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

成功时返回 _o_ 转换为浮点对象后的结果，失败时返回 `NULL`。 等价于 Python 表达式 `float(o)`。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_Index([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

成功时返回 _o_ 转换为 Python int 类型后的结果，失败时返回 `NULL` 并引发 [`TypeError`](../library/exceptions.md#TypeError "TypeError") 异常。

在 3.10 版本发生变更: 结果总是为 [`int`](../library/functions.md#int "int") 类型。 在之前版本中，结果可能为 `int` 的子类的实例。

[PyObject](structures.md#c.PyObject "PyObject") *PyNumber_ToBase([PyObject](structures.md#c.PyObject "PyObject") *n, int base)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

返回整数 _n_ 转换成以 _base_ 为基数的字符串后的结果。这个 _base_ 参数必须是 2，8，10 或者 16 。对于基数 2，8，或 16 ，返回的字符串将分别加上基数标识 `'0b'`, `'0o'`, or `'0x'`。如果 _n_ 不是 Python 中的整数 _int_ 类型，就先通过 `PyNumber_Index()` 将它转换成整数类型。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PyNumber_AsSsize_t([PyObject](structures.md#c.PyObject "PyObject") *o, [PyObject](structures.md#c.PyObject "PyObject") *exc)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

如果 _o_ 可以被解读为一个整数则返回 _o_ 转换成的 [`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t") 值。 如果调用失败，则会引发一个异常并返回 `-1`。

如果 _o_ 可以被转换为 Python 的 int 值但尝试转换为 [`Py_ssize_t`](intro.md#c.Py_ssize_t "Py_ssize_t") 值则会引发 [`OverflowError`](../library/exceptions.md#OverflowError "OverflowError")，则 _exc_ 参数将为所引发的异常类型 (通常为 [`IndexError`](../library/exceptions.md#IndexError "IndexError") 或 [`OverflowError`](../library/exceptions.md#OverflowError "OverflowError"))。 如果 _exc_ 为 `NULL`，则异常会被清除并且值会在为负整数时被裁剪为 `PY_SSIZE_T_MIN` 而在为正整数时被裁剪为 `PY_SSIZE_T_MAX`。

int PyIndex_Check([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.8._

返回 `1` 如果 _o_ 是一个索引整数（将 `nb_index` 槽位填充到 `tp_as_number` 结构体），或者在其他情况下返回 `0`。 此函数总是会成功执行。

