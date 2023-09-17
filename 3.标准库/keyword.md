# `keyword` \--- 检验Python关键字¶

**源码：** [Lib/keyword.py](https://github.com/python/cpython/tree/3.12/Lib/keyword.py)

* * *

此模块使 Python 程序可以确定某个字符串是否为 [关键字](lexical_analysis.md#keywords) 或 [软关键字](lexical_analysis.md#soft-keywords)。

keyword.iskeyword( _s_ )¶

    

~~~
如果 _s_ 是一个 Python [关键字](../reference/lexical_analysis.md#keywords) 则返回 `True`。

keyword.kwlist¶
~~~
    

~~~
包含解释器定义的所有 [关键字](../reference/lexical_analysis.md#keywords) 的序列。 如果所定义的任何关键字仅在特定 [`__future__`](__future__.md#module-__future__ "__future__: Future statement definitions") 语句生效时被激活，它们也将被包含在内。

keyword.issoftkeyword( _s_ )¶
~~~
    

~~~
如果 _s_ 是一个 Python [软关键字](../reference/lexical_analysis.md#soft-keywords) 则返回 `True`。

在 3.9 版本加入.

keyword.softkwlist¶
~~~
    

~~~
