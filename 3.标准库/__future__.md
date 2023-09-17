# `__future__` \--- Future 语句定义¶

**源代码：** [Lib/__future__.py](https://github.com/python/cpython/tree/3.12/Lib/__future__.py)

* * *

`__future__` 是一个真正的模块，这主要有 3 个原因：

  * 避免混淆已有的分析 import 语句并查找 import 的模块的工具。

  * 确保 [future 语句](simple_stmts.md#future) 在 2.1 之前的版本运行时至少能抛出 runtime 异常（import `__future__` 会失败，因为 2.1 版本之前没有这个模块）。

  * 当引入不兼容的修改时，可以记录其引入的时间以及强制使用的时间。这是一种可执行的文档，并且可以通过 import `__future__` 来做程序性的检查。

`__future__.py` 中的每一条语句都是以下格式的：

    
    
~~~
FeatureName = _Feature(OptionalRelease, MandatoryRelease,
                       CompilerFlag)
~~~

通常 _OptionalRelease_ 要比 _MandatoryRelease_ 小，并且都是和 [`sys.version_info`](3.标准库/sys.md#sys.version_info "sys.version_info") 格式一致的 5 元素元组。

    
    
~~~
(PY_MAJOR_VERSION, # the 2 in 2.1.0a3; an int
 PY_MINOR_VERSION, # the 1; an int
 PY_MICRO_VERSION, # the 0; an int
 PY_RELEASE_LEVEL, # "alpha", "beta", "candidate" or "final"; string
 PY_RELEASE_SERIAL # the 3; an int
)
~~~

_OptionalRelease_ 记录了一个特性首次发布时的 Python 版本。

在 _MandatoryRelases_ 还没有发布时， _MandatoryRelease_ 表示该特性会变成语言的一部分的预测时间。

其他情况下， _MandatoryRelease_ 用来记录这个特性是何时成为语言的一部分的。从该版本往后，使用该特性将不需要 future 语句，不过很多人还是会加上对应的 import。

_MandatoryRelease_ 也可能是 `None`, 表示这个特性已经被撤销。

`_Feature` 类的实例有两个对应的方法，`getOptionalRelease()` 和 `getMandatoryRelease()`。

_CompilerFlag_ 是一个（位）标记，对于动态编译的代码，需要将这个标记作为第四个参数传入内建函数 [`compile()`](functions.md#compile "compile") 中以开启对应的特性。这个标记存储在 `_Feature` 类实例的 `compiler_flag` 属性中。

`__future__` 中不会删除特性的描述。从 Python 2.1 中首次加入以来，通过这种方式引入了以下特性：

特性

|

可选版本

|

强制加入版本

|

效果  
  
---|---|---|---  
  
nested_scopes

|

2.1.0b1

|

2.2

|

[**PEP 227**](https://peps.python.org/pep-0227/): _静态嵌套作用域_  
  
generators

|

2.2.0a1

|

2.3

|

[**PEP 255**](https://peps.python.org/pep-0255/): _简单生成器_  
  
division

|

2.2.0a2

|

3.0

|

[**PEP 238**](https://peps.python.org/pep-0238/): _修改除法运算符_  
  
absolute_import

|

2.5.0a1

|

3.0

|

[**PEP 328**](https://peps.python.org/pep-0328/): _导入：多行与绝对/相对_  
  
with_statement

|

2.5.0a1

|

2.6

|

[**PEP 343**](https://peps.python.org/pep-0343/): * "with" 语句*  
  
print_function

|

2.6.0a2

|

3.0

|

[**PEP 3105**](https://peps.python.org/pep-3105/): _print 改为函数_  
  
unicode_literals

|

2.6.0a2

|

3.0

|

[**PEP 3112**](https://peps.python.org/pep-3112/): _Python 3000 中的字节字面值_  
  
generator_stop

|

3.5.0b1

|

3.7

|

[**PEP 479**](https://peps.python.org/pep-0479/): _在生成器中处理 StopIteration_  
  
annotations

|

3.7.0b1

|

TBD [1]

|

[**PEP 563**](https://peps.python.org/pep-0563/): _Postponed evaluation of annotations_  
  
[1]

先前计划在 Python 3.10 中强制使用 `from __future__ import annotations` ，但Python指导委员会两次决定推迟这一改变（ [Python 3.10 的公告](https://mail.python.org/archives/list/python-dev@python.org/message/CLVXXPQ2T2LQ5MP2Y53VVQFCXYWQJHKZ/) ； [Python 3.11 的公告](https://mail.python.org/archives/list/python-dev@python.org/message/VIZEBX5EYMSYIJNDBF6DMUMZOCWHARSO/) ）。目前还没有做出最终决定。参见 [**PEP 563**](https://peps.python.org/pep-0563/) 和 [**PEP 649**](https://peps.python.org/pep-0649/) 。

参见

[future 语句](simple_stmts.md#future)

    

~~~
