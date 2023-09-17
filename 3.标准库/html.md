# `html` \--- 超文本标记语言支持¶

**源码：** [Lib/html/__init__.py](https://github.com/python/cpython/tree/3.12/Lib/html/__init__.py)

* * *

该模块定义了操作HTML的工具。

html.escape( _s_ , _quote =True_)¶

    

~~~
将字符串 _s_ 中的字符 `&` 、 `<` 和 `>` 转换为安全的HTML序列。 如果需要在 HTML 中显示可能包含此类字符的文本，请使用此选项。 如果可选的标志 _quote_ 为真值，则字符 (`"`) 和 (`'`) 也被转换；这有助于包含在由引号分隔的 HTML 属性中，如 `<a href="...">`。

在 3.2 版本加入.

html.unescape( _s_ )¶
~~~
    

~~~
