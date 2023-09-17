# 复数对象¶

从C API看，Python的复数对象由两个不同的部分实现：一个是在Python程序使用的Python对象，另外的是一个代表真正复数值的C结构体。API提供了函数共同操作两者。

## 表示复数的C结构体¶

需要注意的是接受这些结构体的作为参数并当做结果返回的函数，都是传递“值”而不是引用指针。此规则适用于整个API。

type Py_complex¶  

    

~~~
这是一个对应Python复数对象的值部分的C结构体。绝大部分处理复数对象的函数都用这类型的结构体作为输入或者输出值，它可近似地定义为：
~~~
    
    
~~~
typedef struct {
   double real;
   double imag;
} Py_complex;
~~~

Py_complex _Py_c_sum(Py_complex left, Py_complex right)¶  

    

~~~
返回两个复数的和，用 C 类型 `Py_complex` 表示。

Py_complex _Py_c_diff(Py_complex left, Py_complex right)¶  
~~~
    

~~~
返回两个复数的差，用 C 类型 `Py_complex` 表示。

Py_complex _Py_c_neg(Py_complex num)¶  
~~~
    

~~~
返回复数 _num_ 的负值，用 C `Py_complex` 表示。

Py_complex _Py_c_prod(Py_complex left, Py_complex right)¶  
~~~
    

~~~
返回两个复数的乘积，用 C 类型 `Py_complex` 表示。

Py_complex _Py_c_quot(Py_complex dividend, Py_complex divisor)¶  
~~~
    

~~~
返回两个复数的商，用 C 类型 `Py_complex` 表示。

如果 _divisor_ 为空，则此方法将返回零并将 `errno` 设为 `EDOM`。

Py_complex _Py_c_pow(Py_complex num, Py_complex exp)¶  
~~~
    

~~~
返回 _num_ 的 _exp_ 次幂，用 C 类型 `Py_complex` 表示。

如果 _num_ 为空且 _exp_ 不是正实数，则此方法将返回零并将 `errno` 设为 `EDOM`。

## 表示复数的Python对象¶

type PyComplexObject¶  
~~~
    

~~~
这个C类型 [`PyObject`](structures.md#c.PyObject "PyObject") 的子类型代表一个 Python 复数对象。

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyComplex_Type¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这是个属于 [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") 的代表Python复数类型的实例。在Python层面的类型 [`complex`](../library/functions.md#complex "complex") 是同一个对象。

int PyComplex_Check([PyObject](structures.md#c.PyObject "PyObject") *p)¶  
~~~
    

~~~
如果它的参数是一个 `PyComplexObject` 或者 `PyComplexObject` 的子类型则返回真值。 此函数总是会成功执行。

int PyComplex_CheckExact([PyObject](structures.md#c.PyObject "PyObject") *p)¶  
~~~
    

~~~
如果它的参数是一个 `PyComplexObject` 但不是 `PyComplexObject` 的子类型则返回真值。 此函数总是会成功执行。

[PyObject](structures.md#c.PyObject "PyObject") *PyComplex_FromCComplex(Py_complex v)¶  

    _返回值：新的引用。_

根据C类型 `Py_complex` 的值生成一个新的Python复数对象。

[PyObject](structures.md#c.PyObject "PyObject") *PyComplex_FromDoubles(double real, double imag)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

根据 _real_ 和 _imag_ 返回一个新的C类型 `PyComplexObject` 对象。

double PyComplex_RealAsDouble([PyObject](structures.md#c.PyObject "PyObject") *op)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

以 C 类型 double 返回 _op_ 的实部。

double PyComplex_ImagAsDouble([PyObject](structures.md#c.PyObject "PyObject") *op)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

以 C 类型 double 返回 _op_ 的虚部。

Py_complex PyComplex_AsCComplex([PyObject](structures.md#c.PyObject "PyObject") *op)¶  
~~~
    

~~~
