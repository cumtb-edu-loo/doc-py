# `tomllib` \--- 解析 TOML 文件¶

在 3.11 版本加入.

**源代码：** [Lib/tomllib](https://github.com/python/cpython/tree/3.12/Lib/tomllib)

* * *

这个模块提供了一个解析 TOML （Tom's Obvious Minimal Language, [https://toml.io](https://toml.io/en/)）接口。该模块不支持写 TOML。

参见

包 [Tomli-W package](https://pypi.org/project/tomli-w/) 是一个 TOML writer，可以与该模块一起使用，其提供了标准库用户熟悉的 [`marshal`](3.标准库/marshal.md#module-marshal "marshal: Convert Python objects to streams of bytes and back \(with different constraints\).") 和 [`pickle`](pickle.md#module-pickle "pickle: Convert Python objects to streams of bytes and back.") 模块相似的写 API。

参见

[TOML Kit 包](https://pypi.org/project/tomlkit/) 是一个兼具读和写功能的保持样式的 TOML 库。推荐使用此模块编辑已经存在的 TOML 文件。

这个模块定义了以下函数：

tomllib.load( _fp_ , _/_ , _*_ , _parse_float =float_)¶

    

~~~
读取一个 TOML 文件。第一个参数应该是一个可读的二进制文件对象。返回 [`dict`](stdtypes.md#dict "dict")。使用 转换表 将 TOML 类型转换为 Python。

对每个要解析的 TOML 浮点数字符串调用 _parse_float_ 。默认情况下，这相当于 `float(num_str)`。这可以用于为 TOML 浮点数使用另一种数据类型或解析器（例如：[`decimal.Decimal`](decimal.md#decimal.Decimal "decimal.Decimal")）。可调用对象不能返回 [`dict`](stdtypes.md#dict "dict") 或 [`list`](stdtypes.md#list "list")，否则将引发 [`ValueError`](exceptions.md#ValueError "ValueError")。

对无效的 TOML 文档将引发 `TOMLDecodeError`。

tomllib.loads( _s_ , _/_ , _*_ , _parse_float =float_)¶
~~~
    

~~~
从 [`str`](stdtypes.md#str "str") 对象中加载 TOML。返回 [`dict`](stdtypes.md#dict "dict")。使用 转换表 将 TOML 类型转换为 Python类型。参数 _parse_float_ 与 `load()` 中的意义相同。

对无效的 TOML 文档将引发 `TOMLDecodeError`。

有以下几种异常：

_exception _tomllib.TOMLDecodeError¶
~~~
    

~~~
[`ValueError`](exceptions.md#ValueError "ValueError") 的子类

## 例子¶

解析 TOML 文件:
~~~
    
    
~~~
import tomllib

with open("pyproject.toml", "rb") as f:
    data = tomllib.load(f)
~~~

解析 TOML 字符串:

    
    
~~~
import tomllib

toml_str = """
python-version = "3.11.0"
python-implementation = "CPython"
"""

data = tomllib.loads(toml_str)
~~~

## 转换表¶

TOML

|

Python  
  
---|---  
  
table

|

dict  
  
string

|

str  
  
integer

|

int  
  
float

|

float（可用 _parse_float_ 配置）  
  
boolean

|

bool  
  
offset date-time

|

datetime.datetime（`tzinfo` 属性设置为 `datetime.timezone` 的实例）  
  
local date-time

|

datetime.datetime （`tzinfo` 属性设置为 `None`）  
  
local date

|

datetime.date  
  
local time

|

datetime.time  
  
array

|

list  
  
