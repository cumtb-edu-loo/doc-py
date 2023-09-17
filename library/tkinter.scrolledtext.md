# `tkinter.scrolledtext` \--- 滚动文字控件¶

**源代码：** [Lib/tkinter/scrolledtext.py](https://github.com/python/cpython/tree/3.12/Lib/tkinter/scrolledtext.py)

* * *

`tkinter.scrolledtext` 模块提供了一个同名的类，实现了带有垂直滚动条并被配置为可以“正常运作”的文本控件 。 使用 `ScrolledText` 类会比直接配置一个文本控件附加滚动条要简单得多。

文本控件与滚动条打包在一个 `Frame` 中， `Grid` 方法和 `Pack` 方法的布局管理器从 `Frame` 对象中获得。这允许 `ScrolledText` 控件可以直接用于实现大多数正常的布局管理行为。

如果需要更具体的控制，可以使用以下属性：

_class _tkinter.scrolledtext.ScrolledText( _master =None_, _** kw_)¶

    

~~~
frame¶
~~~
    

~~~
围绕文本和滚动条控件的框架。

vbar¶
~~~
    

~~~
