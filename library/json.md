# `json` \--- JSON 编码和解码器¶

**源代码:** [Lib/json/__init__.py](https://github.com/python/cpython/tree/3.12/Lib/json/__init__.py)

* * *

[JSON (JavaScript Object Notation)](http://json.org)，由 [**RFC 7159**](https://datatracker.ietf.org/doc/html/rfc7159.md) (它取代了 [**RFC 4627**](https://datatracker.ietf.org/doc/html/rfc4627.md)) 和 [ECMA-404](http://www.ecma-international.org/publications/standards/Ecma-404.htm) 指定，是一个受 [JavaScript](https://en.wikipedia.org/wiki/JavaScript) 的对象字面值句法启发的轻量级数据交换格式，尽管它不仅仅是一个严格意义上的 JavaScript 的子集 [1]。

警告

在解析来自不受信任恶劣的 JSON 数据时要小心谨慎。 恶意的 JSON 字符串可能导致解码器消耗大量 CPU 和内存资源。 建议对要解析的数据大小进行限制。

`json` 提供了与标准库 [`marshal`](marshal.md#module-marshal "marshal: Convert Python objects to streams of bytes and back \(with different constraints\).") 和 [`pickle`](pickle.md#module-pickle "pickle: Convert Python objects to streams of bytes and back.") 相似的API接口。

对基本的 Python 对象层次结构进行编码：

    
    
~~~shell
>>> import json
>>> json.dumps(['foo', {'bar': ('baz', None, 1.0, 2)}])
'["foo", {"bar": ["baz", null, 1.0, 2]}]'
>>> print(json.dumps("\"foo\bar"))
"\"foo\bar"
>>> print(json.dumps('\u1234'))
"\u1234"
>>> print(json.dumps('\\'))
"\\"
>>> print(json.dumps({"c": 0, "b": 0, "a": 0}, sort_keys=True))
{"a": 0, "b": 0, "c": 0}
>>> from io import StringIO
>>> io = StringIO()
>>> json.dump(['streaming API'], io)
>>> io.getvalue()
'["streaming API"]'
~~~

紧凑编码：

    
    
~~~shell
>>> import json
>>> json.dumps([1, 2, 3, {'4': 5, '6': 7}], separators=(',', ':'))
'[1,2,3,{"4":5,"6":7}]'
~~~

美化输出：

    
    
~~~shell
>>> import json
>>> print(json.dumps({'6': 7, '4': 5}, sort_keys=True, indent=4))
{
    "4": 5,
    "6": 7
}
~~~

Specializing JSON object encoding:

    
    
~~~shell
>>> import json
>>> def custom_json(obj):
...     if isinstance(obj, complex):
...         return {'__complex__': True, 'real': obj.real, 'imag': obj.imag}
...     raise TypeError(f'Cannot serialize object of {type(obj)}')
...
>>> json.dumps(1 + 2j, default=custom_json)
'{"__complex__": true, "real": 1.0, "imag": 2.0}'
~~~

JSON解码：

    
    
~~~shell
>>> import json
>>> json.loads('["foo", {"bar":["baz", null, 1.0, 2]}]')
['foo', {'bar': ['baz', None, 1.0, 2]}]
>>> json.loads('"\\"foo\\bar"')
'"foo\x08ar'
>>> from io import StringIO
>>> io = StringIO('["streaming API"]')
>>> json.load(io)
['streaming API']
~~~

特殊 JSON 对象解码：

    
    
~~~shell
>>> import json
>>> def as_complex(dct):
...     if '__complex__' in dct:
...         return complex(dct['real'], dct['imag'])
...     return dct
...
>>> json.loads('{"__complex__": true, "real": 1, "imag": 2}',
...     object_hook=as_complex)
(1+2j)
>>> import decimal
>>> json.loads('1.1', parse_float=decimal.Decimal)
Decimal('1.1')
~~~

扩展 `JSONEncoder`：

    
    
~~~shell
>>> import json
>>> class ComplexEncoder(json.JSONEncoder):
...     def default(self, obj):
...         if isinstance(obj, complex):
...             return [obj.real, obj.imag]
...         # Let the base class default method raise the TypeError
...         return json.JSONEncoder.default(self, obj)
...
>>> json.dumps(2 + 1j, cls=ComplexEncoder)
'[2.0, 1.0]'
>>> ComplexEncoder().encode(2 + 1j)
'[2.0, 1.0]'
>>> list(ComplexEncoder().iterencode(2 + 1j))
['[2.0', ', 1.0', ']']
~~~

从命令行使用 `json.tool` 来验证并美化输出：

    
    
~~~
$ echo '{"json":"obj"}' | python -m json.tool
{
    "json": "obj"
}
$ echo '{1.2:3.4}' | python -m json.tool
Expecting property name enclosed in double quotes: line 1 column 2 (char 1)
~~~

详细文档请参见 CLI。

备注

JSON 是 [YAML](https://yaml.org/) 1.2 的一个子集。 由该模块的默认设置所产生的 JSON（尤其是默认的 _separators_ 值）也是 YAML 1.0 和 1.1 的一个子集。 因此该模块也能被用作 YAML 序列化器。

备注

这个模块的编码器和解码器默认保护输入和输出的顺序。仅当底层的容器未排序时才会失去顺序。

## 基本使用¶

json.dump( _obj_ , _fp_ , _*_ , _skipkeys =False_, _ensure_ascii =True_, _check_circular =True_, _allow_nan =True_, _cls =None_, _indent =None_, _separators =None_, _default =None_, _sort_keys =False_, _** kw_)¶

    

~~~
使用这个 转换表 将 _obj_ 序列化为 JSON 格式化流形式的 _fp_ (支持 `.write()` 的 [file-like object](../glossary.md#term-file-like-object))。

如果 _skipkeys_ 是 true （默认为 `False`），那么那些不是基本对象（包括 [`str`](stdtypes.md#str "str"), [`int`](functions.md#int "int")、[`float`](functions.md#float "float")、[`bool`](functions.md#bool "bool")、`None`）的字典的键会被跳过；否则引发一个 [`TypeError`](exceptions.md#TypeError "TypeError")。

`json` 模块始终产生 [`str`](stdtypes.md#str "str") 对象而非 [`bytes`](stdtypes.md#bytes "bytes") 对象。因此，`fp.write()` 必须支持 [`str`](stdtypes.md#str "str") 输入。

如果 _ensure_ascii_ 是 true （即默认值），输出保证将所有输入的非 ASCII 字符转义。如果 _ensure_ascii_ 是 false，这些字符会原样输出。

如果 _check_circular_ 为假值 (默认值: `True`)，那么容器类型的循环引用检查会被跳过并且循环引用会引发 [`RecursionError`](exceptions.md#RecursionError "RecursionError") (或者更糟的情况)。

如果 _allow_nan_ 是 false（默认为 `True`），那么在对严格 JSON 规格范围外的 [`float`](functions.md#float "float") 类型值（`nan`、`inf` 和 `-inf`）进行序列化时会引发一个 [`ValueError`](exceptions.md#ValueError "ValueError")。如果 _allow_nan_ 是 true，则使用它们的 JavaScript 等价形式（`NaN`、`Infinity` 和 `-Infinity`）。

如果 _indent_ 是一个非负整数或者字符串，那么 JSON 数组元素和对象成员会被美化输出为该值指定的缩进等级。 如果缩进等级为零、负数或者 `""`，则只会添加换行符。 `None` (默认值) 选择最紧凑的表达。 使用一个正整数会让每一层缩进同样数量的空格。 如果 _indent_ 是一个字符串 (比如 `"\t"`)，那个字符串会被用于缩进每一层。

在 3.2 版本发生变更: 现允许使用字符串作为 _indent_ 而不再仅仅是整数。

当被指定时， _separators_ 应当是一个 `(item_separator, key_separator)` 元组。当 _indent_ 为 `None` 时，默认值取 `(', ', ': ')`，否则取 `(',', ': ')`。为了得到最紧凑的 JSON 表达式，你应该指定其为 `(',', ':')` 以消除空白字符。

在 3.4 版本发生变更: 现当 _indent_ 不是 `None` 时，采用 `(',', ': ')` 作为默认值。

当 _default_ 被指定时，其应该是一个函数，每当某个对象无法被序列化时它会被调用。它应该返回该对象的一个可以被 JSON 编码的版本或者引发一个 [`TypeError`](exceptions.md#TypeError "TypeError")。如果没有被指定，则会直接引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

如果 _sort_keys_ 是 true（默认为 `False`），那么字典的输出会以键的顺序排序。

要使用自定义的 `JSONEncoder` 子类（例如重载了 `default()` 方法来序列化额外类型），请使用 _cls_ 关键字参数来指定它；否则将使用 `JSONEncoder`。

在 3.6 版本发生变更: 所有可选形参现在都是 [仅限关键字参数](../glossary.md#keyword-only-parameter)。

备注

与 [`pickle`](pickle.md#module-pickle "pickle: Convert Python objects to streams of bytes and back.") 和 [`marshal`](marshal.md#module-marshal "marshal: Convert Python objects to streams of bytes and back \(with different constraints\).") 不同，JSON 不是一个具有框架的协议，所以尝试多次使用同一个 _fp_ 调用 `dump()` 来序列化多个对象会产生一个不合规的 JSON 文件。

json.dumps( _obj_ , _*_ , _skipkeys =False_, _ensure_ascii =True_, _check_circular =True_, _allow_nan =True_, _cls =None_, _indent =None_, _separators =None_, _default =None_, _sort_keys =False_, _** kw_)¶
~~~
    

~~~
使用这个 转换表 将 _obj_ 序列化为 JSON 格式的 [`str`](stdtypes.md#str "str")。 其参数的含义与 `dump()` 中的相同。

备注

JSON 中的键-值对中的键永远是 [`str`](stdtypes.md#str "str") 类型的。当一个对象被转化为 JSON 时，字典中所有的键都会被强制转换为字符串。这所造成的结果是字典被转换为 JSON 然后转换回字典时可能和原来的不相等。换句话说，如果 x 具有非字符串的键，则有 `loads(dumps(x)) != x`。

json.load( _fp_ , _*_ , _cls =None_, _object_hook =None_, _parse_float =None_, _parse_int =None_, _parse_constant =None_, _object_pairs_hook =None_, _** kw_)¶
~~~
    

~~~
使用这个 转换表 将 _fp_ (一个支持 `.read()` 并包含一个 JSON 文档的 [text file](../glossary.md#term-text-file) 或者 [binary file](../glossary.md#term-binary-file)) 反序列化为一个 Python 对象。

_object_hook_ 是一个将附带任意已解码的对象字面值 (即 [`dict`](stdtypes.md#dict "dict")) 来调用的可选函数。 _object_hook_ 的返回值会代替 [`dict`](stdtypes.md#dict "dict") 使用。 此特性可被用于实现自定义解码器 (例如 [JSON-RPC](https://www.jsonrpc.org) 的类型提示)。

_object_pairs_hook_ 是一个可选的函数，它会被调用于每一个有序列表对解码出的对象字面量。 _object_pairs_hook_ 的返回值将会取代原本的 [`dict`](stdtypes.md#dict "dict") 。这一特性能够被用于实现自定义解码器。如果 _object_hook_ 也被定义， _object_pairs_hook_ 优先。

在 3.1 版本发生变更: 添加了对 _object_pairs_hook_ 的支持。

_parse_float_ ，如果指定，将与每个要解码 JSON 浮点数的字符串一同调用。默认状态下，相当于 `float(num_str)` 。可以用于对 JSON 浮点数使用其它数据类型和语法分析程序 （比如 [`decimal.Decimal`](decimal.md#decimal.Decimal "decimal.Decimal") ）。

_parse_int_ ，如果指定，将与每个要解码 JSON 整数的字符串一同调用。默认状态下，相当于 `int(num_str)` 。可以用于对 JSON 整数使用其它数据类型和语法分析程序 （比如 [`float`](functions.md#float "float") ）。

在 3.11 版本发生变更: 现在 [`int()`](functions.md#int "int") 默认的 _parse_int_ 会通过解释器的 [整数字符串长度上限](stdtypes.md#int-max-str-digits) 来限制整数字符串的最大长度以帮助避免拒绝服务攻击。

_parse_constant_ ，如果指定，将要与以下字符串中的一个一同调用： `'-Infinity'` ， `'Infinity'` ， `'NaN'` 。如果遇到无效的 JSON 数字则可以使用它引发异常。

在 3.1 版本发生变更: _parse_constant_ 不再调用 'null' ， 'true' ， 'false' 。

要使用自定义的 `JSONDecoder` 子类，用 `cls` 指定他；否则使用 `JSONDecoder` 。额外的关键词参数会通过类的构造函数传递。

如果反序列化的数据不是有效 JSON 文档，引发 `JSONDecodeError` 错误。

在 3.6 版本发生变更: 所有可选形参现在都是 [仅限关键字参数](../glossary.md#keyword-only-parameter)。

在 3.6 版本发生变更: _fp_ 现在可以是 [binary file](../glossary.md#term-binary-file) 。输入编码应当是 UTF-8 ， UTF-16 或者 UTF-32 。

json.loads( _s_ , _*_ , _cls =None_, _object_hook =None_, _parse_float =None_, _parse_int =None_, _parse_constant =None_, _object_pairs_hook =None_, _** kw_)¶
~~~
    

~~~
使用这个 转换表 将 _s_ (一个包含 JSON 文档的 [`str`](stdtypes.md#str "str"), [`bytes`](stdtypes.md#bytes "bytes") 或 [`bytearray`](stdtypes.md#bytearray "bytearray") 实例) 反序列化为 Python 对象。

其他参数的含义与 `load()` 中的相同。

如果反序列化的数据不是有效 JSON 文档，引发 `JSONDecodeError` 错误。

在 3.6 版本发生变更: _s_ 现在可以为 [`bytes`](stdtypes.md#bytes "bytes") 或 [`bytearray`](stdtypes.md#bytearray "bytearray") 类型。 输入编码应为 UTF-8, UTF-16 或 UTF-32。

在 3.9 版本发生变更: 关键字参数 _encoding_ 已被移除。

## 编码器和解码器¶

_class _json.JSONDecoder( _*_ , _object_hook =None_, _parse_float =None_, _parse_int =None_, _parse_constant =None_, _strict =True_, _object_pairs_hook =None_)¶
~~~
    

~~~
简单的JSON解码器。

默认情况下，解码执行以下翻译:

JSON

|

Python  
  
---|---  
  
object -- 对象

|

dict  
  
array

|

list -- 列表  
  
string

|

str  
  
number (int)

|

int  
  
number (real)

|

float  
  
true

|

True  
  
false

|

False  
  
null

|

None  
  
它还将“NaN”、“Infinity”和“-Infinity”理解为它们对应的“float”值，这超出了JSON规范。

如果指定了 _object_hook_ ，它将附带每个已解码 JSON 对象的结果来调用并将被用于替代给定的 [`dict`](stdtypes.md#dict "dict")。 这可被用于提供自定义的反序列化操作（例如支持 [JSON-RPC](https://www.jsonrpc.org) 类提示）。

如果指定了 _object_pairs_hook_ 则它将被调用并传入以对照值有序列表进行解码的每个 JSON 对象的结果。 _object_pairs_hook_ 的结果值将被用来替代 [`dict`](stdtypes.md#dict "dict")。 这一特性可被用于实现自定义解码器。 如果还定义了 _object_hook_ ，则 _object_pairs_hook_ 的优先级更高。

在 3.1 版本发生变更: 添加了对 _object_pairs_hook_ 的支持。

_parse_float_ ，如果指定，将与每个要解码 JSON 浮点数的字符串一同调用。默认状态下，相当于 `float(num_str)` 。可以用于对 JSON 浮点数使用其它数据类型和语法分析程序 （比如 [`decimal.Decimal`](decimal.md#decimal.Decimal "decimal.Decimal") ）。

_parse_int_ ，如果指定，将与每个要解码 JSON 整数的字符串一同调用。默认状态下，相当于 `int(num_str)` 。可以用于对 JSON 整数使用其它数据类型和语法分析程序 （比如 [`float`](functions.md#float "float") ）。

_parse_constant_ ，如果指定，将要与以下字符串中的一个一同调用： `'-Infinity'` ， `'Infinity'` ， `'NaN'` 。如果遇到无效的 JSON 数字则可以使用它引发异常。

如果 _strict_ 为 false （默认为 `True` ），那么控制字符将被允许在字符串内。在此上下文中的控制字符编码在范围 0--31 内的字符，包括 `'\t'` (制表符）， `'\n'` ， `'\r'` 和 `'\0'` 。

如果反序列化的数据不是有效 JSON 文档，引发 `JSONDecodeError` 错误。

在 3.6 版本发生变更: 所有形参现在都是 [仅限关键字参数](../glossary.md#keyword-only-parameter)。

decode( _s_ )¶
~~~
    

~~~
返回 _s_ 的 Python 表示形式（包含一个 JSON 文档的 [`str`](stdtypes.md#str "str") 实例）。

如果给定的 JSON 文档无效则将引发 `JSONDecodeError`。

raw_decode( _s_ )¶
~~~
    

~~~
从 _s_ 中解码出 JSON 文档（以 JSON 文档开头的一个 [`str`](stdtypes.md#str "str") 对象）并返回一个 Python 表示形式为 2 元组以及指明该文档在 _s_ 中结束位置的序号。

这可以用于从一个字符串解码JSON文档，该字符串的末尾可能有无关的数据。

_class _json.JSONEncoder( _*_ , _skipkeys =False_, _ensure_ascii =True_, _check_circular =True_, _allow_nan =True_, _sort_keys =False_, _indent =None_, _separators =None_, _default =None_)¶
~~~
    

~~~
用于Python数据结构的可扩展JSON编码器。

默认支持以下对象和类型：

Python

|

JSON  
  
---|---  
  
dict

|

object -- 对象  
  
list, tuple

|

array  
  
str

|

string  
  
int, float, int 和 float 派生的枚举

|

number  
  
True

|

true  
  
False

|

false  
  
None

|

null  
  
在 3.4 版本发生变更: 添加了对 int 和 float 派生的枚举类的支持

要将其扩展至识别其他对象，需要子类化并实现 `default()`，如果可能还要实现另一个返回 `o` 的可序列化对象的方法，否则它应当调用超类实现 (来引发 [`TypeError`](exceptions.md#TypeError "TypeError"))。

如果 _skipkeys_ 为假值（默认），则当尝试对非 [`str`](stdtypes.md#str "str"), [`int`](functions.md#int "int"), [`float`](functions.md#float "float") 或 `None` 的键进行编码时将会引发 [`TypeError`](exceptions.md#TypeError "TypeError")。 如果 _skipkeys_ 为真值，这些条目将被直接跳过。

如果 _ensure_ascii_ 是 true （即默认值），输出保证将所有输入的非 ASCII 字符转义。如果 _ensure_ascii_ 是 false，这些字符会原样输出。

如果 _check_circular_ 为真值（默认），那么列表、字典和自定义的已编码对象将在编码期间进行循环引用检查以防止无限递归 (无限递归会导致 [`RecursionError`](exceptions.md#RecursionError "RecursionError"))。 在其他情况下，将不会进行这种检查。

如果 _allow_nan_ 为 true （默认），那么 `NaN` ， `Infinity` ，和 `-Infinity` 进行编码。此行为不符合 JSON 规范，但与大多数的基于 Javascript 的编码器和解码器一致。否则，它将是一个 [`ValueError`](exceptions.md#ValueError "ValueError") 来编码这些浮点数。

如果 _sort_keys_ 为 true （默认为： `False` ），那么字典的输出是按照键排序；这对回归测试很有用，以确保可以每天比较 JSON 序列化。

如果 _indent_ 是一个非负整数或者字符串，那么 JSON 数组元素和对象成员会被美化输出为该值指定的缩进等级。 如果缩进等级为零、负数或者 `""`，则只会添加换行符。 `None` (默认值) 选择最紧凑的表达。 使用一个正整数会让每一层缩进同样数量的空格。 如果 _indent_ 是一个字符串 (比如 `"\t"`)，那个字符串会被用于缩进每一层。

在 3.2 版本发生变更: 现允许使用字符串作为 _indent_ 而不再仅仅是整数。

当被指定时， _separators_ 应当是一个 `(item_separator, key_separator)` 元组。当 _indent_ 为 `None` 时，默认值取 `(', ', ': ')`，否则取 `(',', ': ')`。为了得到最紧凑的 JSON 表达式，你应该指定其为 `(',', ':')` 以消除空白字符。

在 3.4 版本发生变更: 现当 _indent_ 不是 `None` 时，采用 `(',', ': ')` 作为默认值。

当 _default_ 被指定时，其应该是一个函数，每当某个对象无法被序列化时它会被调用。它应该返回该对象的一个可以被 JSON 编码的版本或者引发一个 [`TypeError`](exceptions.md#TypeError "TypeError")。如果没有被指定，则会直接引发 [`TypeError`](exceptions.md#TypeError "TypeError")。

在 3.6 版本发生变更: 所有形参现在都是 [仅限关键字参数](../glossary.md#keyword-only-parameter)。

default( _o_ )¶
~~~
    

~~~
在子类中实现这种方法使其返回 _o_ 的可序列化对象，或者调用基础实现（引发 [`TypeError`](exceptions.md#TypeError "TypeError") ）。

例如，要支持任意的迭代器，你可以这样实现 `default()`:
~~~
    
    
~~~python
def default(self, o):
   try:
       iterable = iter(o)
   except TypeError:
       pass
   else:
       return list(iterable)
   # Let the base class default method raise the TypeError
   return json.JSONEncoder.default(self, o)
~~~

encode( _o_ )¶

    

~~~
返回 Python _o_ 数据结构的 JSON 字符串表达方式。例如:
~~~
    
    
~~~shell
>>> json.JSONEncoder().encode({"foo": ["bar", "baz"]})
'{"foo": ["bar", "baz"]}'
~~~

iterencode( _o_ )¶

    

~~~
编码给定对象 _o_ ，并且让每个可用的字符串表达方式。例如:
~~~
    
    
~~~
for chunk in json.JSONEncoder().iterencode(bigobject):
    mysocket.write(chunk)
~~~

## 异常¶

_exception _json.JSONDecodeError( _msg_ , _doc_ , _pos_ )¶

    

~~~
拥有以下附加属性的 [`ValueError`](exceptions.md#ValueError "ValueError") 的子类：

msg¶
~~~
    

~~~
未格式化的错误消息。

doc¶
~~~
    

~~~
正在解析的 JSON 文档。

pos¶
~~~
    

~~~
The start index of _doc_ where parsing failed.

lineno¶
~~~
    

~~~
The line corresponding to _pos_.

colno¶
~~~
    

~~~
The column corresponding to _pos_.

在 3.5 版本加入.

## 标准符合性和互操作性¶

JSON 格式由 [**RFC 7159**](https://datatracker.ietf.org/doc/html/rfc7159.md) 和 [ECMA-404](http://www.ecma-international.org/publications/standards/Ecma-404.htm) 指定。此段落详细讲了这个模块符合 RFC 的级别。简单来说， `JSONEncoder` 和 `JSONDecoder` 子类，和明确提到的参数以外的参数，不作考虑。

此模块不严格遵循于 RFC ，它实现了一些扩展是有效的 Javascript 但不是有效的 JSON。尤其是：

  * 无限和 NaN 数值是被接受并输出；

  * 对象内的重复名称是接受的，并且仅使用最后一对属性-值对的值。

自从 RFC 允许符合 RFC 的语法分析程序接收 不符合 RFC 的输入文本以来，这个模块的解串器在默认状态下默认符合 RFC 。

### 字符编码¶

RFC 要求使用 UTF-8 ， UTF-16 ，或 UTF-32 之一来表示 JSON ，为了最大互通性推荐使用 UTF-8 。

RFC允许，尽管不是必须的，这个模块的序列化默认设置为 _ensure_ascii=True_ ，这样消除输出以便结果字符串至容纳 ASCII 字符。

_ensure_ascii_ 参数以外，此模块是严格的按照在 Python 对象和 [`Unicode strings`](stdtypes.md#str "str") 间的转换定义的，并且因此不能直接解决字符编码的问题。

RFC 禁止添加字符顺序标记（ BOM ）在 JSON 文本的开头，这个模块的序列化器不添加 BOM 标记在它的输出上。 RFC，准许 JSON 反序列化器忽略它们输入中的初始 BOM 标记，但不要求。此模块的反序列化器引发 [`ValueError`](exceptions.md#ValueError "ValueError") 当存在初始 BOM 标记。

RFC 不会明确禁止包含字节序列的 JSON 字符串这不对应有效的 Unicode 字符（比如 不成对的 UTF-16 的替代物），但是它确实指出它们可能会导致互操作性问题。默认下，模块对这样的序列接受和输出（当在原始 [`str`](stdtypes.md#str "str") 存在时）代码点。

### Infinite 和 NaN 数值¶

RFC 不允许 infinite 或者 NaN 数值的表达方式。尽管这样，默认情况下，此模块接受并且输出 `Infinity` ， `-Infinity`，和 `NaN` 好像它们是有效的JSON数字字面值
~~~
    
    
~~~shell
>>> # Neither of these calls raises an exception, but the results are not valid JSON
>>> json.dumps(float('-inf'))
'-Infinity'
>>> json.dumps(float('nan'))
'NaN'
>>> # Same when deserializing
>>> json.loads('-Infinity')
-inf
>>> json.loads('NaN')
nan
~~~

序列化器中， _allow_nan_ 参数可用于替代这个行为。反序列化器中， _parse_constant_ 参数，可用于替代这个行为。

### 对象中的重复名称¶

RFC 具体说明了 在 JSON对象里的名字应该是唯一的，但没有规定如何处理JSON对象中的重复名称。默认下，此模块不引发异常；作为替代，对于给定名它将忽略除姓-值对之外的所有对:

    
    
~~~shell
>>> weird_json = '{"x": 1, "x": 2, "x": 3}'
>>> json.loads(weird_json)
{'x': 3}
~~~

The _object_pairs_hook_ parameter can be used to alter this behavior.

### 顶级非对象，非数组值¶

过时的 [**RFC 4627**](https://datatracker.ietf.org/doc/html/rfc4627.md) 指定的旧版本 JSON 要求 JSON 文本顶级值必须是 JSON 对象或数组（ Python [`dict`](stdtypes.md#dict "dict") 或 [`list`](stdtypes.md#list "list") ），并且不能是 JSON null 值，布尔值，数值或者字符串值。 [**RFC 7159**](https://datatracker.ietf.org/doc/html/rfc7159.md) 移除这个限制，此模块没有并且从未在序列化器和反序列化器中实现这个限制。

无论如何，为了最大化地获取互操作性，你可能希望自己遵守该原则。

### 实现限制¶

一些 JSON 反序列化器的实现应该在以下方面做出限制：

  * 可接受的 JSON 文本大小

  * 嵌套 JSON 对象和数组的最高水平

  * JSON 数字的范围和精度

  * JSON 字符串的内容和最大长度

此模块不强制执行任何上述限制，除了相关的 Python 数据类型本身或者 Python 解释器本身的限制以外。

当序列化为 JSON ，在应用中当心此类限制这可能破坏你的 JSON 。特别是，通常将 JSON 数字反序列化为 IEEE 754 双精度数字，从而受到该表示方式的范围和精度限制。这是特别相关的，当序列化非常大的 Python [`int`](functions.md#int "int") 值时，或者当序列化 "exotic" 数值类型的实例时比如 [`decimal.Decimal`](decimal.md#decimal.Decimal "decimal.Decimal") 。

## CLI¶

**源代码：** [Lib/json/tool.py](https://github.com/python/cpython/tree/3.12/Lib/json/tool.py)

* * *

The `json.tool` module provides a simple command line interface to validate and pretty-print JSON objects.

如果未指定可选的 `infile` 和 `outfile` 参数，则将分别使用 [`sys.stdin`](sys.md#sys.stdin "sys.stdin") 和 [`sys.stdout`](sys.md#sys.stdout "sys.stdout"):

    
    
~~~
$ echo '{"json": "obj"}' | python -m json.tool
{
    "json": "obj"
}
$ echo '{1.2:3.4}' | python -m json.tool
Expecting property name enclosed in double quotes: line 1 column 2 (char 1)
~~~

在 3.5 版本发生变更: 输出现在将与输入顺序保持一致。 请使用 `--sort-keys` 选项来将输出按照键的字母顺序排序。

### 命令行选项¶

infile¶

    

~~~
要被验证或美化打印的 JSON 文件：
~~~
    
    
~~~
$ python -m json.tool mp_films.json
[
    {
        "title": "And Now for Something Completely Different",
        "year": 1971
    },
    {
        "title": "Monty Python and the Holy Grail",
        "year": 1975
    }
]
~~~

如果未指定 _infile_ ，则从 [`sys.stdin`](sys.md#sys.stdin "sys.stdin") 读取。

outfile¶

    

~~~
将 _infile_ 输出写入到给定的 _outfile_ 。 在其他情况下，将写入到 [`sys.stdout`](sys.md#sys.stdout "sys.stdout")。

\--sort-keys¶
~~~
    

~~~
将字典输出按照键的字母顺序排序。

在 3.5 版本加入.

\--no-ensure-ascii¶
~~~
    

~~~
禁用非 ASCII 字符的转义，详情参见 `json.dumps()`。

在 3.9 版本加入.

\--json-lines¶
~~~
    

~~~
将每个输入行解析为单独的 JSON 对象。

在 3.8 版本加入.

\--indent, \--tab, \--no-indent, \--compact¶
~~~
    

~~~
用于空白符控制的互斥选项。

在 3.9 版本加入.

-h, \--help¶
~~~

显示帮助消息。

备注

[1]

正如 [RFC 7159 的勘误表](https://www.rfc-editor.org/errata_search.php?rfc=7159) 所说明的，JSON 允许以字符串表示字面值字符 U+2028 (LINE SEPARATOR) 和 U+2029 (PARAGRAPH SEPARATOR)，而 JavaScript (在 ECMAScript 5.1 版中) 不允许。

