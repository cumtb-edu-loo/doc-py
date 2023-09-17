# 内置函数¶

Python 解释器内置了很多函数和类型，任何时候都能使用。以下按字母顺序给出列表。

内置函数  
  
---  
  
**A**

`abs()`

`aiter()`

`all()`

`anext()`

`any()`

`ascii()`

  

~~~
**B**

`bin()`

`bool()`

`breakpoint()`

`bytearray()`

`bytes()`
~~~
  

~~~
**C**

`callable()`

`chr()`

`classmethod()`

`compile()`

`complex()`
~~~
  

~~~
**D**

`delattr()`

`dict()`

`dir()`

`divmod()`
~~~
  

~~~
|

**E**

`enumerate()`

`eval()`

`exec()`
~~~
  

~~~
**F**

`filter()`

`float()`

`format()`

`frozenset()`
~~~
  

~~~
**G**

`getattr()`

`globals()`
~~~
  

~~~
**H**

`hasattr()`

`hash()`

`help()`

`hex()`
~~~
  

~~~
**I**

`id()`

`input()`

`int()`

`isinstance()`

`issubclass()`

`iter()`

|

**L**

`len()`

`list()`

`locals()`
~~~
  

~~~
**M**

`map()`

`max()`

`memoryview()`

`min()`
~~~
  

~~~
**N**

`next()`
~~~
  

~~~
**O**

`object()`

`oct()`

`open()`

`ord()`
~~~
  

~~~
**P**

`pow()`

`print()`

`property()`
~~~
  

  

  

  

~~~
|

**R**

`range()`

`repr()`

`reversed()`

`round()`
~~~
  

~~~
**S**

`set()`

`setattr()`

`slice()`

`sorted()`

`staticmethod()`

`str()`

`sum()`

`super()`
~~~
  

~~~
**T**

`tuple()`

`type()`
~~~
  

~~~
**V**

`vars()`
~~~
  

~~~
**Z**

`zip()`
~~~
  

~~~
**_**

`__import__()`  
  
abs( _x_ )¶
~~~
    

~~~
返回一个数的绝对值。 参数可以是整数、浮点数或任何实现了 `__abs__()` 的对象。 如果参数是一个复数，则返回它的模。

aiter( _async_iterable_ )¶
~~~
    

