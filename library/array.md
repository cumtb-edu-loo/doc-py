# `array` \--- 高效的数字数组¶

* * *

此模块定义了一种对象类型，可以紧凑地表示由基本值（字符、整数、浮点数）组成的数组。数组是序列类型，其行为与列表非常相似，不同之处在于其中存储的对象类型是受限的，在数组对象创建时用单个字符的 _类型码_ 来指定。已定义的类型码如下：

类型码

|

C 类型

|

Python 类型

|

以字节为单位的最小大小

|

备注  
  
---|---|---|---|---  
  
`'b'`

|

signed char

|

int

|

1

|  
  
`'B'`

|

unsigned char

|

int

|

1

|  
  
`'u'`

|

wchar_t

|

Unicode 字符

|

2

|

(1)  
  
`'w'`

|

Py_UCS4

|

Unicode 字符

|

4

|  
  
`'h'`

|

signed short

|

int

|

2

|  
  
`'H'`

|

unsigned short

|

int

|

2

|  
  
`'i'`

|

signed int

|

int

|

2

|  
  
`'I'`

|

unsigned int

|

int

|

2

|  
  
`'l'`

|

signed long

|

int

|

4

|  
  
`'L'`

|

unsigned long

|

int

|

4

|  
  
`'q'`

|

signed long long

|

int

|

8

|  
  
`'Q'`

|

unsigned long long

|

int

|

8

|  
  
`'f'`

|

float

|

float

|

4

|  
  
`'d'`

|

double

|

float

|

8

|  
  
备注：

  1. 在不同的平台上，它可能为 16 位或 32 位。

在 3.9 版本发生变更: `array('u')` 现在使用 `wchar_t` 作为 C 类型而不是已不建议使用的 `Py_UNICODE`。这个改变不会影响其行为，因为 `Py_UNICODE` 自 Python 3.3 起就是 `wchar_t` 的别名。

从 3.3 版起不建议使用，将在 3.16 版中移除: Please migrate to `'w'` typecode.

值的实际表示是由机器架构（严格说是由 C 实现）决定的。实际大小可以通过 `array.itemsize` 属性来访问。

此模块定义了以下项目：

array.typecodes¶

    

~~~
一个由所有可用的类型码组成的字符串。

此模块定义了以下类型：

_class _array.array( _typecode_ [, _initializer_ ])¶
~~~
    

