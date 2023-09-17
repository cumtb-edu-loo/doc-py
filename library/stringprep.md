# `stringprep` \--- 因特网字符串预备¶

**源代码:** [Lib/stringprep.py](https://github.com/python/cpython/tree/3.12/Lib/stringprep.py)

* * *

在标识因特网上的事物（例如主机名），经常需要比较这些标识是否（相等）。 这种比较的具体执行可能会取决于应用域的不同，例如是否要区分大小写等等。 有时也可能需要限制允许的标识为仅由“可打印”字符组成。

[**RFC 3454**](https://datatracker.ietf.org/doc/html/rfc3454.md) 定义了在因特网协议中 Unicode 字符串的“预备”过程。 在将字符串连线传输之前，它们会先使用预备过程进行处理，之后它们将具有特定的标准形式。 该 RFC 定义了一系列表格，它们可以被组合为选项配置。 每个配置必须定义所使用的表格，`stringprep` 过程的其他可选项也是配置的组成部分。 `stringprep` 配置的一个例子是 `nameprep`，它被用于国际化域名。

`stringprep` 模块只公开了来自 [**RFC 3454**](https://datatracker.ietf.org/doc/html/rfc3454.md) 的表格。 由于以字典或列表形式表示这些表格将会非常庞大，因此该模块在内部使用 Unicode 字符数据库。 该模块本身的源代码是使用 `mkstringprep.py` 工具生成的。

因此，这些表格以函数而非数据结构的形式公开。 在 RFC 中有两种表格：集合与映射。 对于集合，`stringprep` 提供了“特征函数”，即如果形参是集合的一部分则返回值为 `True` 的函数。 对于映射，它提供了映射函数：它会根据给定的键返回所关联的值。 以下是模块中所有可用函数的列表。

stringprep.in_table_a1( _code_ )¶

    

~~~
确定 _code_ 是否属于 tableA.1 (Unicode 3.2 中的未分配码位)。

stringprep.in_table_b1( _code_ )¶
~~~
    

~~~
确定 _code_ 是否属于 tableB.1 (通常映射为空值)。

stringprep.map_table_b2( _code_ )¶
~~~
    

~~~
返回 _code_ 依据 tableB.2 (配合 NFKC 使用的大小写转换映射) 所映射的值。

stringprep.map_table_b3( _code_ )¶
~~~
    

~~~
返回 _code_ 依据 tableB.3 (不附带正规化的大小写折叠映射) 所映射的值。

stringprep.in_table_c11( _code_ )¶
~~~
    

~~~
确定 _code_ 是否属于 tableC.1.1 (ASCII 空白字符)。

stringprep.in_table_c12( _code_ )¶
~~~
    

~~~
确定 _code_ 是否属于 tableC.1.2 (非 ASCII 空白字符)。

stringprep.in_table_c11_c12( _code_ )¶
~~~
    

~~~
确定 _code_ 是否属于 tableC.1 (空白字符，C.1.1 和 C.1.2 的并集)。

stringprep.in_table_c21( _code_ )¶
~~~
    

~~~
确定 _code_ 是否属于 tableC.2.1 (ASCII 控制字符)。

stringprep.in_table_c22( _code_ )¶
~~~
    

~~~
确定 _code_ 是否属于 tableC.2.2 (非 ASCII 控制字符)。

stringprep.in_table_c21_c22( _code_ )¶
~~~
    

~~~
确定 _code_ 是否属于 tableC.2 (控制字符，C.2.1 和 C.2.2 的并集)。

stringprep.in_table_c3( _code_ )¶
~~~
    

~~~
确定 _code_ 是否属于 tableC.3 (私有使用)。

stringprep.in_table_c4( _code_ )¶
~~~
    

~~~
确定 _code_ 是否属于 tableC.4 (非字符码位)。

stringprep.in_table_c5( _code_ )¶
~~~
    

~~~
确定 _code_ 是否属于 tableC.5 (替代码)。

stringprep.in_table_c6( _code_ )¶
~~~
    

~~~
确定 _code_ 是否属于 tableC.6 (不适用于纯文本)。

stringprep.in_table_c7( _code_ )¶
~~~
    

~~~
确定 _code_ 是否属于 tableC.7 (不适用于规范表示)。

stringprep.in_table_c8( _code_ )¶
~~~
    

~~~
确定 _code_ 是否属于 tableC.8 (改变显示属性或已弃用)。

stringprep.in_table_c9( _code_ )¶
~~~
    

~~~
确定 _code_ 是否属于 tableC.9 (标记字符)。

stringprep.in_table_d1( _code_ )¶
~~~
    

~~~
确定 _code_ 是否属于 tableD.1 (带有双向属性 "R" 或 "AL" 的字符)。

stringprep.in_table_d2( _code_ )¶
~~~
    

~~~
