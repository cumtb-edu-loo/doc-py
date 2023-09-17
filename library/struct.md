# `struct` \--- 将字节串解读为打包的二进制数据¶

**源代码：** [Lib/struct.py](https://github.com/python/cpython/tree/3.12/Lib/struct.py)

* * *

此模块可在 Python 值和以 Python [`bytes`](stdtypes.md#bytes "bytes") 对象表示的 C 结构体之间进行转换。 通过紧凑 格式字符串 描述预期的 Python 值转换目标/来源。 此模块的函数和对象可被用于两种相当不同的应用程序，与外部源（文件或网络连接）进行数据交换，或者在 Python 应用和 C 层级之间进行数据传输。

备注

当未给出前缀字符时，将默认为原生模式。 它会基于构建 Python 解释器的平台和编译器来打包和解包数据。 打包一个给定 C 结构体的结果包括为所涉及的 C 类型保持正确对齐的填充字节；类似地，当解包时也会将对齐纳入考虑。 相反地，当在外部源之间进行数据通信时，将由程序员负责定义字节顺序和元素之间的填充。 请参阅 字节顺序，大小和对齐方式 了解详情。

某些 `struct` 的函数（以及 `Struct` 的方法）接受一个 _buffer_ 参数。 这将指向实现了 [缓冲协议](../c-api/buffer.md#bufferobjects) 并提供只读或是可读写缓冲的对象。 用于此目的的最常见类型为 [`bytes`](stdtypes.md#bytes "bytes") 和 [`bytearray`](stdtypes.md#bytearray "bytearray")，但许多其他可被视为字节数组的类型也实现了缓冲协议，因此它们无需额外从 [`bytes`](stdtypes.md#bytes "bytes") 对象复制即可被读取或填充。

## 函数和异常¶

此模块定义了下列异常和函数：

_exception _struct.error¶

    

~~~
会在多种场合下被引发的异常；其参数为一个描述错误信息的字符串。

struct.pack( _format_ , _v1_ , _v2_ , _..._ )¶
~~~
    

~~~
返回一个 bytes 对象，其中包含根据格式字符串 _format_ 打包的值 _v1_ , _v2_ , ... 参数个数必须与格式字符串所要求的值完全匹配。

struct.pack_into( _format_ , _buffer_ , _offset_ , _v1_ , _v2_ , _..._ )¶
~~~
    

~~~
根据格式字符串 _format_ 打包 _v1_ , _v2_ , ... 等值并将打包的字节串写入可写缓冲区 _buffer_ 从 _offset_ 开始的位置。 请注意 _offset_ 是必需的参数。

struct.unpack( _format_ , _buffer_ )¶
~~~
    

~~~
根据格式字符串 _format_ 从缓冲区 _buffer_ 解包（假定是由 `pack(format, ...)` 打包）。 结果为一个元组，即使其只包含一个条目。 缓冲区的字节大小必须匹配格式所要求的大小，如 `calcsize()` 所示。

struct.unpack_from( _format_ , _/_ , _buffer_ , _offset =0_)¶
~~~
    

~~~
对 _buffer_ 从位置 _offset_ 开始根据格式字符串 _format_ 进行解包。 结果为一个元组，即使其中只包含一个条目。 缓冲区的字节大小从位置 _offset_ 开始必须至少为 `calcsize()` 显示的格式所要求的大小。

struct.iter_unpack( _format_ , _buffer_ )¶
~~~
    

~~~
根据格式字符串 _format_ 以迭代方式从缓冲区 _buffer_ 中解包。 此函数返回一个迭代器，它将从缓冲区读取大小相等的块直到其所有内容耗尽为止。 缓冲区的字节大小必须是格式所要求的大小的整数倍，如 `calcsize()` 所显示的。

每次迭代将产生一个如格式字符串所指定的元组。

在 3.4 版本加入.

struct.calcsize( _format_ )¶
~~~
    

~~~
返回与格式字符串 _format_ 相对应的结构的大小（亦即 `pack(format, ...)` 所产生的字节串对象的大小）。

## 格式字符串¶

格式字符串描述了打包和解包数据时的数据布局。 它们是使用 格式字符 来构建的，格式字符指明被打包/解包的数据的类型。 此外，还有用来控制 字节顺序、大小和对齐 的特殊字符。 每个格式字符串都是由一个可选的描述数据总体属性的前缀字符和一个或多个描述实际数据值和填充的格式字符组成的。

### 字节顺序，大小和对齐方式¶

在默认情况下，C 类型将以所在机器的原生格式和字节顺序来表示，并在必要时通过跳过填充字节来正确地对齐（根据 C 编译器所使用的规则）。 选择此行为是为了使已打包结构体的字节与对应的 C 结构体的内存布局完全对应。 使用原生字节顺序和填充还是标准格式取决于应用程序本身。

或者，根据下表，格式字符串的第一个字符可用于指示打包数据的字节顺序，大小和对齐方式：

字符

|

字节顺序

|

大小

|

对齐方式  
  
---|---|---|---  
  
`@`

|

按原字节

|

按原字节

|

按原字节  
  
`=`

|

按原字节

|

标准

|

无  
  
`<`

|

小端

|

标准

|

无  
  
`>`

|

大端

|

标准

|

无  
  
`!`

|

网络（=大端）

|

标准

|

无  
  
如果第一个字符不是其中之一，则假定为 `'@'` 。

原生字节顺序可能为大端序或小端序，具体取决于主机系统。 例如，Intel x86, AMD64 (x86-64) 和 Apple M1 是小端序的；IBM z 和许多旧式架构则是大端序的。 请使用 [`sys.byteorder`](sys.md#sys.byteorder "sys.byteorder") 来检查你的系统字节顺序。

本机大小和对齐方式是使用 C 编译器的 `sizeof` 表达式来确定的。 这总是会与本机字节顺序相绑定。

标准大小仅取决于格式字符；请参阅 格式字符 部分中的表格。

请注意 `'@'` 和 `'='` 之间的区别：两个都使用本机字节顺序，但后者的大小和对齐方式是标准化的。

形式 `'!'` 代表网络字节顺序总是使用在 [IETF RFC 1700](https://datatracker.ietf.org/doc/html/rfc1700) 中所定义的大端序。

没有什么方式能指定非本机字节顺序（强制字节对调）；请正确选择使用 `'<'` 或 `'>'`。

注释：

  1. 填充只会在连续结构成员之间自动添加。 填充不会添加到已编码结构的开头和末尾。

  2. 当使用非本机大小和对齐方式即 '<', '>', '=', and '!' 时不会添加任何填充。

  3. 要将结构的末尾对齐到符合特定类型的对齐要求，请以该类型代码加重复计数的零作为格式结束。 参见 例子。

### 格式字符¶

格式字符具有以下含义；C 和 Python 值之间的按其指定类型的转换应当是相当明显的。 ‘标准大小’列是指当使用标准大小时以字节表示的已打包值大小；也就是当格式字符串以 `'<'`, `'>'`, `'!'` 或 `'='` 之一开头的情况。 当使用本机大小时，已打包值的大小取决于具体的平台。

格式

|

C 类型

|

Python 类型

|

标准大小

|

备注  
  
---|---|---|---|---  
  
`x`

|

填充字节

|

无

|  |

(7)  
  
`c`

|

char

|

长度为 1 的字节串

|

1

|  
  
`b`

|

signed char

|

整数

|

1

|

(1), (2)  
  
`B`

|

unsigned char

|

整数

|

1

|

(2)  
  
`?`

|

_Bool

|

bool

|

1

|

(1)  
  
`h`

|

short

|

整数

|

2

|

(2)  
  
`H`

|

unsigned short

|

整数

|

2

|

(2)  
  
`i`

|

int

|

整数

|

4

|

(2)  
  
`I`

|

unsigned int

|

整数

|

4

|

(2)  
  
`l`

|

long

|

整数

|

4

|

(2)  
  
`L`

|

unsigned long

|

整数

|

4

|

(2)  
  
`q`

|

long long

|

整数

|

8

|

(2)  
  
`Q`

|

unsigned long long

|

整数

|

8

|

(2)  
  
`n`

|

`ssize_t`

|

整数

|  |

(3)  
  
`N`

|

`size_t`

|

整数

|  |

(3)  
  
`e`

|

(6)

|

float

|

2

|

(4)  
  
`f`

|

float

|

float

|

4

|

(4)  
  
`d`

|

double

|

float

|

8

|

(4)  
  
`s`

|

char[]

|

字节串

|  |

(9)  
  
`p`

|

char[]

|

字节串

|  |

(8)  
  
`P`

|

void*

|

整数

|  |

(5)  
  
在 3.3 版本发生变更: 增加了对 `'n'` 和 `'N'` 格式的支持

在 3.6 版本发生变更: 添加了对 `'e'` 格式的支持。

注释：

  1. `'?'` 转换码对应于 C99 对应的 _Bool 类型。 如此此类型不可用，则使用 char 来模块。 在标准模式下，它总是以一个字节表示。

  2. 当尝试使用任何整数转换码打包一个非整数时，如果该非整数具有 [`__index__()`](../reference/datamodel.md#object.__index__ "object.__index__") 方法，则会在打包之前将参数转换为一个整数。

在 3.2 版本发生变更: 增加了用于非整数的 [`__index__()`](../reference/datamodel.md#object.__index__ "object.__index__") 方法。

  3. `'n'` 和 `'N'` 转换码仅对本机大小可用（选择为默认或使用 `'@'` 字节顺序字符）。 对于标准大小，你可以使用适合你的应用的任何其他整数格式。

  4. 对于 `'f'`, `'d'` 和 `'e'` 转换码，打包表示形式将使用 IEEE 754 binary32, binary64 或 binary16 格式 (分别对应于 `'f'`, `'d'` 或 `'e'`)，无论平台使用何种浮点格式。

  5. `'P'` 格式字符仅对本机字节顺序可用（选择为默认或使用 `'@'` 字节顺序字符）。 字节顺序字符 `'='` 选择使用基于主机系统的小端或大端排序。 struct 模块不会将其解读为本机排序，因此 `'P'` 格式将不可用。

  6. IEEE 754 binary16 "半精度" 类型是在 [IEEE 754 标准](https://en.wikipedia.org/wiki/IEEE_754-2008_revision) 的 2008 修订版中引入的。 它包含一个符号位，5 个指数位和 11 个精度位（明确存储 10 位），可以完全精确地表示大致范围在 `6.1e-05` 和 `6.5e+04` 之间的数字。 此类型并不被 C 编译器广泛支持：在一台典型的机器上，可以使用 unsigned short 进行存储，但不会被用于数学运算。 请参阅维基百科页面 [half-precision floating-point format](https://en.wikipedia.org/wiki/Half-precision_floating-point_format) 了解详情。

  7. 在打包时，`'x'` 会插入一个 NUL 字节。

  8. `'p'` 格式字符用于编码“Pascal 字符串”，即存储在由计数指定的 _固定长度字节_ 中的可变长度短字符串。 所存储的第一个字节为字符串长度或 255 中的较小值。 之后是字符串对应的字节。 如果传入 `pack()` 的字符串过长（超过计数值减 1），则只有字符串前 `count-1` 个字节会被存储。 如果字符串短于 `count-1`，则会填充空字节以使得恰好使用了 count 个字节。 请注意对于 `unpack()`，`'p'` 格式字符会消耗 `count` 个字节，但返回的字符串永远不会包含超过 255 个字节。

  9. 对于 `'s'` 格式字符，计数会被解读为字节的长度，而不是像其他格式字符那样的重复计数；例如，`'10s'` 表示一个与特定的 Python 字节串互相映射的长度为 10 的字节数据，而 `'10c'` 则表示个 10 个与十个不同的 Python 字节对象互相映射的独立的一字节字符元素 (如 `cccccccccc`)。 (其中的差别的具体演示请参见 例子。) 如果未给出计数，则默认值为 1。 对于打包操作，字节串会被适当地截断或填充空字节以符合尺寸要求。 对于解包操作，结果字节对象总是会恰好具有指定数量的字节。 作为特例，`'0s'` 表示单个空字节串 (而 `'0c'` 表示 0 个字符)。

格式字符之前可以带有整数重复计数。 例如，格式字符串 `'4h'` 的含义与 `'hhhh'` 完全相同。

格式之间的空白字符会被忽略；但是计数及其格式字符中不可有空白字符。

当使用某一种整数格式 (`'b'`, `'B'`, `'h'`, `'H'`, `'i'`, `'I'`, `'l'`, `'L'`, `'q'`, `'Q'`) 打包值 `x` 时，如果 `x` 在该格式的有效范围之外则将引发 `struct.error`。

在 3.1 版本发生变更: 在之前版本中，某些整数格式包装了超范围的值并会引发 [`DeprecationWarning`](exceptions.md#DeprecationWarning "DeprecationWarning") 而不是 `struct.error`。

对于 `'?'` 格式字符，返回值为 [`True`](constants.md#True "True") 或 [`False`](constants.md#False "False")。 在打包时将会使用参数对象的逻辑值。 以本机或标准 bool 类型表示的 0 或 1 将被打包，任何非零值在解包时将为 `True`。

### 例子¶

备注

原生字节顺序的示例 (由 `'@'` 格式前缀或不带任何前缀字符的形式指定) 可能与读者机器所产生的内容不匹配，因为这取决于具体的平台和编译器。

打包和解包三种不同大小的整数，使用大端序:
~~~
    
    
~~~shell
>>> from struct import *
>>> pack(">bhl", 1, 2, 3)
b'\x01\x00\x02\x00\x00\x00\x03'
>>> unpack('>bhl', b'\x01\x00\x02\x00\x00\x00\x03')
(1, 2, 3)
>>> calcsize('>bhl')
7
~~~

尝试打包一个对于所定义字段来说过大的整数:

    
    
~~~shell
>>> pack(">h", 99999)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
struct.error: 'h' format requires -32768 <= number <= 32767
~~~

显示 `'s'` and `'c'` 格式字符之间的差异:

    
    
~~~shell
>>> pack("@ccc", b'1', b'2', b'3')
b'123'
>>> pack("@3s", b'123')
b'123'
~~~

解包的字段可通过将它们赋值给变量或将结果包装为一个具名元组来命名:

    
    
~~~shell
>>> record = b'raymond   \x32\x12\x08\x01\x08'
>>> name, serialnum, school, gradelevel = unpack('<10sHHb', record)

>>> from collections import namedtuple
>>> Student = namedtuple('Student', 'name serialnum school gradelevel')
>>> Student._make(unpack('<10sHHb', record))
Student(name=b'raymond   ', serialnum=4658, school=264, gradelevel=8)
~~~

格式字符的顺序可能会因为填充是隐式的而对在原生模式中的大小产生影响。 在标准模式下，用户要负责插入任何必要的填充。 请注意下面的第一个 `pack` 调用中在已打包的 `'#'` 之后添加了三个 NUL 字节以便在四字节边界上对齐到下面的整数。 在这个例子中，输出是在一台小端序的机器上产生的:

    
    
~~~shell
>>> pack('@ci', b'#', 0x12131415)
b'#\x00\x00\x00\x15\x14\x13\x12'
>>> pack('@ic', 0x12131415, b'#')
b'\x15\x14\x13\x12#'
>>> calcsize('@ci')
8
>>> calcsize('@ic')
5
~~~

以下格式 `'llh0l'` 将会在末尾添加两个填充字节，假定平台的 long 类型按 4 个字节的边界对齐的话:

    
    
~~~shell
>>> pack('@llh0l', 1, 2, 3)
b'\x00\x00\x00\x01\x00\x00\x00\x02\x00\x03\x00\x00'
~~~

参见

模块 [`array`](array.md#module-array "array: Space efficient arrays of uniformly typed numeric values.")

    

~~~
被打包为二进制存储的同质数据。

模块 [`json`](json.md#module-json "json: Encode and decode the JSON format.")
~~~
    

~~~
JSON 编码器和解码器。

模块 [`pickle`](pickle.md#module-pickle "pickle: Convert Python objects to streams of bytes and back.")
~~~
    

~~~
Python 对象序列化。

## 应用¶

`struct` 模块存在两个主要应用，即在一个应用程序或使用相同编译器编译的另一个应用程序中 Python 和 C 代码之间的数据交换 (原生格式)，以及使用商定的数据布局的应用程序之间的数据交换 (标准格式)。 一般来说，针对这两个领域构造的格式字符串是不一样的。

### 原生格式¶

当构造模仿原生布局的格式字符串时，编译器和机器架构会决定字节顺序和填充。 在这种情况下，应当使用 `@` 格式字符来指明原生字节顺序和数据大小。 内部填充字节通常是自动插入的。 为了正确对齐连续的数据块可能会在格式字符串末尾需要一个零重复的格式代码以舍入到正确的字节边界。

请看这两个简单的示例（在 64 位的小端序机器上）:
~~~
    
    
~~~shell
>>> calcsize('@lhl')
24
>>> calcsize('@llh')
18
~~~

在不使用额外填充的情况下不会将数据填充到第二个格式字符串末尾的 8 字节边界上。 零重复的格式代码解决了这个问题:

    
    
~~~shell
>>> calcsize('@llh0l')
24
~~~

`'x'` 格式代码可被用来指定重复，但对于原生格式来说最好是使用 `'0l'` 这样的零重复格式。

在默认情况下，将使用原生字节顺序和对齐，但最好是显式指定并使用 `'@'` 前缀字符。

### 标准格式¶

当与你的进程之外如网络或存储交换数据时，请务必保持精确。 准确地指定字节顺序、大小和对齐。 不要假定它们与特定机器的原生顺序相匹配。 例如，网络字节顺序是大端序的，而许多流行的 CPU 则是小端序的。 通过显式定义，用户将无需关心他们的代码运行所在平台的具体规格。 第一个字符通常应为 `<` 或 `>` (或者 `!`)。 程序员要负责填充操作。 零重复格式字符是无效的。 相反，用户必须在需要时显式地添加 `'x'` 填充字节。 回顾上一节中的示例，我们得到:

    
    
~~~shell
>>> calcsize('<qh6xq')
24
>>> pack('<qh6xq', 1, 2, 3) == pack('@lhl', 1, 2, 3)
True
>>> calcsize('@llh')
18
>>> pack('@llh', 1, 2, 3) == pack('<qqh', 1, 2, 3)
True
>>> calcsize('<qqh6x')
24
>>> calcsize('@llh0l')
24
>>> pack('@llh0l', 1, 2, 3) == pack('<qqh6x', 1, 2, 3)
True
~~~

上述结果（在 64 位机器上执行）不保证在不同的机器上执行时仍能匹配。 例如，以下示例是在 32 位机器上执行的:

    
    
~~~shell
>>> calcsize('<qqh6x')
24
>>> calcsize('@llh0l')
12
>>> pack('@llh0l', 1, 2, 3) == pack('<qqh6x', 1, 2, 3)
False
~~~

## 类¶

`struct` 模块还定义了以下类型：

_class _struct.Struct( _format_ )¶

    

~~~
返回一个新的 Struct 对象，它会根据格式字符串object which writes and reads binary data according to the format string _format_ 来写入和读取二进制数据。 一次性地创建 `Struct` 对象并调用其方法相比调用相同格式的模块层级函数效率更高因为格式字符串只会被编译一次。

备注

传递线路模块层级函数的已编译版最新格式字符串会被缓存，因此只使用少量格式字符串的程序无需担心重用单独的 `Struct` 实例。

已编译的 Struct 对象支持以下方法和属性：

pack( _v1_ , _v2_ , _..._ )¶
~~~
    

~~~
等价于 `pack()` 函数，使用了已编译的格式。 (`len(result)` 将等于 `size`。)

pack_into( _buffer_ , _offset_ , _v1_ , _v2_ , _..._ )¶
~~~
    

~~~
等价于 `pack_into()` 函数，使用了已编译的格式。

unpack( _buffer_ )¶
~~~
    

~~~
等价于 `unpack()` 函数，使用了已编译的格式。 缓冲区的字节大小必须等于 `size`。

unpack_from( _buffer_ , _offset =0_)¶
~~~
    

~~~
等价于 `unpack_from()` 函数，使用了已编译的格式。 缓冲区的字节大小从位置 _offset_ 开始必须至少为 `size`。

iter_unpack( _buffer_ )¶
~~~
    

~~~
等价于 `iter_unpack()` 函数，使用了已编译的格式。 缓冲区的大小必须为 `size` 的整数倍。

在 3.4 版本加入.

format¶
~~~
    

~~~
用于构造此 Struct 对象的格式字符串。

在 3.7 版本发生变更: 格式字符串类型现在是 [`str`](stdtypes.md#str "str") 而不再是 [`bytes`](stdtypes.md#bytes "bytes")。

size¶
~~~
    

~~~
计算出对应于 [`format`](functions.md#format "format") 的结构大小（亦即 `pack()` 方法所产生的字节串对象的大小）。

在 3.13 版本发生变更: The _repr()_ of structs has changed. It is now:
~~~
    
    
~~~shell
>>> Struct('i')
Struct('i')
~~~