~~~
一个由 _typecode_ 限制类型的新数组，并通过可选的 _initializer_ 进行初始化。 _initializer_ 必须为一个列表、[bytes-like object](../glossary.md#term-bytes-like-object) 或在合适类型元素上迭代的可迭代对象。

如果是一个列表或字符串，该 initializer 会被传给新数组的 `fromlist()`，`frombytes()` 或 `fromunicode()` 方法（见下）以将初始项添加到数组中。其它将可迭代对象将被传给 `extend()` 方法。

数组对象支持普通的序列操作如索引、切片、拼接和重复等。当使用切片赋值时，所赋的值必须为具有相同类型码的数组对象；所有其他情况都将引发 [`TypeError`](exceptions.md#TypeError "TypeError")。数组对象也实现了缓冲区接口，可以用于所有支持 [字节类对象](../glossary.md#term-bytes-like-object) 的场合。

引发一个 [审计事件](sys.md#auditing) `array.__new__` 附带参数 `typecode` 和 `initializer`。

typecode¶
~~~
    

~~~
在创建数组时指定的类型码字符。

itemsize¶
~~~
    

~~~
在内部表示中，单个数组项的长度，单位为字节。

append( _x_ )¶
~~~
    

~~~
添加一个值为 _x_ 的新项到数组末尾。

buffer_info()¶
~~~
    

~~~
返回一个元组 `(address, length)` 给出存放数组内容的内存缓冲区的当前地址和长度（以元素个数为单位）。以字节为单位的的内存缓冲区大小可通过 `array.buffer_info()[1] * array.itemsize` 来计算。工作在需要内存地址的底层（因此天然地不够安全）的 I/O 接口上时，这有时会有用，例如某些 `ioctl()` 操作。只要数组还存在，并且没有对其应用过改变长度的操作，则返回的数值就是有效的。

备注

当在 C 或 C++ 编写的代码中使用数组对象时（这是有效使用此类信息的唯一方式），使用数组对象所支持的缓冲区接口更为适宜。 此方法仅保留用作向下兼容，应避免在新代码中使用。 缓冲区接口的文档参见 [缓冲协议](../c-api/buffer.md#bufferobjects)。

byteswap()¶
~~~
    

~~~
“字节对调”所有数组项。 此方法只支持大小为 1, 2, 4 或 8 字节的值；对于其他值类型将引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。 它适用于从不同字节序机器所生成的文件中读取数据的情况。

count( _x_ )¶
~~~
    

~~~
返回 _x_ 在数组中的出现次数。

extend( _iterable_ )¶
~~~
    

~~~
将来自 _iterable_ 的项添加到数组末尾。 如果 _iterable_ 是另一个数组，它必须具有 _完全_ 相同的类型码；否则将引发 [`TypeError`](exceptions.md#TypeError "TypeError")。 如果 _iterable_ 不是一个数组，则它必须为可迭代对象并且其元素必须为可添加到数组的适当类型。

frombytes( _s_ )¶
~~~
    

~~~
添加来自字节串的项，将字符节解读为机器值的数组（相当于使用 `fromfile()` 方法从文件中读取数据）。

在 3.2 版本加入: `fromstring()` 被重命名为含义更准确的 `frombytes()`。

fromfile( _f_ , _n_ )¶
~~~
    

~~~
从 [file object](../glossary.md#term-file-object) _f_ 中读取 _n_ 项并将它们添加到数组末尾。 如果可用数据少于 _n_ 项，则会引发 [`EOFError`](exceptions.md#EOFError "EOFError")，但可用的项仍然会被插入数组。

fromlist( _list_ )¶
~~~
    

~~~
添加来自 list 的项。 这等价于 `for x in list: a.append(x)`，区别在于如果发生类型错误，数组将不会被改变。

fromunicode( _s_ )¶
~~~
    

~~~
Extends this array with data from the given unicode string. The array must have type code `'u'` or `'w'`; otherwise a [`ValueError`](exceptions.md#ValueError "ValueError") is raised. Use `array.frombytes(unicodestring.encode(enc))` to append Unicode data to an array of some other type.

index( _x_ [, _start_ [, _stop_ ]])¶
~~~
    

~~~
返回最小的 _i_ 使得 _i_ 为数组中首次出现的 _x_ 的索引号。 指定可选参数 _start_ 和 _stop_ 以便在数组的一个子部分内部搜索 _x_ 。 如果未找到 _x_ 则会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

在 3.10 版本发生变更: 添加了可选的 _start_ 和 _stop_ 形参。

insert( _i_ , _x_ )¶
~~~
    

~~~
将值 _x_ 作为新项插入数组的 _i_ 位置之前。 负值将被视为相对于数组末尾的位置。

pop([ _i_ ])¶
~~~
    

~~~
从数组中移除序号为 _i_ 的项并将其返回。 可选参数值默认为 `-1`，因此默认将移除并返回末尾项。

remove( _x_ )¶
~~~
    

~~~
从数组中移除首次出现的 _x_ 。

reverse()¶
~~~
    

~~~
反转数组中各项的顺序。

tobytes()¶
~~~
    

~~~
将数组转换为一个机器值数组并返回其字节表示（即相当与通过 `tofile()` 方法写入到文件的字节序列。）

在 3.2 版本加入: `tostring()` 被重命名为含义更准确的 `tobytes()`。

tofile( _f_ )¶
~~~
    

~~~
将所有项（作为机器值）写入到 [file object](../glossary.md#term-file-object) _f_ 。

tolist()¶
~~~
    

~~~
将数组转换为包含相同项的普通列表。

tounicode()¶
~~~
    

~~~
Convert the array to a unicode string. The array must have a type `'u'` or `'w'`; otherwise a [`ValueError`](exceptions.md#ValueError "ValueError") is raised. Use `array.tobytes().decode(enc)` to obtain a unicode string from an array of some other type.

When an array object is printed or converted to a string, it is represented as `array(typecode, initializer)`. The _initializer_ is omitted if the array is empty, otherwise it is a string if the _typecode_ is `'u'` or `'w'`, otherwise it is a list of numbers. The string is guaranteed to be able to be converted back to an array with the same type and value using [`eval()`](functions.md#eval "eval"), so long as the `array` class has been imported using `from array import array`. Examples:
~~~
    
    
~~~
array('l')
array('w', 'hello \u2641')
array('l', [1, 2, 3, 4, 5])
array('d', [1.0, 2.0, 3.14])
~~~

参见

模块 [`struct`](struct.md#module-struct "struct: Interpret bytes as packed binary data.")

    

~~~
打包和解包异构二进制数据。

[NumPy](https://numpy.org/)
~~~
    

~~~