~~~
返回 [asynchronous iterable](../glossary.md#term-asynchronous-iterable) 的 [asynchronous iterator](../glossary.md#term-asynchronous-iterator) 。相当于调用 `x.__aiter__()`。

注意：与 `iter()` 不同，`aiter()` 没有两个参数的版本。

在 3.10 版本加入.

all( _iterable_ )¶
~~~
    

~~~
如果 _iterable_ 的所有元素均为真值（或可迭代对象为空）则返回 `True` 。 等价于：
~~~
    
    
~~~python
def all(iterable):
    for element in iterable:
        if not element:
            return False
    return True
~~~

_awaitable _anext( _async_iterator_ )¶

_awaitable _anext( _async_iterator_ , _default_ )

    

~~~
当进入 await 状态时，从给定 [asynchronous iterator](../glossary.md#term-asynchronous-iterator) 返回下一数据项，迭代完毕则返回 _default_ 。

这是内置函数 `next()` 的异步版本，类似于：

调用 _async_iterator_ 的 [`__anext__()`](../reference/datamodel.md#object.__anext__ "object.__anext__") 方法，返回一个 [awaitable](../glossary.md#term-awaitable)。等待返回迭代器的下一个值。若有给出 _default_ ，则在迭代完毕后会返回给出的值，否则会触发 [`StopAsyncIteration`](exceptions.md#StopAsyncIteration "StopAsyncIteration")。

在 3.10 版本加入.

any( _iterable_ )¶
~~~
    

~~~
如果 _iterable_ 的任一元素为真值则返回 `True`。 如果可迭代对象为空，返回 `False`。 等价于:
~~~
    
    
~~~python
def any(iterable):
    for element in iterable:
        if element:
            return True
    return False
~~~

ascii( _object_ )¶

    

~~~
与 `repr()` 类似，返回一个包含对象的可打印表示形式的字符串，但是使用 `\x`、`\u` 和 `\U` 对 `repr()` 返回的字符串中非 ASCII 编码的字符进行转义。生成的字符串和 Python 2 的 `repr()` 返回的结果相似。

bin( _x_ )¶
~~~
    

~~~
将一个整数转换为带前缀 "0b" 的二进制数字符串。 结果是一个合法的 Python 表达式。 如果 _x_ 不是一个 Python `int` 对象，则它必须定义返回一个整数的 [`__index__()`](../reference/datamodel.md#object.__index__ "object.__index__") 方法。 下面是一些例子:
~~~
    
    
~~~shell
>>> bin(3)
'0b11'
>>> bin(-10)
'-0b1010'
~~~

若要控制是否显示前缀“0b”，可以采用以下两种方案：

    
    
~~~shell
>>> format(14, '#b'), format(14, 'b')
('0b1110', '1110')
>>> f'{14:#b}', f'{14:b}'
('0b1110', '1110')
~~~

另见 `format()` 获取更多信息。

_class _bool( _x =False_)¶

    

~~~
返回布尔值，即 `True` 或 `False` 两者之一。 _x_ 将使用标准的 [真值测试过程](stdtypes.md#truth) 来转换。 如果 _x_ 为假值或者被省略，则返回 `False`；在其他情况下，将返回 `True`。 `bool` 类是 `int` 的子类 (参见 [数字类型 --- int, float, complex](stdtypes.md#typesnumeric))。 它不能被继续子类化。 它只有 `False` 和 `True` 两个实例 (参见 [布尔类型 - bool](stdtypes.md#typebool))。

在 3.7 版本发生变更: _x_ 现在只能作为位置参数。

breakpoint( _* args_, _** kws_)¶
~~~
    

~~~
此函数会在调用点进入调试器。具体来说，它调用 [`sys.breakpointhook()`](sys.md#sys.breakpointhook "sys.breakpointhook") ，直接传递 `args` 和 `kws` 。默认情况下， `sys.breakpointhook()` 调用 [`pdb.set_trace()`](pdb.md#pdb.set_trace "pdb.set_trace") 且没有参数。在这种情况下，它纯粹是一个便利函数，因此您不必显式导入 [`pdb`](pdb.md#module-pdb "pdb: The Python debugger for interactive interpreters.") 且键入尽可能少的代码即可进入调试器。但是， [`sys.breakpointhook()`](sys.md#sys.breakpointhook "sys.breakpointhook") 可以设置为其他一些函数并被 `breakpoint()` 自动调用，以允许进入你想用的调试器。如果 [`sys.breakpointhook()`](sys.md#sys.breakpointhook "sys.breakpointhook") 不可访问，这个函数将会引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。

在默认情况下，`breakpoint()` 的行为可使用 [`PYTHONBREAKPOINT`](../using/cmdline.md#envvar-PYTHONBREAKPOINT) 环境变量来改变。 请参阅 [`sys.breakpointhook()`](sys.md#sys.breakpointhook "sys.breakpointhook") 了解详细用法。

请注意这并不保证 [`sys.breakpointhook()`](sys.md#sys.breakpointhook "sys.breakpointhook") 会被替换。

引发一个 [审计事件](sys.md#auditing) `builtins.breakpoint` 并附带参数 `breakpointhook`。

在 3.7 版本加入.

_class _bytearray( _source =b''_)

_class _bytearray( _source_ , _encoding_ )

_class _bytearray( _source_ , _encoding_ , _errors_ )
~~~
    

~~~
返回一个新的 bytes 数组。 [`bytearray`](stdtypes.md#bytearray "bytearray") 类是一个可变序列，包含范围为 0 <= x < 256 的整数。它有可变序列大部分常见的方法，见 [可变序列类型](stdtypes.md#typesseq-mutable) 的描述；同时有 [`bytes`](stdtypes.md#bytes "bytes") 类型的大部分方法，参见 [bytes 和 bytearray 操作](stdtypes.md#bytes-methods)。

可选形参 _source_ 可以用不同的方式来初始化数组：

  * 如果是一个 _string_ ，您必须提供 _encoding_ 参数（ _errors_ 参数仍是可选的）；[`bytearray()`](stdtypes.md#bytearray "bytearray") 会使用 [`str.encode()`](stdtypes.md#str.encode "str.encode") 方法来将 string 转变成 bytes。

  * 如果是一个 _integer_ ，会初始化大小为该数字的数组，并使用 null 字节填充。

  * 如果是一个遵循 [缓冲区接口](../c-api/buffer.md#bufferobjects) 的对象，该对象的只读缓冲区将被用来初始化字节数组。

  * 如果是一个 _iterable_ 可迭代对象，它的元素的范围必须是 `0 <= x < 256` 的整数，它会被用作数组的初始内容。

如果没有实参，则创建大小为 0 的数组。

另见 [二进制序列类型 --- bytes, bytearray, memoryview](stdtypes.md#binaryseq) 和 [bytearray 对象](stdtypes.md#typebytearray)。

_class _bytes( _source =b''_)

_class _bytes( _source_ , _encoding_ )

_class _bytes( _source_ , _encoding_ , _errors_ )
~~~
    

~~~
返回一个新的“bytes”对象，这是一个不可变序列，包含范围为 `0 <= x < 256` 的整数。[`bytes`](stdtypes.md#bytes "bytes") 是 [`bytearray`](stdtypes.md#bytearray "bytearray") 的不可变版本——带有同样不改变序列的方法，支持同样的索引、切片操作。

因此，构造函数的实参和 [`bytearray()`](stdtypes.md#bytearray "bytearray") 相同。

字节对象还可以用字面值创建，参见 [字符串与字节串字面值](../reference/lexical_analysis.md#strings)。

另见 [二进制序列类型 --- bytes, bytearray, memoryview](stdtypes.md#binaryseq)，[bytes 对象](stdtypes.md#typebytes) 和 [bytes 和 bytearray 操作](stdtypes.md#bytes-methods)。

callable( _object_ )¶
~~~
    

~~~
如果参数 _object_ 是可调用的就返回 [`True`](constants.md#True "True")，否则返回 [`False`](constants.md#False "False")。 如果返回 `True`，调用仍可能失败，但如果返回 `False`，则调用 _object_ 将肯定不会成功。 请注意类是可调用的（调用类将返回一个新的实例）；如果实例所属的类有 `__call__()` 则它就是可调用的。

在 3.2 版本加入: 这个函数一开始在 Python 3.0 被移除了，但在 Python 3.2 被重新加入。

chr( _i_ )¶
~~~
    

~~~
返回 Unicode 码位为整数 _i_ 的字符的字符串格式。例如，`chr(97)` 返回字符串 `'a'`，`chr(8364)` 返回字符串 `'€'`。这是 `ord()` 的逆函数。

实参的合法范围是 0 到 1,114,111（16 进制表示是 0x10FFFF）。如果 _i_ 超过这个范围，会触发 [`ValueError`](exceptions.md#ValueError "ValueError") 异常。

@classmethod¶
~~~
    

~~~
把一个方法封装成类方法。

类方法隐含的第一个参数就是类，就像实例方法接收实例作为参数一样。要声明一个类方法，按惯例请使用以下方案：
~~~
    
    
~~~
class C:
    @classmethod
    def f(cls, arg1, arg2): ...
~~~

`@classmethod` 这样的形式称为函数的 [decorator](../glossary.md#term-decorator) \-- 详情参阅 [函数定义](8.%20复合语句.md#function)。

类方法的调用可以在类上进行 (例如 `C.f()`) 也可以在实例上进行 (例如 `C().f()`)。 其所属类以外的类实例会被忽略。 如果类方法在其所属类的派生类上调用，则该派生类对象会被作为隐含的第一个参数被传入。

类方法与 C++ 或 Java 中的静态方法不同。 如果你需要后者，请参阅本节中的 `staticmethod()`。 有关类方法的更多信息，请参阅 [标准类型层级结构](3.%20数据模型.md#types)。

在 3.9 版本发生变更: 类方法现在可以包装其他 [描述器](../glossary.md#term-descriptor) 例如 `property()`。

在 3.10 版本发生变更: 类方法现在继承了方法的属性（ `__module__`、 `__name__`、 `__qualname__`、 `__doc__` 和 `__annotations__`），并拥有一个新的 `__wrapped__` 属性。

在 3.11 版本发生变更: 类方法不再可以包装其他 [descriptors](../glossary.md#term-descriptor) 例如 `property()`。

compile( _source_ , _filename_ , _mode_ , _flags =0_, _dont_inherit =False_, _optimize =-1_)¶

    

~~~
将 _source_ 编译成代码或 AST 对象。代码对象可以被 `exec()` 或 `eval()` 执行。 _source_ 可以是常规的字符串、字节字符串，或者 AST 对象。参见 [`ast`](ast.md#module-ast "ast: Abstract Syntax Tree classes and manipulation.") 模块的文档了解如何使用 AST 对象。

_filename_ 实参需要是代码读取的文件名；如果代码不需要从文件中读取，可以传入一些可辨识的值（经常会使用 `'<string>'`）。

_mode_ 实参指定了编译代码必须用的模式。如果 _source_ 是语句序列，可以是 `'exec'`；如果是单一表达式，可以是 `'eval'`；如果是单个交互式语句，可以是 `'single'`。（在最后一种情况下，如果表达式执行结果不是 `None` 将会被打印出来。）

可选参数 _flags_ 和 _dont_inherit_ 控制应当激活哪个 [编译器选项](ast.md#ast-compiler-flags) 以及应当允许哪个 [future 特性](../reference/simple_stmts.md#future)。 如果两者都未提供 (或都为零) 则代码会应用与调用 `compile()` 的代码相同的旗标来编译。 如果给出了 _flags_ 参数而未给出 _dont_inherit_ (或者为零) 则会在无论如何都将被使用的旗标之外还会额外使用 _flags_ 参数所指定的编译器选项和 future 语句。 如果 _dont_inherit_ 为非零整数，则只使用 _flags_ 参数 -- 外围代码中的旗标 (future 特性和编译器选项) 会被忽略。

编译器选项和 future 语句是由比特位来指明的。 比特位可以通过一起按位 OR 来指明多个选项。 指明特定 future 特性所需的比特位可以在 [`__future__`](__future__.md#module-__future__ "__future__: Future statement definitions") 模块的 `_Feature` 实例的 `compiler_flag` 属性中找到。 [编译器旗标](ast.md#ast-compiler-flags) 可以在 [`ast`](ast.md#module-ast "ast: Abstract Syntax Tree classes and manipulation.") 模块中查找带有 `PyCF_` 前缀的名称。

_optimize_ 实参指定编译器的优化级别；默认值 `-1` 选择与解释器的 [`-O`](../using/cmdline.md#cmdoption-O) 选项相同的优化级别。显式级别为 `0` （没有优化；`__debug__` 为真）、`1` （断言被删除， `__debug__` 为假）或 `2` （文档字符串也被删除）。

如果编译的源码不合法，此函数会触发 [`SyntaxError`](exceptions.md#SyntaxError "SyntaxError") 异常；如果源码包含 null 字节，则会触发 [`ValueError`](exceptions.md#ValueError "ValueError") 异常。

如果您想分析 Python 代码的 AST 表示，请参阅 [`ast.parse()`](ast.md#ast.parse "ast.parse")。

引发一个 [审计事件](sys.md#auditing) `compile` 附带参数 `source`, `filename`。

备注

在 `'single'` 或 `'eval'` 模式编译多行代码字符串时，输入必须以至少一个换行符结尾。 这使 [`code`](code.md#module-code "code: Facilities to implement read-eval-print loops.") 模块更容易检测语句的完整性。

警告

在将足够大或者足够复杂的字符串编译成 AST 对象时，Python 解释器有可能因为 Python AST 编译器的栈深度限制而崩溃。

在 3.2 版本发生变更: Windows 和 Mac 的换行符均可使用。而且在 `'exec'` 模式下的输入不必再以换行符结尾了。另增加了 _optimize_ 参数。

在 3.5 版本发生变更: 之前 _source_ 中包含 null 字节的话会触发 [`TypeError`](exceptions.md#TypeError "TypeError") 异常。

在 3.8 版本加入: `ast.PyCF_ALLOW_TOP_LEVEL_AWAIT` 现在可在旗标中传入以启用对最高层级 `await`, `async for` 和 `async with` 的支持。

_class _complex( _real =0_, _imag =0_)¶

_class _complex( _string_ )
~~~
    

~~~
返回值为 _real_ \+ _imag_ *1j 的复数，或将字符串或数字转换为复数。如果第一个形参是字符串，则它被解释为一个复数，并且函数调用时必须没有第二个形参。第二个形参不能是字符串。每个实参都可以是任意的数值类型（包括复数）。如果省略了 _imag_ ，则默认值为零，构造函数会像 `int` 和 `float` 一样进行数值转换。如果两个实参都省略，则返回 `0j`。

对于一个普通 Python 对象 `x`，`complex(x)` 会委托给 `x.__complex__()`。 如果 [`__complex__()`](../reference/datamodel.md#object.__complex__ "object.__complex__") 未定义则将回退至 [`__float__()`](../reference/datamodel.md#object.__float__ "object.__float__")。 如果 `__float__()` 未定义则将回退至 [`__index__()`](../reference/datamodel.md#object.__index__ "object.__index__")。

备注

当从字符串转换时，字符串在 `+` 或 `-` 的周围必须不能有空格。例如 `complex('1+2j')` 是合法的，但 `complex('1 + 2j')` 会触发 [`ValueError`](exceptions.md#ValueError "ValueError") 异常。

[数字类型 --- int, float, complex](stdtypes.md#typesnumeric) 描述了复数类型。

在 3.6 版本发生变更: 您可以使用下划线将代码文字中的数字进行分组。

在 3.8 版本发生变更: 如果 [`__complex__()`](../reference/datamodel.md#object.__complex__ "object.__complex__") 和 [`__float__()`](../reference/datamodel.md#object.__float__ "object.__float__") 均未定义则回退至 [`__index__()`](../reference/datamodel.md#object.__index__ "object.__index__")。

delattr( _object_ , _name_ )¶
~~~
    

~~~
这是 `setattr()` 的相关函数。 其参数是一个对象和一个字符串。 其中字符串必须是对象的某个属性的名称。 该函数会删除指定的属性，如果对象允许这样做的话。 例如，`delattr(x, 'foobar')` 等价于 `del x.foobar`。 _name_ 不要求必须是 Python 标识符 (参见 `setattr()`)。

_class _dict( _** kwarg_)

_class _dict( _mapping_ , _** kwarg_)

_class _dict( _iterable_ , _** kwarg_)
~~~
    

~~~
创建一个新的字典。[`dict`](stdtypes.md#dict "dict") 对象是一个字典类。参见 [`dict`](stdtypes.md#dict "dict") 和 [映射类型 --- dict](stdtypes.md#typesmapping) 了解这个类。

其他容器类型，请参见内置的 [`list`](stdtypes.md#list "list")、[`set`](stdtypes.md#set "set") 和 [`tuple`](stdtypes.md#tuple "tuple") 类，以及 [`collections`](collections.md#module-collections "collections: Container datatypes") 模块。

dir()¶

dir( _object_ )
~~~
    

~~~
如果没有实参，则返回当前本地作用域中的名称列表。如果有实参，它会尝试返回该对象的有效属性列表。

如果对象有一个名为 `__dir__()` 的方法，那么该方法将被调用，并且必须返回一个属性列表。这允许实现自定义 `__getattr__()` 或 `__getattribute__()` 函数的对象能够自定义 `dir()` 来报告它们的属性。

如果对象未提供 `__dir__()` 方法，该函数会尽量从对象的 [`__dict__`](stdtypes.md#object.__dict__ "object.__dict__") 属性和其类型对象中收集信息。得到的列表不一定是完整，如果对象带有自定义 `__getattr__()` 方法时，结果可能不准确。

默认的 `dir()` 机制对不同类型的对象行为不同，它会试图返回最相关而不是最全的信息：

  * 如果对象是模块对象，则列表包含模块的属性名称。

  * 如果对象是类型或类对象，则列表包含它们的属性名称，并且递归查找所有基类的属性。

  * 否则，列表包含对象的属性名称，它的类属性名称，并且递归查找它的类的所有基类的属性。

返回的列表按字母表排序。例如：
~~~
    
    
~~~shell
>>> import struct
>>> dir()   # show the names in the module namespace  
['__builtins__', '__name__', 'struct']
>>> dir(struct)   # show the names in the struct module 
['Struct', '__all__', '__builtins__', '__cached__', '__doc__', '__file__',
 '__initializing__', '__loader__', '__name__', '__package__',
 '_clearcache', 'calcsize', 'error', 'pack', 'pack_into',
 'unpack', 'unpack_from']
>>> class Shape:
...     def __dir__(self):
...         return ['area', 'perimeter', 'location']
...
>>> s = Shape()
>>> dir(s)
['area', 'location', 'perimeter']
~~~

备注

因为 `dir()` 主要是为了便于在交互式时使用，所以它会试图返回人们感兴趣的名字集合，而不是试图保证结果的严格性或一致性，它具体的行为也可能在不同版本之间改变。例如，当实参是一个类时，metaclass 的属性不包含在结果列表中。

divmod( _a_ , _b_ )¶

    

~~~
以两个（非复数）数字为参数，在作整数除法时，返回商和余数。若操作数为混合类型，则适用二进制算术运算符的规则。对于整数而言，结果与 `(a // b, a % b)` 相同。对于浮点数则结果为 `(q, a % b)`，其中 _q_ 通常为 `math.floor(a / b)`，但可能比它小 1。在任何情况下， `q * b + a % b` 都非常接近 _a_ ，如果 `a % b` 非零，则结果符号与 _b_ 相同，并且 `0 <= abs(a % b) < abs(b)`。

enumerate( _iterable_ , _start =0_)¶
~~~
    

~~~
返回一个枚举对象。 _iterable_ 必须是一个序列，或 [iterator](../glossary.md#term-iterator)，或其他支持迭代的对象。 `enumerate()` 返回的迭代器的 [`__next__()`](stdtypes.md#iterator.__next__ "iterator.__next__") 方法返回一个元组，里面包含一个计数值（从 _start_ 开始，默认为 0）和通过迭代 _iterable_ 获得的值。
~~~
    
    
~~~shell
>>> seasons = ['Spring', 'Summer', 'Fall', 'Winter']
>>> list(enumerate(seasons))
[(0, 'Spring'), (1, 'Summer'), (2, 'Fall'), (3, 'Winter')]
>>> list(enumerate(seasons, start=1))
[(1, 'Spring'), (2, 'Summer'), (3, 'Fall'), (4, 'Winter')]
~~~

等价于:

    
    
~~~python
def enumerate(iterable, start=0):
    n = start
    for elem in iterable:
        yield n, elem
        n += 1
~~~

eval( _expression_ , _globals =None_, _locals =None_)¶

    

~~~
实参是一个字符串，以及可选的 globals 和 locals。 _globals_ 实参必须是一个字典。 _locals_ 可以是任何映射对象。

表达式解析参数 _expression_ 并作为 Python 表达式进行求值（从技术上说是一个条件列表），采用 _globals_ 和 _locals_ 字典作为全局和局部命名空间。 如果存在 _globals_ 字典，并且不包含 `__builtins__` 键的值，则在解析 _expression_ 之前会插入以该字符串为键以对内置模块 [`builtins`](builtins.md#module-builtins "builtins: The module that provides the built-in namespace.") 的字典的引用为值的项。 这样就可以在将 _globals_ 传给 `eval()` 之前通过向其传入你自己的 `__builtins__` 字典来控制可供被执行代码可以使用哪些内置模块。 如果 _locals_ 字典被省略则它默认为 _globals_ 字典。 如果两个字典都被省略，则将使用调用 `eval()` 的环境中的 _globals_ 和 _locals_ 来执行该表达式。 注意， _eval()_ 无法访问闭包环境中的 [嵌套作用域](../glossary.md#term-nested-scope) (非局部变量)。

返回值就是表达式的求值结果。 语法错误将作为异常被报告。 例如：
~~~
    
    
~~~shell
>>> x = 1
>>> eval('x+1')
2
~~~

该函数还可用于执行任意代码对象（比如由 `compile()` 创建的对象）。 这时传入的是代码对象，而非一个字符串了。如果代码对象已用参数为 _mode_ 的 `'exec'` 进行了编译，那么 `eval()` 的返回值将为 `None`。

提示： `exec()` 函数支持语句的动态执行。 `globals()` 和 `locals()` 函数分别返回当前的全局和本地字典，可供传给 `eval()` 或 `exec()` 使用。

如果给出的源数据是个字符串，那么其前后的空格和制表符将被剔除。

另外可以参阅 [`ast.literal_eval()`](ast.md#ast.literal_eval "ast.literal_eval")，该函数可以安全执行仅包含文字的表达式字符串。

引发一个 [审计事件](3.标准库/sys.md#auditing) `exec` 附带参数 `code_object`。

exec( _object_ , _globals =None_, _locals =None_, _/_ , _*_ , _closure =None_)¶

    

~~~
这个函数支持动态执行 Python 代码。 _object_ 必须是字符串或者代码对象。 如果是字符串，那么该字符串将被解析为一系列 Python 语句并执行（除非发生语法错误）。 [1] 如果是代码对象，它将被直接执行。 在任何情况下，被执行的代码都应当是有效的文件输入（见参考手册中的 [文件输入](../reference/toplevel_components.md#file-input) 一节）。 请注意即使在传递给 `exec()` 函数的代码的上下文中，[`nonlocal`](../reference/simple_stmts.md#nonlocal), [`yield`](../reference/simple_stmts.md#yield) 和 [`return`](../reference/simple_stmts.md#return) 语句也不能在函数定义以外使用。 该函数的返回值是 `None`。

无论在什么情况下，如果省略了可选部分，代码将运行于当前作用域中。如果只提供了 _globals_ ，则必须为字典对象（而不能是字典的子类），同时用于存放全局变量和局部变量。如果提供了 _globals_ 和 _locals_ ，则将分别用于全局变量和局部变量。 _locals_ 可以是任意字典映射对象。请记住，在模块级别，globals 和 locals 是同一个字典。如果 exec 获得两个独立的对象作为 _globals_ 和 _locals_ ，代码执行起来就像嵌入到某个类定义中一样。

如果 _globals_ 字典不包含 `__builtins__` 键值，则将为该键插入对内建 [`builtins`](builtins.md#module-builtins "builtins: The module that provides the built-in namespace.") 模块字典的引用。因此，在将执行的代码传递给 `exec()` 之前，可以通过将自己的 `__builtins__` 字典插入到 _globals_ 中来控制可以使用哪些内置代码。

_closure_ 参数指定一个闭包 -- 即由 cellvar 组成的元组。 它仅在 _object_ 是一个包含自由变量的代码对象时才可用。 该元组的长度必须与代码对象所引用的自由变量的数量完全一致。

引发一个 [审计事件](sys.md#auditing) `exec` 附带参数 `code_object`。

备注

内置 `globals()` 和 `locals()` 函数各自返回当前的全局和本地字典，因此可以将它们传递给 `exec()` 的第二个和第三个实参。

备注

默认情况下， _locals_ 的行为如下面 `locals()` 函数描述的一样：不要试图改变默认的 _locals_ 字典。 如果您需要在 `exec()` 函数返回时查看代码对 _locals_ 的影响，请明确地传递 _locals_ 字典。

在 3.11 版本发生变更: 添加了 _closure_ 参数。

filter( _function_ , _iterable_ )¶
~~~
    

~~~
使用 _iterable_ 中 _function_ 返回真值的元素构造一个迭代器。 _iterable_ 可以是一个序列，一个支持迭代的容器或者一个迭代器。 如果 _function_ 为 `None`，则会使用标识号函数，也就是说， _iterable_ 中所有具有假值的元素都将被移除。

请注意， `filter(function, iterable)` 相当于一个生成器表达式，当 function 不是 `None` 的时候为 `(item for item in iterable if function(item))`；function 是 `None` 的时候为 `(item for item in iterable if item)` 。

请参阅 [`itertools.filterfalse()`](itertools.md#itertools.filterfalse "itertools.filterfalse") 来了解返回 _iterable_ 中 _function_ 返回假值的元素的补充函数。

_class _float( _x =0.0_)¶
~~~
    

~~~
返回从数字或字符串 _x_ 生成的浮点数。

如果该参数是一个字符串，则它应当包含一个十进制数字，前面可以选择带一个符号，也可以选择嵌入空格。 可选的符号可以为 `'+'` 或 `'-'`；`'+'` 符号对所产生的值没有影响。 该参数还可以是一个代表 NaN (not-a-number) 或正负无穷大的字符串。 更确切地说，在去除前导和尾随的空格之后，输入必须为符合以下语法的 `floatvalue` 产生规则:
~~~
    
    
~~~
**sign** ::=  "+" | "-"
**infinity** ::=  "Infinity" | "inf"
**nan** ::=  "nan"
**digitpart** ::=  digit (["_"] digit)*
**number** ::=  [digitpart] "." digitpart | digitpart ["."]
**exponent** ::=  ("e" | "E") ["+" | "-"] digitpart
**floatnumber** ::=  number [exponent]
**floatvalue** ::=  [sign] (floatnumber | infinity | nan)
~~~

在这里 `digit` 是一个 Unicode 十进制数码 (Unicode 主分类为 `Nd` 的字符)。 对大小写不敏感，因此，举例来说，"inf", "Inf", "INFINITY" 和 "iNfINity" 都是正无穷大的可接受拼写。

另一方面，如果实参是整数或浮点数，则返回具有相同值（在 Python 浮点精度范围内）的浮点数。如果实参在 Python 浮点精度范围外，则会触发 [`OverflowError`](3.标准库/exceptions.md#OverflowError "OverflowError")。

对于一个普通 Python 对象 `x`，`float(x)` 会委托给 `x.__float__()`。 如果 [`__float__()`](3.%20数据模型.md#object.__float__ "object.__float__") 未定义则将回退至 [`__index__()`](3.%20数据模型.md#object.__index__ "object.__index__")。

如果没有实参，则返回 `0.0` 。

示例:

    
    
~~~shell
>>> float('+1.23')
1.23
>>> float('   -12345\n')
-12345.0
>>> float('1e-003')
0.001
>>> float('+1E6')
1000000.0
>>> float('-Infinity')
-inf
~~~

[数字类型 --- int, float, complex](stdtypes.md#typesnumeric) 描述了浮点类型。

在 3.6 版本发生变更: 您可以使用下划线将代码文字中的数字进行分组。

在 3.7 版本发生变更: _x_ 现在只能作为位置参数。

在 3.8 版本发生变更: 如果 [`__float__()`](3.%20数据模型.md#object.__float__ "object.__float__") 未定义则回退至 [`__index__()`](3.%20数据模型.md#object.__index__ "object.__index__")。

format( _value_ , _format_spec =''_)¶

    

~~~
将 _value_ 转换为“格式化后”的形式，格式由 _format_spec_ 进行控制。 _format_spec_ 的解释方式取决于 _value_ 参数的类型；但大多数内置类型使用一种标准的格式化语法： [格式规格迷你语言](string.md#formatspec)。

默认的 _format_spec_ 是一个空字符串，它通常给出与调用 [`str(value)`](stdtypes.md#str "str") 相同的结果。

调用 `format(value, format_spec)` 会转换成 `type(value).__format__(value, format_spec)` ，所以实例字典中的 `__format__()` 方法将不会调用。如果方法搜索回退到 `object` 类但 _format_spec_ 不为空，或者如果 _format_spec_ 或返回值不是字符串，则会触发 [`TypeError`](exceptions.md#TypeError "TypeError") 异常。

在 3.4 版本发生变更: 当 _format_spec_ 不是空字符串时， `object().__format__(format_spec)` 会触发 [`TypeError`](exceptions.md#TypeError "TypeError")。

_class _frozenset( _iterable =set()_)
~~~
    

~~~
返回一个新的 [`frozenset`](stdtypes.md#frozenset "frozenset") 对象，它包含可选参数 _iterable_ 中的元素。 `frozenset` 是一个内置的类。有关此类的文档，请参阅 [`frozenset`](stdtypes.md#frozenset "frozenset") 和 [集合类型 --- set, frozenset](stdtypes.md#types-set)。

请参阅内建的 [`set`](stdtypes.md#set "set")、[`list`](stdtypes.md#list "list")、[`tuple`](stdtypes.md#tuple "tuple") 和 [`dict`](stdtypes.md#dict "dict") 类，以及 [`collections`](collections.md#module-collections "collections: Container datatypes") 模块来了解其它的容器。

getattr( _object_ , _name_ )¶

getattr( _object_ , _name_ , _default_ )
~~~
    

~~~
_object_ 中指定名称的属性的值。 _name_ 必须是字符串。 如果该字符串是对象的某一属性的名称，则结果将为该属性的值。 例如，`getattr(x, 'foobar')` 等同于 `x.foobar`。 如果指定名称的属性不存在，则如果提供了 _default_ 则返回该值，否则将引发 [`AttributeError`](exceptions.md#AttributeError "AttributeError")。 _name_ 不必是一个 Python 标识符 (参见 `setattr()`)。

备注

由于 [私有名称混合](../reference/expressions.md#private-name-mangling) 发生在编译时，因此必须手动混合私有属性（以两个下划线打头的属性）名称以使用 `getattr()` 来提取它。

globals()¶
~~~
    

~~~
返回实现当前模块命名空间的字典。对于函数内的代码，这是在定义函数时设置的，无论函数在哪里被调用都保持不变。

hasattr( _object_ , _name_ )¶
~~~
    

~~~
该实参是一个对象和一个字符串。如果字符串是对象的属性之一的名称，则返回 `True`，否则返回 `False`。（此功能是通过调用 `getattr(object, name)` 看是否有 [`AttributeError`](exceptions.md#AttributeError "AttributeError") 异常来实现的。）

hash( _object_ )¶
~~~
    

~~~
返回该对象的哈希值（如果它有的话）。哈希值是整数。它们在字典查找元素时用来快速比较字典的键。相同大小的数字变量有相同的哈希值（即使它们类型不同，如 1 和 1.0）。

备注

对于具有自定义 `__hash__()` 方法的对象，请注意 `hash()` 会根据主机的位数来截断返回值。 详情参见 [`__hash__`](../reference/datamodel.md#object.__hash__ "object.__hash__")。

help()¶

help( _request_ )
~~~
    

~~~
启动内置的帮助系统（此函数主要在交互式中使用）。如果没有实参，解释器控制台里会启动交互式帮助系统。如果实参是一个字符串，则在模块、函数、类、方法、关键字或文档主题中搜索该字符串，并在控制台上打印帮助信息。如果实参是其他任意对象，则会生成该对象的帮助页。

请注意，如果在调用 `help()` 时，目标函数的形参列表中存在斜杠（/），则意味着斜杠之前的参数只能是位置参数。详情请参阅 [有关仅限位置形参的 FAQ 条目](../faq/programming.md#faq-positional-only-arguments)。

该函数通过 [`site`](site.md#module-site "site: Module responsible for site-specific configuration.") 模块加入到内置命名空间。

在 3.4 版本发生变更: [`pydoc`](pydoc.md#module-pydoc "pydoc: Documentation generator and online help system.") 和 [`inspect`](inspect.md#module-inspect "inspect: Extract information and source code from live objects.") 的变更使得可调用对象的签名信息更加全面和一致。

hex( _x_ )¶
~~~
    

~~~
将整数转换为带前缀 "0x" 前缀的小写十六进制数字符串。 如果 _x_ 不是一个 Python `int` 对象，则它必须定义返回一个整数的 [`__index__()`](../reference/datamodel.md#object.__index__ "object.__index__") 方法。 下面是一些例子:
~~~
    
    
~~~shell
>>> hex(255)
'0xff'
>>> hex(-42)
'-0x2a'
~~~

如果要将整数转换为大写或小写的十六进制字符串，并可选择有无“0x”前缀，则可以使用如下方法：

    
    
~~~shell
>>> '%#x' % 255, '%x' % 255, '%X' % 255
('0xff', 'ff', 'FF')
>>> format(255, '#x'), format(255, 'x'), format(255, 'X')
('0xff', 'ff', 'FF')
>>> f'{255:#x}', f'{255:x}', f'{255:X}'
('0xff', 'ff', 'FF')
~~~

另见 `format()` 获取更多信息。

另请参阅 `int()` 将十六进制字符串转换为以 16 为基数的整数。

备注

如果要获取浮点数的十六进制字符串形式，请使用 [`float.hex()`](stdtypes.md#float.hex "float.hex") 方法。

id( _object_ )¶

    

~~~
返回对象的“标识值”。该值是一个整数，在此对象的生命周期中保证是唯一且恒定的。两个生命期不重叠的对象可能具有相同的 `id()` 值。

**CPython 实现细节：** 这是对象在内在中的地址。

引发一个 [审计事件](sys.md#auditing) `builtins.id`，附带参数 `id`。

input()¶

input( _prompt_ )
~~~
    

~~~
如果存在 _prompt_ 实参，则将其写入标准输出，末尾不带换行符。接下来，该函数从输入中读取一行，将其转换为字符串（除了末尾的换行符）并返回。当读取到 EOF 时，则触发 [`EOFError`](exceptions.md#EOFError "EOFError")。例如:
~~~
    
    
~~~shell
>>> s = input('--> ')  
--> Monty Python's Flying Circus
>>> s  
"Monty Python's Flying Circus"
~~~

如果加载了 [`readline`](readline.md#module-readline "readline: GNU readline support for Python. \(Unix\)") 模块，`input()` 将使用它来提供复杂的行编辑和历史记录功能。

引发一个 [审计事件](3.标准库/sys.md#auditing) `builtins.input` 附带参数 `prompt`。

在成功读取输入之后引发一个 [审计事件](3.标准库/sys.md#auditing) `builtins.input/result` 附带结果。

_class _int( _x =0_)¶

_class _int( _x_ , _base =10_)

    

~~~
返回一个基于数字或字符串 _x_ 构造的整数对象，或者在未给出参数时返回 `0`。 如果 _x_ 定义了 [`__int__()`](../reference/datamodel.md#object.__int__ "object.__int__")，则 `int(x)` 将返回 `x.__int__()`。 如果 _x_ 定义了 [`__index__()`](../reference/datamodel.md#object.__index__ "object.__index__")，则将返回 `x.__index__()`。 如果 _x_ 定义了 [`__trunc__()`](../reference/datamodel.md#object.__trunc__ "object.__trunc__")，则将返回 `x.__trunc__()`。 对于浮点数，这将向零方向截断。

如果 _x_ 不是一个数字或者如果给定了 _base_ ，则 _x_ 必须是一个表示以 _base_ 为基数的整数的字符串、[`bytes`](stdtypes.md#bytes "bytes") 或 [`bytearray`](stdtypes.md#bytearray "bytearray") 实例。 字符串前面还能加上可选的 `+` 或 `-` (中间没有空格)，带有前导的零，带有两侧的空格，并可带有数位之间的单个下划线。

一个以 n 为基数的整数字符串包含多个数位，每个数位代表从 0 到 n-1 范围内的值。 0--9 的值可以用任何 Unicode 十进制数码来表示。 10--35 的值可以用 `a` 到 `z` (或 `A` 到 `Z`) 来表示。 默认的 _base_ 为 10。 允许的基数为 0 和 2--36。 对于基数 2, -8 和 -16 来说字符串前面还能加上可选的 `0b`/`0B`, `0o`/`0O` 或 `0x`/`0X` 前缀，就像代码中的整数字面值那样。 对于基数 0 来说，字符串会以与 [代码中的整数字面值](../reference/lexical_analysis.md#integers) 类似的方式来解读，即实际的基数将由前缀确定为 2, 8, 10 或 16。 基数为 0 还会禁用前导的零: `int('010', 0)` 将是无效的，而 `int('010')` 和 `int('010', 8)` 则是有效的。

整数类型定义请参阅 [数字类型 --- int, float, complex](stdtypes.md#typesnumeric) 。

在 3.4 版本发生变更: 如果 _base_ 不是 `int` 的实例，但 _base_ 对象有 [`base.__index__`](../reference/datamodel.md#object.__index__ "object.__index__") 方法，则会调用该方法来获取进制数。以前的版本使用 [`base.__int__`](../reference/datamodel.md#object.__int__ "object.__int__") 而不是 [`base.__index__`](../reference/datamodel.md#object.__index__ "object.__index__")。

在 3.6 版本发生变更: 您可以使用下划线将代码文字中的数字进行分组。

在 3.7 版本发生变更: _x_ 现在只能作为位置参数。

在 3.8 版本发生变更: 如果 [`__int__()`](../reference/datamodel.md#object.__int__ "object.__int__") 未定义则回退至 [`__index__()`](../reference/datamodel.md#object.__index__ "object.__index__")。

在 3.11 版本发生变更: 委托给 [`__trunc__()`](../reference/datamodel.md#object.__trunc__ "object.__trunc__") 的做法已被弃用。

在 3.11 版本发生变更: `int` 字符串输入和字符串表示形式可受到限制以帮助避免拒绝服务攻击。 当将一个字符串 _x_ 转换为 `int` 或者当将一个 `int` 转换为字符串的操作超出限制则会引发 [`ValueError`](exceptions.md#ValueError "ValueError")。 请参阅 [整数字符串转换长度限制](stdtypes.md#int-max-str-digits) 文档。

isinstance( _object_ , _classinfo_ )¶
~~~
    

~~~
如果 _object_ 参数是 _classinfo_ 参数的实例，或者是其 (直接、间接或 [虚拟](../glossary.md#term-abstract-base-class)) 子类的实例则返回 `True`。 如果 _object_ 不是给定类型的对象，则该函数总是返回 `False`。 如果 _classinfo_ 是由类型对象结成的元组 (或是由其他此类元组递归生成) 或者是多个类型的 [union 类型](stdtypes.md#types-union)，则如果 _object_ 是其中任一类型的实例时将会返回 `True`。 如果 _classinfo_ 不是一个类型或类型元组及此类元组，则会引发 [`TypeError`](exceptions.md#TypeError "TypeError") 异常。 如果之前的检查成功执行则可以不会为无效的类型引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

在 3.10 版本发生变更: _classinfo_ 可以是一个 [union 类型](stdtypes.md#types-union)。

issubclass( _class_ , _classinfo_ )¶
~~~
    

~~~
如果 _class_ 是 _classinfo_ 的子类（直接、间接或 [虚的](../glossary.md#term-abstract-base-class) ），则返回 `True`。类将视为自己的子类。 _classinfo_ 可为类对象的元组（或递归地，其他这样的元组）或 [union 类型](stdtypes.md#types-union)，这时如果 _class_ 是 _classinfo_ 中任何条目的子类，则返回 `True` 。任何其他情况都会触发 [`TypeError`](exceptions.md#TypeError "TypeError") 异常。

在 3.10 版本发生变更: _classinfo_ 可以是一个 [union 类型](stdtypes.md#types-union)。

iter( _object_ )¶

iter( _object_ , _sentinel_ )
~~~
    

~~~
返回一个 [iterator](../glossary.md#term-iterator) 对象。根据是否存在第二个实参，第一个实参的解释是非常不同的。如果没有第二个实参， _object_ 必须是支持 [iterable](../glossary.md#term-iterable) 协议（有 `__iter__()` 方法）的集合对象，或必须支持序列协议（有 `__getitem__()` 方法，且数字参数从 `0` 开始）。如果它不支持这些协议，会触发 [`TypeError`](exceptions.md#TypeError "TypeError")。如果有第二个实参 _sentinel_ ，那么 _object_ 必须是可调用的对象。这种情况下生成的迭代器，每次迭代调用它的 [`__next__()`](stdtypes.md#iterator.__next__ "iterator.__next__") 方法时都会不带实参地调用 _object_ ；如果返回的结果是 _sentinel_ 则触发 [`StopIteration`](exceptions.md#StopIteration "StopIteration")，否则返回调用结果。

另请参阅 [迭代器类型](stdtypes.md#typeiter)。

适合 `iter()` 的第二种形式的应用之一是构建块读取器。 例如，从二进制数据库文件中读取固定宽度的块，直至到达文件的末尾:
~~~
    
    
~~~
from functools import partial
with open('mydata.db', 'rb') as f:
    for block in iter(partial(f.read, 64), b''):
        process_block(block)
~~~

len( _s_ )¶

    

~~~
返回对象的长度（元素个数）。实参可以是序列（如 string、bytes、tuple、list 或 range 等）或集合（如 dictionary、set 或 frozen set 等）。

**CPython 实现细节：** `len` 对于大于 [`sys.maxsize`](sys.md#sys.maxsize "sys.maxsize") 的长度如 [`range(2 ** 100)`](stdtypes.md#range "range") 会引发 [`OverflowError`](exceptions.md#OverflowError "OverflowError")。

_class _list

_class _list( _iterable_ )
~~~
    

~~~
虽然被称为函数，[`list`](stdtypes.md#list "list") 实际上是一种可变序列类型，详情请参阅 [列表](stdtypes.md#typesseq-list) 和 [序列类型 --- list, tuple, range](stdtypes.md#typesseq)。

locals()¶
~~~
    

~~~
更新并返回表示当前本地符号表的字典。 在函数代码块但不是类代码块中调用 `locals()` 时将返回自由变量。 请注意在模块层级上，`locals()` 和 `globals()` 是同一个字典。

备注

不要更改此字典的内容；更改不会影响解释器使用的局部变量或自由变量的值。

map( _function_ , _iterable_ , _* iterables_)¶
~~~
    

~~~
返回一个将 _function_ 应用于 _iterable_ 的每一项，并产生其结果的迭代器。 如果传入了额外的 _iterables_ 参数，则 _function_ 必须接受相同个数的参数并被用于到从所有可迭代对象中并行获取的项。 当有多个可迭代对象时，当最短的可迭代对象耗尽则整个迭代将会停止。 对于函数的输入已经是参数元组的情况，请参阅 [`itertools.starmap()`](itertools.md#itertools.starmap "itertools.starmap")。

max( _iterable_ , _*_ , _key =None_)¶

max( _iterable_ , _*_ , _default_ , _key =None_)

max( _arg1_ , _arg2_ , _* args_, _key =None_)
~~~
    

~~~
返回可迭代对象中最大的元素，或者返回两个及以上实参中最大的。

如果只提供了一个位置参数，它必须是非空 [iterable](../glossary.md#term-iterable)，返回可迭代对象中最大的元素；如果提供了两个及以上的位置参数，则返回最大的位置参数。

有两个可选只能用关键字的实参。 _key_ 实参指定排序函数用的参数，如传给 [`list.sort()`](stdtypes.md#list.sort "list.sort") 的。 _default_ 实参是当可迭代对象为空时返回的值。如果可迭代对象为空，并且没有给 _default_ ，则会触发 [`ValueError`](exceptions.md#ValueError "ValueError")。

如果有多个最大元素，则此函数将返回第一个找到的。这和其他稳定排序工具如 `sorted(iterable, key=keyfunc, reverse=True)[0]` 和 `heapq.nlargest(1, iterable, key=keyfunc)` 保持一致。

在 3.4 版本加入: keyword-only 实参 _default_ 。

在 3.8 版本发生变更: _key_ 可以为 `None`。

_class _memoryview( _object_ )
~~~
    

~~~
返回由给定实参创建的“内存视图”对象。有关详细信息，请参阅 [内存视图](stdtypes.md#typememoryview)。

min( _iterable_ , _*_ , _key =None_)¶

min( _iterable_ , _*_ , _default_ , _key =None_)

min( _arg1_ , _arg2_ , _* args_, _key =None_)
~~~
    

~~~
返回可迭代对象中最小的元素，或者返回两个及以上实参中最小的。

如果只提供了一个位置参数，它必须是 [iterable](../glossary.md#term-iterable)，返回可迭代对象中最小的元素；如果提供了两个及以上的位置参数，则返回最小的位置参数。

有两个可选只能用关键字的实参。 _key_ 实参指定排序函数用的参数，如传给 [`list.sort()`](stdtypes.md#list.sort "list.sort") 的。 _default_ 实参是当可迭代对象为空时返回的值。如果可迭代对象为空，并且没有给 _default_ ，则会触发 [`ValueError`](exceptions.md#ValueError "ValueError")。

如果有多个最小元素，则此函数将返回第一个找到的。这和其他稳定排序工具如 `sorted(iterable, key=keyfunc)[0]` 和 `heapq.nsmallest(1, iterable, key=keyfunc)` 保持一致。

在 3.4 版本加入: keyword-only 实参 _default_ 。

在 3.8 版本发生变更: _key_ 可以为 `None`。

next( _iterator_ )¶

next( _iterator_ , _default_ )
~~~
    

~~~
通过调用 [iterator](../glossary.md#term-iterator) 的 [`__next__()`](stdtypes.md#iterator.__next__ "iterator.__next__") 方法获取下一个元素。如果迭代器耗尽，则返回给定的 _default_ ，如果没有默认值则触发 [`StopIteration`](exceptions.md#StopIteration "StopIteration")。

_class _object¶
~~~
    

~~~
返回一个不带特征的新对象。`object` 是所有类的基类。它带有所有 Python 类实例均通用的方法。本函数不接受任何参数。

备注

由于 `object` 没有 [`__dict__`](stdtypes.md#object.__dict__ "object.__dict__")，因此无法将任意属性赋给 `object` 的实例。

oct( _x_ )¶
~~~
    

~~~
将整数转换为带前缀 "0o" 的八进制数字符串。 结果是一个合法的 Python 表达式。 如果 _x_ 不是一个 Python `int` 对象，则它必须定义返回一个整数的 [`__index__()`](../reference/datamodel.md#object.__index__ "object.__index__") 方法。 例如:
~~~
    
    
~~~shell
>>> oct(8)
'0o10'
>>> oct(-56)
'-0o70'
~~~

若要将整数转换为八进制字符串，并可选择是否带有“0o”前缀，可采用如下方法：

    
    
~~~shell
>>> '%#o' % 10, '%o' % 10
('0o12', '12')
>>> format(10, '#o'), format(10, 'o')
('0o12', '12')
>>> f'{10:#o}', f'{10:o}'
('0o12', '12')
~~~

另见 `format()` 获取更多信息。

> open( _file_ , _mode ='r'_, _buffering =-1_, _encoding =None_, _errors =None_, _newline =None_, _closefd =True_, _opener =None_)¶
    

打开 _file_ 并返回对应的 [file object](../glossary.md#term-file-object)。 如果该文件不能被打开，则引发 [`OSError`](3.标准库/exceptions.md#OSError "OSError")。 请参阅 [读写文件](7.%20输入与输出.md#tut-files) 获取此函数的更多用法示例。

_file_ 是一个 [path-like object](../glossary.md#term-path-like-object)，表示将要打开的文件的路径（绝对路径或者相对当前工作目录的路径），也可以是要封装文件对应的整数类型文件描述符。（如果给出的是文件描述符，则当返回的 I/O 对象关闭时它也会关闭，除非将 _closefd_ 设为 `False` 。）

_mode_ 是一个指明文件打开模式的可选字符串。 它默认为 `'r'` 表示以文本模式读取。 其他常见模式有表示写入的 `'w'` (若文件已存在则将其清空)，表示独占创建的 `'x'`，以及表示追加写入的 `'a'` (在 _某些_ Unix 系统上，这意味着无论当前查找位置在哪里 _所有_ 写入操作都将追加到文件末尾)。 在文本模式下，如果未指定 _encoding_ 则所使用的编码格式将依赖于具体平台: [`locale.getencoding()`](locale.md#locale.getencoding "locale.getencoding") 会被调用以获取当前语言区域的编码格式。 (对于读取和写入原始字节数据请使用二进制模式并且不要指定 _encoding_ 。) 可用的模式有:

字符

|

含意  
  
---|---  
  
`'r'`

|

读取（默认）  
  
`'w'`

|

写入，并先截断文件  
  
`'x'`

|

排它性创建，如果文件已存在则失败  
  
`'a'`

|

打开文件用于写入，如果文件存在则在末尾追加  
  
`'b'`

|

二进制模式  
  
`'t'`

|

文本模式（默认）  
  
`'+'`

|

打开用于更新（读取与写入）  
  
默认模式为 `'r'` （打开文件用于读取文本，与 `'rt'` 同义）。`'w+'` 和 `'w+b'` 模式将打开文件并清空内容。而 `'r+'` 和 `'r+b'` 模式将打开文件但不清空内容。

正如在 [概述](io.md#io-overview) 中提到的，Python区分二进制和文本I/O。以二进制模式打开的文件（包括 _mode_ 参数中的 `'b'` ）返回的内容为 [`bytes`](stdtypes.md#bytes "bytes") 对象，不进行任何解码。在文本模式下（默认情况下，或者在 _mode_ 参数中包含 `'t'` ）时，文件内容返回为 [`str`](stdtypes.md#str "str") ，首先使用指定的 _encoding_ （如果给定）或者使用平台默认的的字节编码解码。

备注

Python不依赖于底层操作系统的文本文件概念;所有处理都由Python本身完成，因此与平台无关。

_buffering_ 是一个可选的整数，用于设置缓冲策略。 传入 0 来关闭缓冲（只允许在二进制模式下），传入 1 来选择行缓冲（只在文本模式下可用），传入一个整数 > 1 来表示固定大小的块缓冲区的字节大小。注意，这样指定缓冲区的大小适用于二进制缓冲的 I/O ，但 `TextIOWrapper` （即用 `mode='r+'` 打开的文件）会有另一种缓冲。要禁用在 `TextIOWrapper` 缓冲，考虑使用 [`io.TextIOWrapper.reconfigure()`](io.md#io.TextIOWrapper.reconfigure "io.TextIOWrapper.reconfigure") 的 `write_through` 标志来。当没有给出 _buffering_ 参数时，默认的缓冲策略工作如下。

  * 二进制文件以固定大小的块进行缓冲；缓冲区的大小是使用启发方式来尝试确定底层设备的“块大小”并会回退至 [`io.DEFAULT_BUFFER_SIZE`](io.md#io.DEFAULT_BUFFER_SIZE "io.DEFAULT_BUFFER_SIZE")。 在许多系统上，缓冲区的长度通常为 4096 或 8192 字节。

  * “交互式”文本文件（ [`isatty()`](io.md#io.IOBase.isatty "io.IOBase.isatty") 返回 `True` 的文件）使用行缓冲。其他文本文件使用上述策略用于二进制文件。

_encoding_ 是用于编码或编码文件的编码格式名称。 这应当只有文本模式下使用。 默认的编码格式依赖于具体平台 (即 [`locale.getencoding()`](locale.md#locale.getencoding "locale.getencoding") 所返回的值)，但是任何 Python 支持的 [text encoding](../glossary.md#term-text-encoding) 都可以被使用。 请参阅 [`codecs`](codecs.md#module-codecs "codecs: Encode and decode data and streams.") 模块获取受支持的编码格式列表。

_errors_ 是一个可选的字符串参数，用于指定如何处理编码和解码错误 - 这不能在二进制模式下使用。可以使用各种标准错误处理程序（列在 [错误处理方案](codecs.md#error-handlers) ），但是使用 [`codecs.register_error()`](codecs.md#codecs.register_error "codecs.register_error") 注册的任何错误处理名称也是有效的。标准名称包括:

  * 如果存在编码错误，`'strict'` 会引发 [`ValueError`](3.标准库/exceptions.md#ValueError "ValueError") 异常。 默认值 `None` 具有相同的效果。

  * `'ignore'` 忽略错误。请注意，忽略编码错误可能会导致数据丢失。

  * `'replace'` 会将替换标记（例如 `'?'` ）插入有错误数据的地方。

  * `'surrogateescape'` 将把任何不正确的字节表示为 U+DC80 至 U+DCFF 范围内的下方替代码位。 当在写入数据时使用 `surrogateescape` 错误处理句柄时这些替代码位会被转回到相同的字节。 这适用于处理具有未知编码格式的文件。

  * 只有在写入文件时才支持 `'xmlcharrefreplace'`。编码不支持的字符将替换为相应的XML字符引用 `&#nnn;`。

  * `'backslashreplace'` 用Python的反向转义序列替换格式错误的数据。

  * `'namereplace'` （也只在编写时支持）用 `\N{...}` 转义序列替换不支持的字符。

_newline_ 决定如何解析来自流的换行符。 它可以为 `None`, `''`, `'\n'`, `'\r'` 和 `'\r\n'`。 它的工作原理如下:

  * 从流中读取输入时，如果 _newline_ 为 `None`，则启用通用换行模式。输入中的行可以以 `'\n'`，`'\r'` 或 `'\r\n'` 结尾，这些行被翻译成 `'\n'` 在返回呼叫者之前。如果它是 `''`，则启用通用换行模式，但行结尾将返回给调用者未翻译。如果它具有任何其他合法值，则输入行仅由给定字符串终止，并且行结尾将返回给未调用的调用者。

  * 将输出写入流时，如果 _newline_ 为 `None`，则写入的任何 `'\n'` 字符都将转换为系统默认行分隔符 [`os.linesep`](os.md#os.linesep "os.linesep")。如果 _newline_ 是 `''` 或 `'\n'`，则不进行翻译。如果 _newline_ 是任何其他合法值，则写入的任何 `'\n'` 字符将被转换为给定的字符串。

如果 _closefd_ 为 `False` 且给出的不是文件名而是文件描述符，那么当文件关闭时，底层文件描述符将保持打开状态。如果给出的是文件名，则 _closefd_ 必须为 `True` （默认值），否则将触发错误。

可以通过传递可调用的 _opener_ 来使用自定义开启器。然后通过使用参数（ _file_ ， _flags_ ）调用 _opener_ 获得文件对象的基础文件描述符。 _opener_ 必须返回一个打开的文件描述符（使用 [`os.open`](os.md#os.open "os.open") as _opener_ 时与传递 `None` 的效果相同）。

新创建的文件是 [不可继承的](os.md#fd-inheritance)。

下面的示例使用 [`os.open()`](os.md#os.open "os.open") 函数的 [dir_fd](os.md#dir-fd) 的形参，从给定的目录中用相对路径打开文件:

    
    
~~~shell
>>> import os
>>> dir_fd = os.open('somedir', os.O_RDONLY)
>>> def opener(path, flags):
...     return os.open(path, flags, dir_fd=dir_fd)
...
>>> with open('spamspam.txt', 'w', opener=opener) as f:
...     print('This will be written to somedir/spamspam.txt', file=f)
...
>>> os.close(dir_fd)  # don't leak a file descriptor
~~~

`open()` 函数所返回的 [file object](../glossary.md#term-file-object) 类型取决于所用模式。 当使用 `open()` 以文本模式 (`'w'`, `'r'`, `'wt'`, `'rt'` 等) 打开文件时，它将返回 [`io.TextIOBase`](io.md#io.TextIOBase "io.TextIOBase") (具体为 [`io.TextIOWrapper`](io.md#io.TextIOWrapper "io.TextIOWrapper")) 的一个子类。 当使用缓冲以二进制模式打开文件时，返回的类是 [`io.BufferedIOBase`](io.md#io.BufferedIOBase "io.BufferedIOBase") 的一个子类。 具体的类会有多种：在只读的二进制模式下，它将返回 [`io.BufferedReader`](io.md#io.BufferedReader "io.BufferedReader")；在写入二进制和追加二进制模式下，它将返回 [`io.BufferedWriter`](io.md#io.BufferedWriter "io.BufferedWriter")，而在读/写模式下，它将返回 [`io.BufferedRandom`](io.md#io.BufferedRandom "io.BufferedRandom")。 当禁用缓冲时，则会返回原始流，即 [`io.RawIOBase`](io.md#io.RawIOBase "io.RawIOBase") 的一个子类 [`io.FileIO`](io.md#io.FileIO "io.FileIO")。

另请参阅文件操作模块，如 [`fileinput`](fileinput.md#module-fileinput "fileinput: Loop over standard input or a list of files.")、[`io`](io.md#module-io "io: Core tools for working with streams.") （声明了 `open()`）、[`os`](os.md#module-os "os: Miscellaneous operating system interfaces.")、[`os.path`](os.path.md#module-os.path "os.path: Operations on pathnames.")、[`tempfile`](tempfile.md#module-tempfile "tempfile: Generate temporary files and directories.") 和 [`shutil`](shutil.md#module-shutil "shutil: High-level file operations, including copying.")。

引发一个 [审计事件](3.标准库/sys.md#auditing) `open` 附带参数 `file`, `mode`, `flags`。

`mode` 与 `flags` 参数可以在原始调用的基础上被修改或传递。

在 3.3 版本发生变更:

  * 增加了 _opener_ 形参。

  * 增加了 `'x'` 模式。

  * 过去触发的 [`IOError`](3.标准库/exceptions.md#IOError "IOError")，现在是 [`OSError`](3.标准库/exceptions.md#OSError "OSError") 的别名。

  * 如果文件已存在但使用了排它性创建模式（ `'x'` ），现在会触发 [`FileExistsError`](3.标准库/exceptions.md#FileExistsError "FileExistsError")。

在 3.4 版本发生变更:

  * 文件现在禁止继承。

在 3.5 版本发生变更:

  * 如果系统调用被中断，但信号处理程序没有触发异常，此函数现在会重试系统调用，而不是触发 [`InterruptedError`](3.标准库/exceptions.md#InterruptedError "InterruptedError") 异常 (原因详见 [**PEP 475**](https://peps.python.org/pep-0475/))。

  * 增加了 `'namereplace'` 错误处理接口。

在 3.6 版本发生变更:

  * 增加对实现了 [`os.PathLike`](os.md#os.PathLike "os.PathLike") 对象的支持。

  * 在 Windows 上，打开一个控制台缓冲区将返回 [`io.RawIOBase`](io.md#io.RawIOBase "io.RawIOBase") 的子类，而不是 [`io.FileIO`](io.md#io.FileIO "io.FileIO")。

在 3.11 版本发生变更: `'U'` 模式已被移除。

ord( _c_ )¶

    

~~~
对表示单个 Unicode 字符的字符串，返回代表它 Unicode 码点的整数。例如 `ord('a')` 返回整数 `97`， `ord('€')` （欧元符号）返回 `8364` 。这是 `chr()` 的逆函数。

pow( _base_ , _exp_ , _mod =None_)¶
~~~
    

~~~
返回 _base_ 的 _exp_ 次幂；如果 _mod_ 存在，则返回 _base_ 的 _exp_ 次幂对 _mod_ 取余（比 `pow(base, exp) % mod` 更高效）。 两参数形式 `pow(base, exp)` 等价于乘方运算符: `base**exp`。

参数必须为数值类型。 对于混用的操作数类型，则适用二元算术运算符的类型强制转换规则。 对于 `int` 操作数，结果具有与操作数相同的类型（转换后），除非第二个参数为负值；在这种情况下，所有参数将被转换为浮点数并输出浮点数结果。 例如，`pow(10, 2)` 返回 `100`，但 `pow(10, -2)` 返回 `0.01`。 对于 `int` 或 `float` 类型的负基和一个非整数的指数，会产生一个复杂的结果。 例如， `pow(-9, 0.5)` 返回一个接近于 `3j` 的值。

对于 `int` 操作数 _base_ 和 _exp_ ，如果给出 _mod_ ，则 _mod_ 必须为整数类型并且 _mod_ 必须不为零。 如果给出 _mod_ 并且 _exp_ 为负值，则 _base_ 必须相对于 _mod_ 不可整除。 在这种情况下，将会返回 `pow(inv_base, -exp, mod)`，其中 _inv_base_ 为 _base_ 的倒数对 _mod_ 取余。

下面的例子是 `38` 的倒数对 `97` 取余:
~~~
    
    
~~~shell
>>> pow(38, -1, mod=97)
23
>>> 23 * 38 % 97 == 1
True
~~~

在 3.8 版本发生变更: 对于 `int` 操作数，三参数形式的 `pow` 现在允许第二个参数为负值，即可以计算倒数的余数。

在 3.8 版本发生变更: 允许关键字参数。 之前只支持位置参数。

print( _* objects_, _sep =' '_, _end ='\n'_, _file =None_, _flush =False_)¶

    

~~~
将 _objects_ 打印输出至 _file_ 指定的文本流，以 _sep_ 分隔并在末尾加上 _end_ 。 _sep_ 、 _end_ 、 _file_ 和 _flush_ 必须以关键字参数的形式给出。

所有非关键字参数都会被转换为字符串，就像是执行了 [`str()`](stdtypes.md#str "str") 一样，并会被写入到流，以 _sep_ 分隔并在末尾加上 _end_ 。 _sep_ 和 _end_ 都必须为字符串；它们也可以为 `None`，这意味着使用默认值。 如果没有给出 _objects_ ，则 `print()` 将只写入 _end_ 。

_file_ 参数必须是一个具有 `write(string)` 方法的对象；如果参数不存在或为 `None`，则将使用 [`sys.stdout`](sys.md#sys.stdout "sys.stdout")。 由于要打印的参数会被转换为文本字符串，因此 `print()` 不能用于二进制模式的文件对象。 对于这些对象，应改用 `file.write(...)`。

输出缓冲通常由 _file_ 确定。 但是，如果 _flush_ 为真值，流将被强制刷新。

在 3.3 版本发生变更: 增加了 _flush_ 关键字参数。

_class _property( _fget =None_, _fset =None_, _fdel =None_, _doc =None_)¶
~~~
    

~~~
返回 property 属性。

_fget_ 是获取属性值的函数。 _fset_ 是用于设置属性值的函数。 _fdel_ 是用于删除属性值的函数。并且 _doc_ 为属性对象创建文档字符串。

一个典型的用法是定义一个托管属性 `x`:
~~~
    
    
~~~
class C:
    def __init__(self):
        self._x = None

    def getx(self):
        return self._x

    def setx(self, value):
        self._x = value

    def delx(self):
        del self._x

    x = property(getx, setx, delx, "I'm the 'x' property.")
~~~

如果 _c_ 为 _C_ 的实例，`c.x` 将调用 getter，`c.x = value` 将调用 setter， `del c.x` 将调用 deleter。

如果给出， _doc_ 将成为该 property 属性的文档字符串。 否则该 property 将拷贝 _fget_ 的文档字符串（如果存在）。 这令使用 `property()` 作为 [decorator](../glossary.md#term-decorator) 来创建只读的特征属性可以很容易地实现:

    
    
~~~
class Parrot:
    def __init__(self):
        self._voltage = 100000

    @property
    def voltage(self):
        """Get the current voltage."""
        return self._voltage
~~~

以上 `@property` 装饰器会将 `voltage()` 方法转化为一个具有相同名称的只读属性的 "getter"，并将 _voltage_ 的文档字符串设置为 "Get the current voltage."

特征属性对象具有 `getter`, `setter` 以及 `deleter` 方法，它们可用作装饰器来创建该特征属性的副本，并将相应的访问函数设为所装饰的函数。 这最好是用一个例子来解释:

    
    
~~~
class C:
    def __init__(self):
        self._x = None

    @property
    def x(self):
        """I'm the 'x' property."""
        return self._x

    @x.setter
    def x(self, value):
        self._x = value

    @x.deleter
    def x(self):
        del self._x
~~~

上述代码与第一个例子完全等价。 注意一定要给附加函数与原始的特征属性相同的名称 (在本例中为 `x`。)

返回的特征属性对象同样具有与构造器参数相对应的属性 `fget`, `fset` 和 `fdel`。

在 3.5 版本发生变更: 特征属性对象的文档字符串现在是可写的。

_class _range( _stop_ )

_class _range( _start_ , _stop_ , _step =1_)

    

~~~
虽然被称为函数，但 [`range`](stdtypes.md#range "range") 实际上是一个不可变的序列类型，参见在 [range 对象](stdtypes.md#typesseq-range) 与 [序列类型 --- list, tuple, range](stdtypes.md#typesseq) 中的文档说明。

repr( _object_ )¶
~~~
    

~~~
返回包含一个对象的可打印表示形式的字符串。 对于许多类型而言，此函数将会尝试返回一个具有与传给 `eval()` 时相同的值的字符串；在其他情况下，其表示形式将为一个包含对象类型名称和通常包括对象名称和地址的额外信息的用尖括号括起来的字符串。 一个类可以通过定义 `__repr__()` 方法来控制此函数为其实例所返回的内容。 如果 [`sys.displayhook()`](sys.md#sys.displayhook "sys.displayhook") 不可访问，则此函数将会引发 [`RuntimeError`](exceptions.md#RuntimeError "RuntimeError")。

reversed( _seq_ )¶
~~~
    

~~~
返回一个反向的 [iterator](../glossary.md#term-iterator)。 _seq_ 必须是一个具有 `__reversed__()` 方法的对象或者是支持该序列协议（具有从 `0` 开始的整数类型参数的 `__len__()` 方法和 `__getitem__()` 方法）。

round( _number_ , _ndigits =None_)¶
~~~
    

~~~
返回 _number_ 舍入到小数点后 _ndigits_ 位精度的值。 如果 _ndigits_ 被省略或为 `None`，则返回最接近输入值的整数。

对于支持 `round()` 方法的内置类型，结果值会舍入至最接近的 10 的负 _ndigits_ 次幂的倍数；如果与两个倍数同样接近，则选用偶数。因此，`round(0.5)` 和 `round(-0.5)` 均得出 `0` 而 `round(1.5)` 则为 `2`。 _ndigits_ 可为任意整数值（正数、零或负数）。如果省略了 _ndigits_ 或为 `None` ，则返回值将为整数。否则返回值与 _number_ 的类型相同。

对于一般的 Python 对象 `number`, `round` 将委托给 `number.__round__`。

备注

对浮点数执行 `round()` 的行为可能会令人惊讶：例如，`round(2.675, 2)` 将给出 `2.67` 而不是期望的 `2.68`。 这不算是程序错误：这一结果是由于大多数十进制小数实际上都不能以浮点数精确地表示。 请参阅 [浮点算术：争议和限制](../tutorial/floatingpoint.md#tut-fp-issues) 了解更多信息。

_class _set

_class _set( _iterable_ )
~~~
    

~~~
返回一个新的 [`set`](stdtypes.md#set "set") 对象，可以选择带有从 _iterable_ 获取的元素。 `set` 是一个内置类型。 请查看 [`set`](stdtypes.md#set "set") 和 [集合类型 --- set, frozenset](stdtypes.md#types-set) 获取关于这个类的文档。

有关其他容器请参看内置的 [`frozenset`](stdtypes.md#frozenset "frozenset"), [`list`](stdtypes.md#list "list"), [`tuple`](stdtypes.md#tuple "tuple") 和 [`dict`](stdtypes.md#dict "dict") 类，以及 [`collections`](collections.md#module-collections "collections: Container datatypes") 模块。

setattr( _object_ , _name_ , _value_ )¶
~~~
    

~~~
本函数与 `getattr()` 相对应。其参数为一个对象、一个字符串和一个任意值。字符串可以为某现有属性的名称，或为新属性。只要对象允许，函数会将值赋给属性。如 `setattr(x, 'foobar', 123)` 等价于 `x.foobar = 123`。

_name_ 无需为在 [标识符和关键字](../reference/lexical_analysis.md#identifiers) 中定义的 Python 标识符除非对象选择强制这样做，例如在一个自定义的 [`__getattribute__()`](../reference/datamodel.md#object.__getattribute__ "object.__getattribute__") 中或是通过 [`__slots__`](../reference/datamodel.md#object.__slots__ "object.__slots__")。 一个名称不为标识符的属性将不可使用点号标记来访问，但是可以通过 `getattr()` 等来访问。

备注

由于 [私有名称混合](../reference/expressions.md#private-name-mangling) 发生在编译时，因此必须手动混合私有属性（以两个下划线打头的属性）名称以便使用 `setattr()` 来设置它。

_class _slice( _stop_ )¶

_class _slice( _start_ , _stop_ , _step =None_)
~~~
    

~~~
返回一个 [slice](../glossary.md#term-slice) 对象，代表由 `range(start, stop, step)` 指定索引集的切片。 其中参数 _start_ 和 _step_ 的默认值为 `None`。切片对象具有只读数据属性 `start` 、`stop` 和 `step`，只是返回对应的参数值（或默认值）。这几个属性没有其他明确的功能；不过 NumPy 和其他第三方扩展会用到。在使用扩展索引语法时，也会生成切片对象。例如： `a[start:stop:step]` 或 `a[start:stop, i]`。 另一种方案是返回迭代器对象，可参阅 [`itertools.islice()`](itertools.md#itertools.islice "itertools.islice") 。

在 3.12 版本发生变更: Slice 对象现在将为 [hashable](../glossary.md#term-hashable) (如果 `start`, `stop` 和 `step` 均为可哈希对象)。

sorted( _iterable_ , _/_ , _*_ , _key =None_, _reverse =False_)¶
~~~
    

~~~
根据 _iterable_ 中的项返回一个新的已排序列表。

具有两个可选参数，它们都必须指定为关键字参数。

_key_ 指定带有单个参数的函数，用于从 _iterable_ 的每个元素中提取用于比较的键 (例如 `key=str.lower`)。 默认值为 `None` (直接比较元素)。

_reverse_ 为一个布尔值。 如果设为 `True`，则每个列表元素将按反向顺序比较进行排序。

使用 [`functools.cmp_to_key()`](functools.md#functools.cmp_to_key "functools.cmp_to_key") 可将老式的 _cmp_ 函数转换为 _key_ 函数。

内置的 `sorted()` 确保是稳定的。 如果一个排序确保不会改变比较结果相等的元素的相对顺序就称其为稳定的 --- 这有利于进行多重排序（例如先按部门、再按薪级排序）。

排序算法只使用 `<` 在项目之间比较。 虽然定义一个 [`__lt__()`](../reference/datamodel.md#object.__lt__ "object.__lt__") 方法就足以进行排序，但 [**PEP 8**](https://peps.python.org/pep-0008/) 建议实现所有六个 [富比较](../reference/expressions.md#comparisons) 。 这将有助于避免在与其他排序工具（如 `max()` ）使用相同的数据时出现错误，这些工具依赖于不同的底层方法。实现所有六个比较也有助于避免混合类型比较的混乱，因为混合类型比较可以调用反射到 [`__gt__()`](../reference/datamodel.md#object.__gt__ "object.__gt__") 的方法。

有关排序示例和简要排序教程，请参阅 [排序指南](../howto/sorting.md#sortinghowto) 。

@staticmethod¶
~~~
    

~~~
将方法转换为静态方法。

静态方法不会接收隐式的第一个参数。要声明一个静态方法，请使用此语法
~~~
    
    
~~~
class C:
    @staticmethod
    def f(arg1, arg2, argN): ...
~~~

`@staticmethod` 这样的形式称为函数的 [decorator](../glossary.md#term-decorator) \-- 详情参阅 [函数定义](8.%20复合语句.md#function)。

静态方法既可以由类中调用（如 `C.f()`），也可以由实例中调用（如 ``C().f()`）。此外，还可以作为普通的函数进行调用（如 `f()`）。

Python 的静态方法与 Java 或 C++ 类似。另请参阅 `classmethod()` ，可用于创建另一种类构造函数。

像所有装饰器一样，也可以像常规函数一样调用 `staticmethod` ，并对其结果执行某些操作。比如某些情况下需要从类主体引用函数并且您希望避免自动转换为实例方法。对于这些情况，请使用此语法:

    
    
~~~python
def regular_function():
    ...

class C:
    method = staticmethod(regular_function)
~~~

想了解更多有关静态方法的信息，请参阅 [标准类型层级结构](3.%20数据模型.md#types) 。

在 3.10 版本发生变更: 静态方法继承了方法的多个属性（ `__module__`、 `__name__`、 `__qualname__`、 `__doc__` 和 `__annotations__`），还拥有一个新的 `__wrapped__` 属性，并且现在还可以作为普通函数进行调用。

_class _str( _object =''_)

_class _str( _object =b''_, _encoding ='utf-8'_, _errors ='strict'_)

    

~~~
返回一个 [`str`](stdtypes.md#str "str") 版本的 _object_ 。有关详细信息，请参阅 [`str()`](stdtypes.md#str "str") 。

`str` 是内置字符串 [class](../glossary.md#term-class) 。更多关于字符串的信息查看 [文本序列类型 --- str](stdtypes.md#textseq)。

sum( _iterable_ , _/_ , _start =0_)¶
~~~
    

~~~
从 _start_ 开始自左向右对 _iterable_ 的项求和并返回总计值。 _iterable_ 的项通常为数字，而 start 值则不允许为字符串。

对某些用例来说，存在 `sum()` 的更好替代。 拼接字符串序列的更好更快方式是调用 `''.join(sequence)`。 要以扩展精度对浮点值求和，请参阅 [`math.fsum()`](math.md#math.fsum "math.fsum")。 要拼接一系列可迭代对象，请考虑使用 [`itertools.chain()`](itertools.md#itertools.chain "itertools.chain")。

在 3.8 版本发生变更: _start_ 形参可用关键字参数形式来指定。

在 3.12 版本发生变更: Summation of floats switched to an algorithm that gives higher accuracy and better commutativity on most builds.

_class _super¶

_class _super( _type_ , _object_or_type =None_)
~~~
    

~~~
返回一个代理对象，它会将方法调用委托给 _type_ 的父类或兄弟类。 这对于访问已在类中被重载的继承方法很有用。

_object_or_type_ 确定要用于搜索的 [method resolution order](../glossary.md#term-method-resolution-order)。 搜索会从 _type_ 之后的类开始。

举例来说，如果 _object_or_type_ 的 [`__mro__`](stdtypes.md#class.__mro__ "class.__mro__") 为 `D -> B -> C -> A -> object` 并且 _type_ 的值为 `B`，则 `super()` 将会搜索 `C -> A -> object`。

_object_or_type_ 的 [`__mro__`](stdtypes.md#class.__mro__ "class.__mro__") 属性列出了 `getattr()` 和 `super()` 所共同使用的方法解析搜索顺序。 该属性是动态的并可在任何继承层级结构发生更新时被改变。

如果省略第二个参数，则返回的超类对象是未绑定的。 如果第二个参数为一个对象，则 `isinstance(obj, type)` 必须为真值。 如果第二个参数为一个类型，则 `issubclass(type2, type)` 必须为真值（这适用于类方法）。

_super_ 有两个典型用例。 在具有单继承的类层级结构中， _super_ 可用来引用父类而不必显式地指定它们的名称，从而令代码更易维护。 这种用法与其他编程语言中 _super_ 的用法非常相似。

第二个用例是在动态执行环境中支持协作多重继承。 此用例为 Python 所独有而不存在于静态编码语言或仅支持单继承的语言当中。 这使用实现“菱形图”成为可能，即有多个基类实现相同的方法。 好的设计强制要求这样的方法在每个情况下都具有相同的调用签名（因为调用顺序是在运行时确定的，也因为这个顺序要适应类层级结构的更改，还因为这个顺序可能包括在运行时之前未知的兄弟类）。

对于以上两个用例，典型的超类调用看起来是这样的:
~~~
    
    
~~~
class C(B):
    def method(self, arg):
        super().method(arg)    # This does the same thing as:
                               # super(C, self).method(arg)
~~~

除了方法查找之外，`super()` 也可用于属性查找。 一个可能的应用场合是在上级或同级类中调用 [描述器](../glossary.md#term-descriptor)。

请注意 `super()` 是作为显式加点属性查找的绑定过程的一部分来实现的，例如 `super().__getitem__(name)`。 它做到这一点是通过实现自己的 `__getattribute__()` 方法，这样就能以可预测的顺序搜索类，并且支持协作多重继承。 对应地，`super()` 在像 `super()[name]` 这样使用语句或操作符进行隐式查找时则未被定义。

还要注意的是，除了零个参数的形式以外，`super()` 并不限于在方法内部使用。 两个参数的形式明确指定参数并进行相应的引用。 零个参数的形式仅适用于类定义内部，因为编译器需要填入必要的细节以正确地检索到被定义的类，还需要让普通方法访问当前实例。

对于有关如何使用 `super()` 来如何设计协作类的实用建议，请参阅 [使用 super() 的指南](https://rhettinger.wordpress.com/2011/05/26/super-considered-super/)。

_class _tuple

_class _tuple( _iterable_ )

    

~~~
虽然被称为函数，但 [`tuple`](stdtypes.md#tuple "tuple") 实际上是一个不可变的序列类型，参见在 [元组](stdtypes.md#typesseq-tuple) 与 [序列类型 --- list, tuple, range](stdtypes.md#typesseq) 中的文档说明。

_class _type( _object_ )¶

_class _type( _name_ , _bases_ , _dict_ , _** kwds_)
~~~
    

~~~
传入一个参数时，返回 _object_ 的类型。 返回值是一个 type 对象，通常与 [`object.__class__`](stdtypes.md#instance.__class__ "instance.__class__") 所返回的对象相同。

推荐使用 `isinstance()` 内置函数来检测对象的类型，因为它会考虑子类的情况。

传入三个参数时，返回一个新的 type 对象。 这在本质上是 [`class`](../reference/compound_stmts.md#class) 语句的一种动态形式， _name_ 字符串即类名并会成为 [`__name__`](stdtypes.md#definition.__name__ "definition.__name__") 属性； _bases_ 元组包含基类并会成为 [`__bases__`](stdtypes.md#class.__bases__ "class.__bases__") 属性；如果为空则会添加所有类的终极基类 `object`。 _dict_ 字典包含类主体的属性和方法定义；它在成为 [`__dict__`](stdtypes.md#object.__dict__ "object.__dict__") 属性之前可能会被拷贝或包装。 下面两条语句会创建相同的 `type` 对象:
~~~
    
    
~~~shell
>>> class X:
...     a = 1
...
>>> X = type('X', (), dict(a=1))
~~~

另请参阅 [类型对象](stdtypes.md#bltin-type-objects)。

提供给三参数形式的关键字参数会被传递给适当的元类机制 (通常为 [`__init_subclass__()`](3.%20数据模型.md#object.__init_subclass__ "object.__init_subclass__"))，相当于类定义中关键字 (除了 _metaclass_ ) 的行为方式。

另请参阅 [自定义类创建](3.%20数据模型.md#class-customization)。

在 3.6 版本发生变更: `type` 的子类如果未重载 `type.__new__`，将不再能使用一个参数的形式来获取对象的类型。

vars()¶

vars( _object_ )

    

~~~
返回模块、类、实例或任何其它具有 [`__dict__`](stdtypes.md#object.__dict__ "object.__dict__") 属性的对象的 [`__dict__`](stdtypes.md#object.__dict__ "object.__dict__") 属性。

模块和实例这样的对象具有可更新的 [`__dict__`](stdtypes.md#object.__dict__ "object.__dict__") 属性；但是，其它对象的 [`__dict__`](stdtypes.md#object.__dict__ "object.__dict__") 属性可能会设为限制写入（例如，类会使用 [`types.MappingProxyType`](types.md#types.MappingProxyType "types.MappingProxyType") 来防止直接更新字典）。

不带参数时，`vars()` 的行为类似 `locals()`。 请注意，locals 字典仅对于读取起作用，因为对 locals 字典的更新会被忽略。

如果指定了一个对象但它没有 [`__dict__`](stdtypes.md#object.__dict__ "object.__dict__") 属性（例如，当它所属的类定义了 [`__slots__`](../reference/datamodel.md#object.__slots__ "object.__slots__") 属性时）则会引发 [`TypeError`](exceptions.md#TypeError "TypeError") 异常。

zip( _* iterables_, _strict =False_)¶
~~~
    

~~~
在多个迭代器上并行迭代，从每个迭代器返回一个数据项组成元组。

示例:
~~~
    
    
~~~shell
>>> for item in zip([1, 2, 3], ['sugar', 'spice', 'everything nice']):
...     print(item)
...
(1, 'sugar')
(2, 'spice')
(3, 'everything nice')
~~~

更正式的说法： `zip()` 返回元组的迭代器，其中第 _i_ 个元组包含的是每个参数迭代器的第 _i_ 个元素。

不妨换一种方式认识 `zip()` ：它会把行变成列，把列变成行。这类似于 [矩阵转置](https://en.wikipedia.org/wiki/Transpose) 。

`zip()` 是延迟执行的：直至迭代时才会对元素进行处理，比如 `for` 循环或放入 [`list`](stdtypes.md#list "list") 中。

值得考虑的是，传给 `zip()` 的可迭代对象可能长度不同；有时是有意为之，有时是因为准备这些对象的代码存在错误。Python 提供了三种不同的处理方案：

  * 默认情况下，`zip()` 在最短的迭代完成后停止。较长可迭代对象中的剩余项将被忽略，结果会裁切至最短可迭代对象的长度：
    
        >>> list(zip(range(3), ['fee', 'fi', 'fo', 'fum']))
    [(0, 'fee'), (1, 'fi'), (2, 'fo')]
    

  * 通常 `zip()` 用于可迭代对象等长的情况下。这时建议用 `strict=True` 的选项。输出与普通的 `zip()` 相同：。
    
        >>> list(zip(('a', 'b', 'c'), (1, 2, 3), strict=True))
    [('a', 1), ('b', 2), ('c', 3)]
    

与默认行为不同，如果一个可迭代对象在其他几个之前被耗尽则会引发 [`ValueError`](3.标准库/exceptions.md#ValueError "ValueError"):

    
        >>> for item in zip(range(3), ['fee', 'fi', 'fo', 'fum'], strict=True):  
    ...     print(item)
    ...
    (0, 'fee')
    (1, 'fi')
    (2, 'fo')
    Traceback (most recent call last):
      ...
    ValueError: zip() argument 2 is longer than argument 1
    

如果未指定 `strict=True` 参数，所有导致可迭代对象长度不同的错误都会被抑制，这可能会在程序的其他地方表现为难以发现的错误。

  * 为了让所有的可迭代对象具有相同的长度，长度较短的可用常量进行填充。这可由 [`itertools.zip_longest()`](itertools.md#itertools.zip_longest "itertools.zip_longest") 来完成。

极端例子是只有一个可迭代对象参数，`zip()` 会返回一个一元组的迭代器。如果未给出参数，则返回一个空的迭代器。

小技巧：

  * 可确保迭代器的求值顺序是从左到右的。这样就能用 `zip(*[iter(s)]*n, strict=True)` 将数据列表按长度 n 进行分组。这将重复 _相同_ 的迭代器 `n` 次，输出的每个元组都包含 `n` 次调用迭代器的结果。这样做的效果是把输入拆分为长度为 n 的块。

  * `zip()` 与 `*` 运算符相结合可以用来拆解一个列表:
    
        >>> x = [1, 2, 3]
    >>> y = [4, 5, 6]
    >>> list(zip(x, y))
    [(1, 4), (2, 5), (3, 6)]
    >>> x2, y2 = zip(*zip(x, y))
    >>> x == list(x2) and y == list(y2)
    True
    

在 3.10 版本发生变更: 增加了 `strict` 参数。

__import__( _name_ , _globals =None_, _locals =None_, _fromlist =()_, _level =0_)¶

    

~~~
备注

与 [`importlib.import_module()`](importlib.md#importlib.import_module "importlib.import_module") 不同，这是一个日常 Python 编程中不需要用到的高级函数。

此函数会由 [`import`](../reference/simple_stmts.md#import) 语句发起调用。 它可以被替换 (通过导入 [`builtins`](builtins.md#module-builtins "builtins: The module that provides the built-in namespace.") 模块并赋值给 `builtins.__import__`) 以便修改 `import` 语句的语义，但是 **强烈** 不建议这样做，因为使用导入钩子 (参见 [**PEP 302**](https://peps.python.org/pep-0302/)) 通常更容易实现同样的目标，并且不会导致代码问题，因为许多代码都会假定所用的是默认实现。 同样也不建议直接使用 `__import__()` 而应该用 [`importlib.import_module()`](importlib.md#importlib.import_module "importlib.import_module")。

本函数会导入模块 _name_ ，利用 _globals_ 和 _locals_ 来决定如何在包的上下文中解释该名称。 _fromlist_ 给出了应从 _name_ 模块中导入的对象或子模块的名称。标准的实现代码完全不会用到 _locals_ 参数，只用到了 _globals_ 用于确定 [`import`](../reference/simple_stmts.md#import) 语句所在的包上下文。

_level_ 指定是使用绝对还是相对导入。 `0` (默认值) 意味着仅执行绝对导入。 _level_ 为正数值表示相对于模块调用 `__import__()` 的目录，将要搜索的父目录层数 (详情参见 [**PEP 328**](https://peps.python.org/pep-0328/))。

当 _name_ 变量的形式为 `package.module` 时，通常将会返回最高层级的包（第一个点号之前的名称），而 _不是_ 以 _name_ 命名的模块。 但是，当给出了非空的 _fromlist_ 参数时，则将返回以 _name_ 命名的模块。

例如，语句 `import spam` 的结果将为与以下代码作用相同的字节码:
~~~
    
    
~~~
spam = __import__('spam', globals(), locals(), [], 0)
~~~

语句 `import spam.ham` 的结果将为以下调用:

    
    
~~~
spam = __import__('spam.ham', globals(), locals(), [], 0)
~~~

请注意在这里 `__import__()` 是如何返回顶层模块的，因为这是通过 [`import`](7.%20简单语句.md#import) 语句被绑定到特定名称的对象。

另一方面，语句 `from spam.ham import eggs, sausage as saus` 的结果将为

    
    
~~~
_temp = __import__('spam.ham', globals(), locals(), ['eggs', 'sausage'], 0)
eggs = _temp.eggs
saus = _temp.sausage
~~~

在这里， `spam.ham` 模块会由 `__import__()` 返回。 要导入的对象将从此对象中提取并赋值给它们对应的名称。

如果您只想按名称导入模块（可能在包中），请使用 [`importlib.import_module()`](importlib.md#importlib.import_module "importlib.import_module")

在 3.3 版本发生变更: _level_ 的值不再支持负数（默认值也修改为0）。

在 3.9 版本发生变更: 当使用了命令行参数 [`-E`](1.%20命令行与环境.md#cmdoption-E) 或 [`-I`](1.%20命令行与环境.md#cmdoption-I) 时，环境变量 [`PYTHONCASEOK`](1.%20命令行与环境.md#envvar-PYTHONCASEOK) 现在将被忽略。

备注

[1]

解析器只接受 Unix 风格的行结束符。如果您从文件中读取代码，请确保用换行符转换模式转换 Windows 或 Mac 风格的换行符。

