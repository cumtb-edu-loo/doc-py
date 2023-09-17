# Unicode对象和编码解码器¶

## Unicode对象¶

自从python3.3中实现了 [**PEP 393**](https://peps.python.org/pep-0393/) 以来，Unicode对象在内部使用各种表示形式，以便在保持内存效率的同时处理完整范围的Unicode字符。对于所有代码点都低于128、256或65536的字符串，有一些特殊情况；否则，代码点必须低于1114112（这是完整的Unicode范围）。

UTF-8 表示将按需创建并缓存在 Unicode 对象中。

备注

`Py_UNICODE` 表示形式在 Python 3.12 中同被弃用的 API 一起被移除了，查阅 [**PEP 623**](https://peps.python.org/pep-0623/) 以获得更多信息。

### Unicode类型¶

以下是用于Python中Unicode实现的基本Unicode对象类型：

type Py_UCS4¶  

type Py_UCS2¶  

type Py_UCS1¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这些类型是无符号整数类型的类型定义，其宽度足以分别包含 32 位、16 位和 8 位字符。 当需要处理单个 Unicode 字符时，请使用 `Py_UCS4`。

在 3.3 版本加入.

type Py_UNICODE¶  

    

~~~
这是 `wchar_t` 的类型定义，根据平台的不同它可能为 16 位类型或 32 位类型。

在 3.3 版本发生变更: 在以前的版本中，这是16位类型还是32位类型，这取决于您在构建时选择的是“窄”还是“宽”Unicode版本的Python。

从 3.13 版起不建议使用，将在 3.15 版中移除.

type PyASCIIObject¶  

type PyCompactUnicodeObject¶  

type PyUnicodeObject¶  
~~~
    

~~~
这些关于 [`PyObject`](structures.md#c.PyObject "PyObject") 的子类型表示了一个 Python Unicode 对象。 在几乎所有情形下，它们不应该被直接使用，因为所有处理 Unicode 对象的 API 函数都接受并返回 [`PyObject`](structures.md#c.PyObject "PyObject") 类型的指针。

在 3.3 版本加入.

[PyTypeObject](type.md#c.PyTypeObject "PyTypeObject") PyUnicode_Type¶  

    _Part of the[ Stable ABI](stable.md#stable)._

这个 [`PyTypeObject`](type.md#c.PyTypeObject "PyTypeObject") 实例代表 Python Unicode 类型。 它作为 `str` 公开给 Python 代码。

以下API是C宏和静态内联函数，用于快速检查和访问Unicode对象的内部只读数据：

int PyUnicode_Check([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
如果对象*o*是Unicode对象或Unicode子类型的实例，则返回“真"。此函数始终成功。

int PyUnicode_CheckExact([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
如果对象*o*是Unicode对象，但不是子类型的实例，则返回“真”。此函数始终成功。

int PyUnicode_READY([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
返回 `0`。 此 API 仅为向下兼容而保留。

在 3.3 版本加入.

自 3.10 版本弃用: 此 API 从 Python 3.12 起将不做任何事。

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PyUnicode_GET_LENGTH([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
返回Unicode字符串的长度（以代码点为单位）*o*必须是“规范”表达方式中的Unicode对象（未选中）。

在 3.3 版本加入.

Py_UCS1 *PyUnicode_1BYTE_DATA([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

Py_UCS2 *PyUnicode_2BYTE_DATA([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

Py_UCS4 *PyUnicode_4BYTE_DATA([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
返回一个用于直接字符访问的指向转换为 UCS1、UCS2 或 UCS4 整数类型的规范表示的指针。 如果规范表示具有正确的字符大小，则不执行检查；使用 `PyUnicode_KIND()` 选择正确的函数。

在 3.3 版本加入.

PyUnicode_1BYTE_KIND¶  

PyUnicode_2BYTE_KIND¶  

PyUnicode_4BYTE_KIND¶  
~~~
    

~~~
返回 `PyUnicode_KIND()` 宏的值。

在 3.3 版本加入.

在 3.12 版本发生变更: `PyUnicode_WCHAR_KIND` 已被移除。

int PyUnicode_KIND([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
返回一个PyUnicode类常量（见上文），指示此Unicode对象用于存储其数据的每个字符的字节数*o*必须是“规范”表达方式中的Unicode对象（未选中）。

在 3.3 版本加入.

void *PyUnicode_DATA([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
返回指向原始Unicode缓冲区的空指针*o*必须是“规范”表达方式中的Unicode对象（未选中）。

在 3.3 版本加入.

void PyUnicode_WRITE(int kind, void *data, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") index, Py_UCS4 value)¶  
~~~
    

~~~
Write into a canonical representation _data_ (as obtained with `PyUnicode_DATA()`). This function performs no sanity checks, and is intended for usage in loops. The caller should cache the _kind_ value and _data_ pointer as obtained from other calls. _index_ is the index in the string (starts at 0) and _value_ is the new code point value which should be written to that location.

在 3.3 版本加入.

Py_UCS4 PyUnicode_READ(int kind, void *data, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") index)¶  
~~~
    

~~~
Read a code point from a canonical representation _data_ (as obtained with `PyUnicode_DATA()`). No checks or ready calls are performed.

在 3.3 版本加入.

Py_UCS4 PyUnicode_READ_CHAR([PyObject](structures.md#c.PyObject "PyObject") *o, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") index)¶  
~~~
    

~~~
Read a character from a Unicode object _o_ , which must be in the "canonical" representation. This is less efficient than `PyUnicode_READ()` if you do multiple consecutive reads.

在 3.3 版本加入.

Py_UCS4 PyUnicode_MAX_CHAR_VALUE([PyObject](structures.md#c.PyObject "PyObject") *o)¶  
~~~
    

~~~
返回适合于基于*o*创建另一个字符串的最大代码点，该字符串必须在“规范”表达方式中。这始终是一种近似，但比在字符串上迭代更有效。

在 3.3 版本加入.

int PyUnicode_IsIdentifier([PyObject](structures.md#c.PyObject "PyObject") *o)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

如果字符串按照语言定义是合法的标识符则返回 `1`，参见 [标识符和关键字](../reference/lexical_analysis.md#identifiers) 小节。 否则返回 `0`。

在 3.9 版本发生变更: The function does not call [`Py_FatalError()`](sys.md#c.Py_FatalError "Py_FatalError") anymore if the string is not ready.

### Unicode字符属性¶

Unicode提供了许多不同的字符特性。最常需要的宏可以通过这些宏获得，这些宏根据Python配置映射到C函数。

int Py_UNICODE_ISSPACE(Py_UCS4 ch)¶  
~~~
    

~~~
根据 _ch_ 是否为空白字符返回 `1` 或 `0`。

int Py_UNICODE_ISLOWER(Py_UCS4 ch)¶  
~~~
    

~~~
根据 _ch_ 是否为小写字符返回 `1` 或 `0`。

int Py_UNICODE_ISUPPER(Py_UCS4 ch)¶  
~~~
    

~~~
根据 _ch_ 是否为大写字符返回 `1` 或 `0`

int Py_UNICODE_ISTITLE(Py_UCS4 ch)¶  
~~~
    

~~~
根据 _ch_ 是否为标题化的大小写返回 `1` 或 `0`。

int Py_UNICODE_ISLINEBREAK(Py_UCS4 ch)¶  
~~~
    

~~~
根据 _ch_ 是否为换行类字符返回 `1` 或 `0`。

int Py_UNICODE_ISDECIMAL(Py_UCS4 ch)¶  
~~~
    

~~~
根据 _ch_ 是否为十进制数字符返回 `1` 或 `0`。

int Py_UNICODE_ISDIGIT(Py_UCS4 ch)¶  
~~~
    

~~~
根据 _ch_ 是否为数码类字符返回 `1` 或 `0`。

int Py_UNICODE_ISNUMERIC(Py_UCS4 ch)¶  
~~~
    

~~~
根据 _ch_ 是否为数值类字符返回 `1` 或 `0`。

int Py_UNICODE_ISALPHA(Py_UCS4 ch)¶  
~~~
    

~~~
根据 _ch_ 是否为字母类字符返回 `1` 或 `0`。

int Py_UNICODE_ISALNUM(Py_UCS4 ch)¶  
~~~
    

~~~
根据 _ch_ 是否为字母数字类字符返回 `1` 或 `0`。

int Py_UNICODE_ISPRINTABLE(Py_UCS4 ch)¶  
~~~
    

~~~
Return `1` or `0` depending on whether _ch_ is a printable character. Nonprintable characters are those characters defined in the Unicode character database as "Other" or "Separator", excepting the ASCII space (0x20) which is considered printable. (Note that printable characters in this context are those which should not be escaped when [`repr()`](../library/functions.md#repr "repr") is invoked on a string. It has no bearing on the handling of strings written to [`sys.stdout`](../library/sys.md#sys.stdout "sys.stdout") or [`sys.stderr`](../library/sys.md#sys.stderr "sys.stderr").)

These APIs can be used for fast direct character conversions:

Py_UCS4 Py_UNICODE_TOLOWER(Py_UCS4 ch)¶  
~~~
    

~~~
Return the character _ch_ converted to lower case.

Py_UCS4 Py_UNICODE_TOUPPER(Py_UCS4 ch)¶  
~~~
    

~~~
Return the character _ch_ converted to upper case.

Py_UCS4 Py_UNICODE_TOTITLE(Py_UCS4 ch)¶  
~~~
    

~~~
Return the character _ch_ converted to title case.

int Py_UNICODE_TODECIMAL(Py_UCS4 ch)¶  
~~~
    

~~~
Return the character _ch_ converted to a decimal positive integer. Return `-1` if this is not possible. This function does not raise exceptions.

int Py_UNICODE_TODIGIT(Py_UCS4 ch)¶  
~~~
    

~~~
Return the character _ch_ converted to a single digit integer. Return `-1` if this is not possible. This function does not raise exceptions.

double Py_UNICODE_TONUMERIC(Py_UCS4 ch)¶  
~~~
    

~~~
Return the character _ch_ converted to a double. Return `-1.0` if this is not possible. This function does not raise exceptions.

These APIs can be used to work with surrogates:

int Py_UNICODE_IS_SURROGATE(Py_UCS4 ch)¶  
~~~
    

~~~
Check if _ch_ is a surrogate (`0xD800 <= ch <= 0xDFFF`).

int Py_UNICODE_IS_HIGH_SURROGATE(Py_UCS4 ch)¶  
~~~
    

~~~
Check if _ch_ is a high surrogate (`0xD800 <= ch <= 0xDBFF`).

int Py_UNICODE_IS_LOW_SURROGATE(Py_UCS4 ch)¶  
~~~
    

~~~
Check if _ch_ is a low surrogate (`0xDC00 <= ch <= 0xDFFF`).

Py_UCS4 Py_UNICODE_JOIN_SURROGATES(Py_UCS4 high, Py_UCS4 low)¶  
~~~
    

~~~
Join two surrogate characters and return a single `Py_UCS4` value. _high_ and _low_ are respectively the leading and trailing surrogates in a surrogate pair. _high_ must be in the range [0xD800; 0xDBFF] and _low_ must be in the range [0xDC00; 0xDFFF].

### Creating and accessing Unicode strings¶

To create Unicode objects and access their basic sequence properties, use these APIs:

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_New([Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") size, Py_UCS4 maxchar)¶  

    _返回值：新的引用。_

Create a new Unicode object. _maxchar_ should be the true maximum code point to be placed in the string. As an approximation, it can be rounded up to the nearest value in the sequence 127, 255, 65535, 1114111.

This is the recommended way to allocate a new Unicode object. Objects created using this function are not resizable.

在 3.3 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_FromKindAndData(int kind, const void *buffer, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") size)¶  

    _返回值：新的引用。_

Create a new Unicode object with the given _kind_ (possible values are `PyUnicode_1BYTE_KIND` etc., as returned by `PyUnicode_KIND()`). The _buffer_ must point to an array of _size_ units of 1, 2 or 4 bytes per character, as given by the kind.

If necessary, the input _buffer_ is copied and transformed into the canonical representation. For example, if the _buffer_ is a UCS4 string (`PyUnicode_4BYTE_KIND`) and it consists only of codepoints in the UCS1 range, it will be transformed into UCS1 (`PyUnicode_1BYTE_KIND`).

在 3.3 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_FromStringAndSize(const char *u, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") size)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Create a Unicode object from the char buffer _u_. The bytes will be interpreted as being UTF-8 encoded. The buffer is copied into the new object. The return value might be a shared object, i.e. modification of the data is not allowed.

This function raises [`SystemError`](../library/exceptions.md#SystemError "SystemError") when:

  * _size_ < 0,

  * _u_ is `NULL` and _size_ > 0

在 3.12 版本发生变更: _u_ == `NULL` with _size_ > 0 is not allowed anymore.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_FromString(const char *u)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Create a Unicode object from a UTF-8 encoded null-terminated char buffer _u_.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_FromFormat(const char *format, ...)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Take a C `printf()`-style _format_ string and a variable number of arguments, calculate the size of the resulting Python Unicode string and return a string with the values formatted into it. The variable arguments must be C types and must correspond exactly to the format characters in the _format_ ASCII-encoded string.

转换标记符包含两个或更多字符并具有以下组成，且必须遵循此处规定的顺序：

  1. `'%'` 字符，用于标记转换符的起始。

  2. 转换旗标（可选），用于影响某些转换类型的结果。

  3. Minimum field width (optional). If specified as an `'*'` (asterisk), the actual width is given in the next argument, which must be of type int, and the object to convert comes after the minimum field width and optional precision.

  4. Precision (optional), given as a `'.'` (dot) followed by the precision. If specified as `'*'` (an asterisk), the actual precision is given in the next argument, which must be of type int, and the value to convert comes after the precision.

  5. 长度修饰符（可选）。

  6. 转换类型。

转换旗标为：

标志

|

含意  
  
---|---  
  
`0`

|

转换将为数字值填充零字符。  
  
`-`

|

转换值将靠左对齐（如果同时给出则会覆盖 `0` 旗标）。  
  
The length modifiers for following integer conversions (`d`, `i`, `o`, `u`, `x`, or `X`) specify the type of the argument (int by default):

Modifier

|

类型  
  
---|---  
  
`l`

|

long 或 unsigned long  
  
`ll`

|

long long 或 unsigned long long  
  
`j`

|

`intmax_t` 或 `uintmax_t`  
  
`z`

|

`size_t` 或 `ssize_t`  
  
`t`

|

`ptrdiff_t`  
  
针对以下转换 `s` 或 `V` 的长度修饰符 `l` 指明参数的类型为 const wchar_t*。

转换指示符如下:

转换指示符

|

类型

|

注释  
  
---|---|---  
  
`%`

|

_不适用_

|

字面的 `%` 字符。  
  
`d`, `i`

|

由长度修饰符指明

|

有符号 C 整数的十进制表示。  
  
`u`

|

由长度修饰符指明

|

无符号 C 整数的十进制表示。  
  
`o`

|

由长度修饰符指明

|

无符号 C 整数的八进制表示。  
  
`x`

|

由长度修饰符指明

|

无符号 C 整数的十六进制表示（小写）。  
  
`X`

|

由长度修饰符指明

|

无符号 C 整数的十六进制表示（大写）。  
  
`c`

|

int

|

单个字符。  
  
`s`

|

const char* 或 const wchar_t*

|

以 null 为终止符的 C 字符数组。  
  
`p`

|

const void*

|

The hex representation of a C pointer. Mostly equivalent to `printf("%p")` except that it is guaranteed to start with the literal `0x` regardless of what the platform's `printf` yields.  
  
`A`

|

[PyObject](structures.md#c.PyObject "PyObject")*

|

[`ascii()`](../library/functions.md#ascii "ascii") 调用的结果。  
  
`U`

|

[PyObject](structures.md#c.PyObject "PyObject")*

|

一个 Unicode 对象。  
  
`V`

|

[PyObject](structures.md#c.PyObject "PyObject")*, const char* or const wchar_t*

|

A Unicode object (which may be `NULL`) and a null-terminated C character array as a second parameter (which will be used, if the first parameter is `NULL`).  
  
`S`

|

[PyObject](structures.md#c.PyObject "PyObject")*

|

The result of calling [`PyObject_Str()`](object.md#c.PyObject_Str "PyObject_Str").  
  
`R`

|

[PyObject](structures.md#c.PyObject "PyObject")*

|

The result of calling [`PyObject_Repr()`](object.md#c.PyObject_Repr "PyObject_Repr").  
  
备注

The width formatter unit is number of characters rather than bytes. The precision formatter unit is number of bytes or `wchar_t` items (if the length modifier `l` is used) for `"%s"` and `"%V"` (if the `PyObject*` argument is `NULL`), and a number of characters for `"%A"`, `"%U"`, `"%S"`, `"%R"` and `"%V"` (if the `PyObject*` argument is not `NULL`).

备注

Unlike to C `printf()` the `0` flag has effect even when a precision is given for integer conversions (`d`, `i`, `u`, `o`, `x`, or `X`).

在 3.2 版本发生变更: Support for `"%lld"` and `"%llu"` added.

在 3.3 版本发生变更: Support for `"%li"`, `"%lli"` and `"%zi"` added.

在 3.4 版本发生变更: Support width and precision formatter for `"%s"`, `"%A"`, `"%U"`, `"%V"`, `"%S"`, `"%R"` added.

在 3.12 版本发生变更: Support for conversion specifiers `o` and `X`. Support for length modifiers `j` and `t`. Length modifiers are now applied to all integer conversions. Length modifier `l` is now applied to conversion specifiers `s` and `V`. Support for variable width and precision `*`. Support for flag `-`.

An unrecognized format character now sets a [`SystemError`](../library/exceptions.md#SystemError "SystemError"). In previous versions it caused all the rest of the format string to be copied as-is to the result string, and any extra arguments discarded.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_FromFormatV(const char *format, va_list vargs)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Identical to `PyUnicode_FromFormat()` except that it takes exactly two arguments.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_FromObject([PyObject](structures.md#c.PyObject "PyObject") *obj)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Copy an instance of a Unicode subtype to a new true Unicode object if necessary. If _obj_ is already a true Unicode object (not a subtype), return a new [strong reference](../glossary.md#term-strong-reference) to the object.

Objects other than Unicode or its subtypes will cause a [`TypeError`](../library/exceptions.md#TypeError "TypeError").

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_FromEncodedObject([PyObject](structures.md#c.PyObject "PyObject") *obj, const char *encoding, const char *errors)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Decode an encoded object _obj_ to a Unicode object.

[`bytes`](../library/stdtypes.md#bytes "bytes"), [`bytearray`](../library/stdtypes.md#bytearray "bytearray") and other [bytes-like objects](../glossary.md#term-bytes-like-object) are decoded according to the given _encoding_ and using the error handling defined by _errors_. Both can be `NULL` to have the interface use the default values (see Built-in Codecs for details).

All other objects, including Unicode objects, cause a [`TypeError`](../library/exceptions.md#TypeError "TypeError") to be set.

The API returns `NULL` if there was an error. The caller is responsible for decref'ing the returned objects.

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PyUnicode_GetLength([PyObject](structures.md#c.PyObject "PyObject") *unicode)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

Return the length of the Unicode object, in code points.

在 3.3 版本加入.

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PyUnicode_CopyCharacters([PyObject](structures.md#c.PyObject "PyObject") *to, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") to_start, [PyObject](structures.md#c.PyObject "PyObject") *from, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") from_start, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") how_many)¶  
~~~
    

~~~
Copy characters from one Unicode object into another. This function performs character conversion when necessary and falls back to `memcpy()` if possible. Returns `-1` and sets an exception on error, otherwise returns the number of copied characters.

在 3.3 版本加入.

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PyUnicode_Fill([PyObject](structures.md#c.PyObject "PyObject") *unicode, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") start, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") length, Py_UCS4 fill_char)¶  
~~~
    

~~~
Fill a string with a character: write _fill_char_ into `unicode[start:start+length]`.

Fail if _fill_char_ is bigger than the string maximum character, or if the string has more than 1 reference.

Return the number of written character, or return `-1` and raise an exception on error.

在 3.3 版本加入.

int PyUnicode_WriteChar([PyObject](structures.md#c.PyObject "PyObject") *unicode, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") index, Py_UCS4 character)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

Write a character to a string. The string must have been created through `PyUnicode_New()`. Since Unicode strings are supposed to be immutable, the string must not be shared, or have been hashed yet.

This function checks that _unicode_ is a Unicode object, that the index is not out of bounds, and that the object can be modified safely (i.e. that it its reference count is one).

在 3.3 版本加入.

Py_UCS4 PyUnicode_ReadChar([PyObject](structures.md#c.PyObject "PyObject") *unicode, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") index)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

Read a character from a string. This function checks that _unicode_ is a Unicode object and the index is not out of bounds, in contrast to `PyUnicode_READ_CHAR()`, which performs no error checking.

在 3.3 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_Substring([PyObject](structures.md#c.PyObject "PyObject") *str, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") start, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") end)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

Return a substring of _str_ , from character index _start_ (included) to character index _end_ (excluded). Negative indices are not supported.

在 3.3 版本加入.

Py_UCS4 *PyUnicode_AsUCS4([PyObject](structures.md#c.PyObject "PyObject") *u, Py_UCS4 *buffer, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") buflen, int copy_null)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

Copy the string _u_ into a UCS4 buffer, including a null character, if _copy_null_ is set. Returns `NULL` and sets an exception on error (in particular, a [`SystemError`](../library/exceptions.md#SystemError "SystemError") if _buflen_ is smaller than the length of _u_ ). _buffer_ is returned on success.

在 3.3 版本加入.

Py_UCS4 *PyUnicode_AsUCS4Copy([PyObject](structures.md#c.PyObject "PyObject") *u)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

Copy the string _u_ into a new UCS4 buffer that is allocated using [`PyMem_Malloc()`](memory.md#c.PyMem_Malloc "PyMem_Malloc"). If this fails, `NULL` is returned with a [`MemoryError`](../library/exceptions.md#MemoryError "MemoryError") set. The returned buffer always has an extra null code point appended.

在 3.3 版本加入.

### Locale Encoding¶

The current locale encoding can be used to decode text from the operating system.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_DecodeLocaleAndSize(const char *str, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") len, const char *errors)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

Decode a string from UTF-8 on Android and VxWorks, or from the current locale encoding on other platforms. The supported error handlers are `"strict"` and `"surrogateescape"` ([ **PEP 383**](https://peps.python.org/pep-0383/)). The decoder uses `"strict"` error handler if _errors_ is `NULL`. _str_ must end with a null character but cannot contain embedded null characters.

Use `PyUnicode_DecodeFSDefaultAndSize()` to decode a string from the [filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler).

This function ignores the [Python UTF-8 Mode](../library/os.md#utf8-mode).

参见

The [`Py_DecodeLocale()`](sys.md#c.Py_DecodeLocale "Py_DecodeLocale") 函数。

在 3.3 版本加入.

在 3.7 版本发生变更: The function now also uses the current locale encoding for the `surrogateescape` error handler, except on Android. Previously, [`Py_DecodeLocale()`](sys.md#c.Py_DecodeLocale "Py_DecodeLocale") was used for the `surrogateescape`, and the current locale encoding was used for `strict`.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_DecodeLocale(const char *str, const char *errors)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

Similar to `PyUnicode_DecodeLocaleAndSize()`, but compute the string length using `strlen()`.

在 3.3 版本加入.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_EncodeLocale([PyObject](structures.md#c.PyObject "PyObject") *unicode, const char *errors)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

Encode a Unicode object to UTF-8 on Android and VxWorks, or to the current locale encoding on other platforms. The supported error handlers are `"strict"` and `"surrogateescape"` ([ **PEP 383**](https://peps.python.org/pep-0383/)). The encoder uses `"strict"` error handler if _errors_ is `NULL`. Return a [`bytes`](../library/stdtypes.md#bytes "bytes") object. _unicode_ cannot contain embedded null characters.

Use `PyUnicode_EncodeFSDefault()` to encode a string to the [filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler).

This function ignores the [Python UTF-8 Mode](../library/os.md#utf8-mode).

参见

The [`Py_EncodeLocale()`](sys.md#c.Py_EncodeLocale "Py_EncodeLocale") function.

在 3.3 版本加入.

在 3.7 版本发生变更: The function now also uses the current locale encoding for the `surrogateescape` error handler, except on Android. Previously, [`Py_EncodeLocale()`](sys.md#c.Py_EncodeLocale "Py_EncodeLocale") was used for the `surrogateescape`, and the current locale encoding was used for `strict`.

### 文件系统编码格式¶

Functions encoding to and decoding from the [filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler) ([ **PEP 383**](https://peps.python.org/pep-0383/) and [**PEP 529**](https://peps.python.org/pep-0529/)).

To encode file names to [`bytes`](../library/stdtypes.md#bytes "bytes") during argument parsing, the `"O&"` converter should be used, passing `PyUnicode_FSConverter()` as the conversion function:

int PyUnicode_FSConverter([PyObject](structures.md#c.PyObject "PyObject") *obj, void *result)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

ParseTuple converter: encode [`str`](../library/stdtypes.md#str "str") objects -- obtained directly or through the [`os.PathLike`](../library/os.md#os.PathLike "os.PathLike") interface -- to [`bytes`](../library/stdtypes.md#bytes "bytes") using `PyUnicode_EncodeFSDefault()`; [`bytes`](../library/stdtypes.md#bytes "bytes") objects are output as-is. _result_ must be a [PyBytesObject](bytes.md#c.PyBytesObject "PyBytesObject")* which must be released when it is no longer used.

在 3.1 版本加入.

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

To decode file names to [`str`](../library/stdtypes.md#str "str") during argument parsing, the `"O&"` converter should be used, passing `PyUnicode_FSDecoder()` as the conversion function:

int PyUnicode_FSDecoder([PyObject](structures.md#c.PyObject "PyObject") *obj, void *result)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

ParseTuple converter: decode [`bytes`](../library/stdtypes.md#bytes "bytes") objects -- obtained either directly or indirectly through the [`os.PathLike`](../library/os.md#os.PathLike "os.PathLike") interface -- to [`str`](../library/stdtypes.md#str "str") using `PyUnicode_DecodeFSDefaultAndSize()`; [`str`](../library/stdtypes.md#str "str") objects are output as-is. _result_ must be a PyUnicodeObject* which must be released when it is no longer used.

在 3.2 版本加入.

在 3.6 版本发生变更: 接受一个 [path-like object](../glossary.md#term-path-like-object)。

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_DecodeFSDefaultAndSize(const char *s, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") size)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Decode a string from the [filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler).

If you need to decode a string from the current locale encoding, use `PyUnicode_DecodeLocaleAndSize()`.

参见

The [`Py_DecodeLocale()`](sys.md#c.Py_DecodeLocale "Py_DecodeLocale") 函数。

在 3.6 版本发生变更: The [filesystem error handler](../glossary.md#term-filesystem-encoding-and-error-handler) is now used.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_DecodeFSDefault(const char *s)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Decode a null-terminated string from the [filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler).

If the string length is known, use `PyUnicode_DecodeFSDefaultAndSize()`.

在 3.6 版本发生变更: The [filesystem error handler](../glossary.md#term-filesystem-encoding-and-error-handler) is now used.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_EncodeFSDefault([PyObject](structures.md#c.PyObject "PyObject") *unicode)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Encode a Unicode object to the [filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler), and return [`bytes`](../library/stdtypes.md#bytes "bytes"). Note that the resulting [`bytes`](../library/stdtypes.md#bytes "bytes") object can contain null bytes.

If you need to encode a string to the current locale encoding, use `PyUnicode_EncodeLocale()`.

参见

The [`Py_EncodeLocale()`](sys.md#c.Py_EncodeLocale "Py_EncodeLocale") function.

在 3.2 版本加入.

在 3.6 版本发生变更: The [filesystem error handler](../glossary.md#term-filesystem-encoding-and-error-handler) is now used.

### wchar_t Support¶

`wchar_t` support for platforms which support it:

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_FromWideChar(const wchar_t *w, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") size)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Create a Unicode object from the `wchar_t` buffer _w_ of the given _size_. Passing `-1` as the _size_ indicates that the function must itself compute the length, using wcslen. Return `NULL` on failure.

[Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") PyUnicode_AsWideChar([PyObject](structures.md#c.PyObject "PyObject") *unicode, wchar_t *w, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") size)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Copy the Unicode object contents into the `wchar_t` buffer _w_. At most _size_ `wchar_t` characters are copied (excluding a possibly trailing null termination character). Return the number of `wchar_t` characters copied or `-1` in case of an error. Note that the resulting wchar_t* string may or may not be null-terminated. It is the responsibility of the caller to make sure that the wchar_t* string is null-terminated in case this is required by the application. Also, note that the wchar_t* string might contain null characters, which would cause the string to be truncated when used with most C functions.

wchar_t *PyUnicode_AsWideCharString([PyObject](structures.md#c.PyObject "PyObject") *unicode, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") *size)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

Convert the Unicode object to a wide character string. The output string always ends with a null character. If _size_ is not `NULL`, write the number of wide characters (excluding the trailing null termination character) into _*size_. Note that the resulting `wchar_t` string might contain null characters, which would cause the string to be truncated when used with most C functions. If _size_ is `NULL` and the wchar_t* string contains null characters a [`ValueError`](../library/exceptions.md#ValueError "ValueError") is raised.

Returns a buffer allocated by [`PyMem_New`](memory.md#c.PyMem_New "PyMem_New") (use [`PyMem_Free()`](memory.md#c.PyMem_Free "PyMem_Free") to free it) on success. On error, returns `NULL` and _*size_ is undefined. Raises a [`MemoryError`](../library/exceptions.md#MemoryError "MemoryError") if memory allocation is failed.

在 3.2 版本加入.

在 3.7 版本发生变更: Raises a [`ValueError`](../library/exceptions.md#ValueError "ValueError") if _size_ is `NULL` and the wchar_t* string contains null characters.

## Built-in Codecs¶

Python provides a set of built-in codecs which are written in C for speed. All of these codecs are directly usable via the following functions.

Many of the following APIs take two arguments encoding and errors, and they have the same semantics as the ones of the built-in [`str()`](../library/stdtypes.md#str "str") string object constructor.

Setting encoding to `NULL` causes the default encoding to be used which is UTF-8. The file system calls should use `PyUnicode_FSConverter()` for encoding file names. This uses the [filesystem encoding and error handler](../glossary.md#term-filesystem-encoding-and-error-handler) internally.

Error handling is set by errors which may also be set to `NULL` meaning to use the default handling defined for the codec. Default error handling for all built-in codecs is "strict" ([`ValueError`](../library/exceptions.md#ValueError "ValueError") is raised).

The codecs all use a similar interface. Only deviations from the following generic ones are documented for simplicity.

### Generic Codecs¶

These are the generic codec APIs:

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_Decode(const char *s, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") size, const char *encoding, const char *errors)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Create a Unicode object by decoding _size_ bytes of the encoded string _s_. _encoding_ and _errors_ have the same meaning as the parameters of the same name in the [`str()`](../library/stdtypes.md#str "str") built-in function. The codec to be used is looked up using the Python codec registry. Return `NULL` if an exception was raised by the codec.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_AsEncodedString([PyObject](structures.md#c.PyObject "PyObject") *unicode, const char *encoding, const char *errors)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Encode a Unicode object and return the result as Python bytes object. _encoding_ and _errors_ have the same meaning as the parameters of the same name in the Unicode [`encode()`](../library/stdtypes.md#str.encode "str.encode") method. The codec to be used is looked up using the Python codec registry. Return `NULL` if an exception was raised by the codec.

### UTF-8 编解码器¶

以下是 UTF-8 编解码器 API:

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_DecodeUTF8(const char *s, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") size, const char *errors)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Create a Unicode object by decoding _size_ bytes of the UTF-8 encoded string _s_. Return `NULL` if an exception was raised by the codec.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_DecodeUTF8Stateful(const char *s, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") size, const char *errors, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") *consumed)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

If _consumed_ is `NULL`, behave like `PyUnicode_DecodeUTF8()`. If _consumed_ is not `NULL`, trailing incomplete UTF-8 byte sequences will not be treated as an error. Those bytes will not be decoded and the number of bytes that have been decoded will be stored in _consumed_.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_AsUTF8String([PyObject](structures.md#c.PyObject "PyObject") *unicode)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Encode a Unicode object using UTF-8 and return the result as Python bytes object. Error handling is "strict". Return `NULL` if an exception was raised by the codec.

const char *PyUnicode_AsUTF8AndSize([PyObject](structures.md#c.PyObject "PyObject") *unicode, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") *size)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.10._

Return a pointer to the UTF-8 encoding of the Unicode object, and store the size of the encoded representation (in bytes) in _size_. The _size_ argument can be `NULL`; in this case no size will be stored. The returned buffer always has an extra null byte appended (not included in _size_ ), regardless of whether there are any other null code points.

In the case of an error, `NULL` is returned with an exception set and no _size_ is stored.

This caches the UTF-8 representation of the string in the Unicode object, and subsequent calls will return a pointer to the same buffer. The caller is not responsible for deallocating the buffer. The buffer is deallocated and pointers to it become invalid when the Unicode object is garbage collected.

在 3.3 版本加入.

在 3.7 版本发生变更: The return type is now `const char *` rather of `char *`.

在 3.10 版本发生变更: This function is a part of the [limited API](stable.md#limited-c-api).

const char *PyUnicode_AsUTF8([PyObject](structures.md#c.PyObject "PyObject") *unicode)¶  
~~~
    

~~~
As `PyUnicode_AsUTF8AndSize()`, but does not store the size.

在 3.3 版本加入.

在 3.7 版本发生变更: The return type is now `const char *` rather of `char *`.

### UTF-32 Codecs¶

These are the UTF-32 codec APIs:

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_DecodeUTF32(const char *s, [Py_ssize_t](intro.md#c.Py_ssize_t "Py_ssize_t") size, const char *errors, int *byteorder)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Decode _size_ bytes from a UTF-32 encoded buffer string and return the corresponding Unicode object. _errors_ (if non-`NULL`) defines the error handling. It defaults to "strict".

If _byteorder_ is non-`NULL`, the decoder starts decoding using the given byte order:
~~~
    
    
~~~
*byteorder == -1: little endian
*byteorder == 0:  native order
*byteorder == 1:  big endian
~~~

If `*byteorder` is zero, and the first four bytes of the input data are a byte order mark (BOM), the decoder switches to this byte order and the BOM is not copied into the resulting Unicode string. If `*byteorder` is `-1` or `1`, any byte order mark is copied to the output.

After completion, _*byteorder_ is set to the current byte order at the end of input data.

If _byteorder_ is `NULL`, the codec starts in native order mode.

Return `NULL` if an exception was raised by the codec.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_DecodeUTF32Stateful(const char *s, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") size, const char *errors, int *byteorder, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") *consumed)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

If _consumed_ is `NULL`, behave like `PyUnicode_DecodeUTF32()`. If _consumed_ is not `NULL`, `PyUnicode_DecodeUTF32Stateful()` will not treat trailing incomplete UTF-32 byte sequences (such as a number of bytes not divisible by four) as an error. Those bytes will not be decoded and the number of bytes that have been decoded will be stored in _consumed_.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_AsUTF32String([PyObject](structures.md#c.PyObject "PyObject") *unicode)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Return a Python byte string using the UTF-32 encoding in native byte order. The string always starts with a BOM mark. Error handling is "strict". Return `NULL` if an exception was raised by the codec.

### UTF-16 Codecs¶

These are the UTF-16 codec APIs:

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_DecodeUTF16(const char *s, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") size, const char *errors, int *byteorder)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Decode _size_ bytes from a UTF-16 encoded buffer string and return the corresponding Unicode object. _errors_ (if non-`NULL`) defines the error handling. It defaults to "strict".

If _byteorder_ is non-`NULL`, the decoder starts decoding using the given byte order:

    
    
~~~
*byteorder == -1: little endian
*byteorder == 0:  native order
*byteorder == 1:  big endian
~~~

If `*byteorder` is zero, and the first two bytes of the input data are a byte order mark (BOM), the decoder switches to this byte order and the BOM is not copied into the resulting Unicode string. If `*byteorder` is `-1` or `1`, any byte order mark is copied to the output (where it will result in either a `\ufeff` or a `\ufffe` character).

After completion, `*byteorder` is set to the current byte order at the end of input data.

If _byteorder_ is `NULL`, the codec starts in native order mode.

Return `NULL` if an exception was raised by the codec.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_DecodeUTF16Stateful(const char *s, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") size, const char *errors, int *byteorder, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") *consumed)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

If _consumed_ is `NULL`, behave like `PyUnicode_DecodeUTF16()`. If _consumed_ is not `NULL`, `PyUnicode_DecodeUTF16Stateful()` will not treat trailing incomplete UTF-16 byte sequences (such as an odd number of bytes or a split surrogate pair) as an error. Those bytes will not be decoded and the number of bytes that have been decoded will be stored in _consumed_.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_AsUTF16String([PyObject](structures.md#c.PyObject "PyObject") *unicode)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Return a Python byte string using the UTF-16 encoding in native byte order. The string always starts with a BOM mark. Error handling is "strict". Return `NULL` if an exception was raised by the codec.

### UTF-7 Codecs¶

These are the UTF-7 codec APIs:

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_DecodeUTF7(const char *s, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") size, const char *errors)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Create a Unicode object by decoding _size_ bytes of the UTF-7 encoded string _s_. Return `NULL` if an exception was raised by the codec.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_DecodeUTF7Stateful(const char *s, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") size, const char *errors, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") *consumed)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

If _consumed_ is `NULL`, behave like `PyUnicode_DecodeUTF7()`. If _consumed_ is not `NULL`, trailing incomplete UTF-7 base-64 sections will not be treated as an error. Those bytes will not be decoded and the number of bytes that have been decoded will be stored in _consumed_.

### Unicode-Escape 编解码器¶

These are the "Unicode Escape" codec APIs:

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_DecodeUnicodeEscape(const char *s, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") size, const char *errors)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Create a Unicode object by decoding _size_ bytes of the Unicode-Escape encoded string _s_. Return `NULL` if an exception was raised by the codec.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_AsUnicodeEscapeString([PyObject](structures.md#c.PyObject "PyObject") *unicode)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Encode a Unicode object using Unicode-Escape and return the result as a bytes object. Error handling is "strict". Return `NULL` if an exception was raised by the codec.

### Raw-Unicode-Escape Codecs¶

These are the "Raw Unicode Escape" codec APIs:

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_DecodeRawUnicodeEscape(const char *s, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") size, const char *errors)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Create a Unicode object by decoding _size_ bytes of the Raw-Unicode-Escape encoded string _s_. Return `NULL` if an exception was raised by the codec.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_AsRawUnicodeEscapeString([PyObject](structures.md#c.PyObject "PyObject") *unicode)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Encode a Unicode object using Raw-Unicode-Escape and return the result as a bytes object. Error handling is "strict". Return `NULL` if an exception was raised by the codec.

### Latin-1 Codecs¶

These are the Latin-1 codec APIs: Latin-1 corresponds to the first 256 Unicode ordinals and only these are accepted by the codecs during encoding.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_DecodeLatin1(const char *s, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") size, const char *errors)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Create a Unicode object by decoding _size_ bytes of the Latin-1 encoded string _s_. Return `NULL` if an exception was raised by the codec.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_AsLatin1String([PyObject](structures.md#c.PyObject "PyObject") *unicode)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Encode a Unicode object using Latin-1 and return the result as Python bytes object. Error handling is "strict". Return `NULL` if an exception was raised by the codec.

### ASCII Codecs¶

These are the ASCII codec APIs. Only 7-bit ASCII data is accepted. All other codes generate errors.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_DecodeASCII(const char *s, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") size, const char *errors)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Create a Unicode object by decoding _size_ bytes of the ASCII encoded string _s_. Return `NULL` if an exception was raised by the codec.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_AsASCIIString([PyObject](structures.md#c.PyObject "PyObject") *unicode)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Encode a Unicode object using ASCII and return the result as Python bytes object. Error handling is "strict". Return `NULL` if an exception was raised by the codec.

### Character Map Codecs¶

This codec is special in that it can be used to implement many different codecs (and this is in fact what was done to obtain most of the standard codecs included in the `encodings` package). The codec uses mappings to encode and decode characters. The mapping objects provided must support the [`__getitem__()`](3.%20数据模型.md#object.__getitem__ "object.__getitem__") mapping interface; dictionaries and sequences work well.

These are the mapping codec APIs:

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_DecodeCharmap(const char *data, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") size, [PyObject](structures.md#c.PyObject "PyObject") *mapping, const char *errors)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Create a Unicode object by decoding _size_ bytes of the encoded string _s_ using the given _mapping_ object. Return `NULL` if an exception was raised by the codec.

If _mapping_ is `NULL`, Latin-1 decoding will be applied. Else _mapping_ must map bytes ordinals (integers in the range from 0 to 255) to Unicode strings, integers (which are then interpreted as Unicode ordinals) or `None`. Unmapped data bytes -- ones which cause a [`LookupError`](3.标准库/exceptions.md#LookupError "LookupError"), as well as ones which get mapped to `None`, `0xFFFE` or `'\ufffe'`, are treated as undefined mappings and cause an error.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_AsCharmapString([PyObject](structures.md#c.PyObject "PyObject") *unicode, [PyObject](structures.md#c.PyObject "PyObject") *mapping)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Encode a Unicode object using the given _mapping_ object and return the result as a bytes object. Error handling is "strict". Return `NULL` if an exception was raised by the codec.

The _mapping_ object must map Unicode ordinal integers to bytes objects, integers in the range from 0 to 255 or `None`. Unmapped character ordinals (ones which cause a [`LookupError`](3.标准库/exceptions.md#LookupError "LookupError")) as well as mapped to `None` are treated as "undefined mapping" and cause an error.

The following codec API is special in that maps Unicode to Unicode.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_Translate([PyObject](structures.md#c.PyObject "PyObject") *str, [PyObject](structures.md#c.PyObject "PyObject") *table, const char *errors)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Translate a string by applying a character mapping table to it and return the resulting Unicode object. Return `NULL` if an exception was raised by the codec.

The mapping table must map Unicode ordinal integers to Unicode ordinal integers or `None` (causing deletion of the character).

Mapping tables need only provide the [`__getitem__()`](3.%20数据模型.md#object.__getitem__ "object.__getitem__") interface; dictionaries and sequences work well. Unmapped character ordinals (ones which cause a [`LookupError`](3.标准库/exceptions.md#LookupError "LookupError")) are left untouched and are copied as-is.

_errors_ has the usual meaning for codecs. It may be `NULL` which indicates to use the default error handling.

### MBCS codecs for Windows¶

These are the MBCS codec APIs. They are currently only available on Windows and use the Win32 MBCS converters to implement the conversions. Note that MBCS (or DBCS) is a class of encodings, not just one. The target encoding is defined by the user settings on the machine running the codec.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_DecodeMBCS(const char *s, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") size, const char *errors)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) on Windows since version 3.7._

Create a Unicode object by decoding _size_ bytes of the MBCS encoded string _s_. Return `NULL` if an exception was raised by the codec.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_DecodeMBCSStateful(const char *s, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") size, const char *errors, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") *consumed)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) on Windows since version 3.7._

If _consumed_ is `NULL`, behave like `PyUnicode_DecodeMBCS()`. If _consumed_ is not `NULL`, `PyUnicode_DecodeMBCSStateful()` will not decode trailing lead byte and the number of bytes that have been decoded will be stored in _consumed_.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_AsMBCSString([PyObject](structures.md#c.PyObject "PyObject") *unicode)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) on Windows since version 3.7._

Encode a Unicode object using MBCS and return the result as Python bytes object. Error handling is "strict". Return `NULL` if an exception was raised by the codec.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_EncodeCodePage(int code_page, [PyObject](structures.md#c.PyObject "PyObject") *unicode, const char *errors)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable) on Windows since version 3.7._

Encode the Unicode object using the specified code page and return a Python bytes object. Return `NULL` if an exception was raised by the codec. Use `CP_ACP` code page to get the MBCS encoder.

在 3.3 版本加入.

### Methods & Slots¶

## 方法与槽位函数¶

The following APIs are capable of handling Unicode objects and strings on input (we refer to them as strings in the descriptions) and return Unicode objects or integers as appropriate.

They all return `NULL` or `-1` if an exception occurs.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_Concat([PyObject](structures.md#c.PyObject "PyObject") *left, [PyObject](structures.md#c.PyObject "PyObject") *right)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Concat two strings giving a new Unicode string.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_Split([PyObject](structures.md#c.PyObject "PyObject") *s, [PyObject](structures.md#c.PyObject "PyObject") *sep, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") maxsplit)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Split a string giving a list of Unicode strings. If _sep_ is `NULL`, splitting will be done at all whitespace substrings. Otherwise, splits occur at the given separator. At most _maxsplit_ splits will be done. If negative, no limit is set. Separators are not included in the resulting list.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_Splitlines([PyObject](structures.md#c.PyObject "PyObject") *s, int keepend)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Split a Unicode string at line breaks, returning a list of Unicode strings. CRLF is considered to be one line break. If _keepend_ is `0`, the line break characters are not included in the resulting strings.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_Join([PyObject](structures.md#c.PyObject "PyObject") *separator, [PyObject](structures.md#c.PyObject "PyObject") *seq)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Join a sequence of strings using the given _separator_ and return the resulting Unicode string.

[Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") PyUnicode_Tailmatch([PyObject](structures.md#c.PyObject "PyObject") *str, [PyObject](structures.md#c.PyObject "PyObject") *substr, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") start, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") end, int direction)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Return `1` if _substr_ matches `str[start:end]` at the given tail end ( _direction_ == `-1` means to do a prefix match, _direction_ == `1` a suffix match), `0` otherwise. Return `-1` if an error occurred.

[Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") PyUnicode_Find([PyObject](structures.md#c.PyObject "PyObject") *str, [PyObject](structures.md#c.PyObject "PyObject") *substr, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") start, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") end, int direction)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Return the first position of _substr_ in `str[start:end]` using the given _direction_ ( _direction_ == `1` means to do a forward search, _direction_ == `-1` a backward search). The return value is the index of the first match; a value of `-1` indicates that no match was found, and `-2` indicates that an error occurred and an exception has been set.

[Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") PyUnicode_FindChar([PyObject](structures.md#c.PyObject "PyObject") *str, Py_UCS4 ch, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") start, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") end, int direction)¶  

    _Part of the[ Stable ABI](stable.md#stable) since version 3.7._

Return the first position of the character _ch_ in `str[start:end]` using the given _direction_ ( _direction_ == `1` means to do a forward search, _direction_ == `-1` a backward search). The return value is the index of the first match; a value of `-1` indicates that no match was found, and `-2` indicates that an error occurred and an exception has been set.

在 3.3 版本加入.

在 3.7 版本发生变更: _start_ and _end_ are now adjusted to behave like `str[start:end]`.

[Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") PyUnicode_Count([PyObject](structures.md#c.PyObject "PyObject") *str, [PyObject](structures.md#c.PyObject "PyObject") *substr, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") start, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") end)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Return the number of non-overlapping occurrences of _substr_ in `str[start:end]`. Return `-1` if an error occurred.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_Replace([PyObject](structures.md#c.PyObject "PyObject") *str, [PyObject](structures.md#c.PyObject "PyObject") *substr, [PyObject](structures.md#c.PyObject "PyObject") *replstr, [Py_ssize_t](10.C%20API接口/intro.md#c.Py_ssize_t "Py_ssize_t") maxcount)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Replace at most _maxcount_ occurrences of _substr_ in _str_ with _replstr_ and return the resulting Unicode object. _maxcount_ == `-1` means replace all occurrences.

int PyUnicode_Compare([PyObject](structures.md#c.PyObject "PyObject") *left, [PyObject](structures.md#c.PyObject "PyObject") *right)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Compare two strings and return `-1`, `0`, `1` for less than, equal, and greater than, respectively.

This function returns `-1` upon failure, so one should call [`PyErr_Occurred()`](10.C%20API接口/exceptions.md#c.PyErr_Occurred "PyErr_Occurred") to check for errors.

int PyUnicode_CompareWithASCIIString([PyObject](structures.md#c.PyObject "PyObject") *uni, const char *string)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Compare a Unicode object, _uni_ , with _string_ and return `-1`, `0`, `1` for less than, equal, and greater than, respectively. It is best to pass only ASCII-encoded strings, but the function interprets the input string as ISO-8859-1 if it contains non-ASCII characters.

This function does not raise exceptions.

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_RichCompare([PyObject](structures.md#c.PyObject "PyObject") *left, [PyObject](structures.md#c.PyObject "PyObject") *right, int op)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

对两个 Unicode 字符串执行富比较并返回以下值之一:

  * `NULL` in case an exception was raised

  * [`Py_True`](bool.md#c.Py_True "Py_True") or [`Py_False`](bool.md#c.Py_False "Py_False") for successful comparisons

  * [`Py_NotImplemented`](object.md#c.Py_NotImplemented "Py_NotImplemented") in case the type combination is unknown

Possible values for _op_ are [`Py_GT`](typeobj.md#c.Py_GT "Py_GT"), [`Py_GE`](typeobj.md#c.Py_GE "Py_GE"), [`Py_EQ`](typeobj.md#c.Py_EQ "Py_EQ"), [`Py_NE`](typeobj.md#c.Py_NE "Py_NE"), [`Py_LT`](typeobj.md#c.Py_LT "Py_LT"), and [`Py_LE`](typeobj.md#c.Py_LE "Py_LE").

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_Format([PyObject](structures.md#c.PyObject "PyObject") *format, [PyObject](structures.md#c.PyObject "PyObject") *args)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

Return a new string object from _format_ and _args_ ; this is analogous to `format % args`.

int PyUnicode_Contains([PyObject](structures.md#c.PyObject "PyObject") *container, [PyObject](structures.md#c.PyObject "PyObject") *element)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Check whether _element_ is contained in _container_ and return true or false accordingly.

_element_ has to coerce to a one element Unicode string. `-1` is returned if there was an error.

void PyUnicode_InternInPlace([PyObject](structures.md#c.PyObject "PyObject") **string)¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Intern the argument _*string_ in place. The argument must be the address of a pointer variable pointing to a Python Unicode string object. If there is an existing interned string that is the same as _*string_ , it sets _*string_ to it (releasing the reference to the old string object and creating a new [strong reference](../glossary.md#term-strong-reference) to the interned string object), otherwise it leaves _*string_ alone and interns it (creating a new [strong reference](../glossary.md#term-strong-reference)). (Clarification: even though there is a lot of talk about references, think of this function as reference-neutral; you own the object after the call if and only if you owned it before the call.)

[PyObject](structures.md#c.PyObject "PyObject") *PyUnicode_InternFromString(const char *v)¶  

    _返回值：新的引用。_ _Part of the[ Stable ABI](stable.md#stable)._

A combination of `PyUnicode_FromString()` and `PyUnicode_InternInPlace()`, returning either a new Unicode string object that has been interned, or a new ("owned") reference to an earlier interned string object with the same value.

