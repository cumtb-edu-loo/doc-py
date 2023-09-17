# `tkinter.font` \--- Tkinter 字体封装¶

**源代码:** [Lib/tkinter/font.py](https://github.com/python/cpython/tree/3.12/Lib/tkinter/font.py)

* * *

`tkinter.font` 模块提供用于创建和使用命名字体的 `Font` 类。

不同的字体粗细和倾斜是：

tkinter.font.NORMAL¶

tkinter.font.BOLD¶

tkinter.font.ITALIC¶

tkinter.font.ROMAN¶

    

~~~
_class _tkinter.font.Font( _root =None_, _font =None_, _name =None_, _exists =False_, _** options_)¶
~~~
    

~~~
`Font` 类表示命名字体。 _Font_ 实例具有唯一的名称，可以通过其族、大小和样式配置进行指定。命名字体是 Tk 将字体创建和标识为单个对象的方法，而不是通过每次出现时的属性来指定字体。

> 参数：
>

>> _font_ \- 字体指示符元组 (family, size, options)

>>

>> _name_ \- 唯一的字体名

>>

>> _exists_ \- 指向现有命名字体（如果有）

>
> 其他关键字选项（如果指定了 _font_ ，则忽略）：
>

>> _family_ \- 字体系列，例如 Courier，Times

>>

>> _size_ \- 字体大小

>>

>> 如果 _size_ 为正数，则解释为以磅为单位的大小。

>>

>> 如果 _size_ 是负数，则将其绝对值

>>

>> 解释为以像素为单位的大小。

>>

>> _weight_ \- 字体强调 (NORMAL, BOLD)（普通，加粗）

>>

>> _slant_ \- ROMAN, ITALIC（正体，斜体）

>>

>> _underline_ \- 字体下划线（0 - 无下划线，1 - 有下划线）

>>

>> _overstrike_ \- 字体删除线（0 - 无删除线，1 - 有删除线）

actual( _option =None_, _displayof =None_)¶
~~~
    

~~~
返回字体的属性。

cget( _option_ )¶
~~~
    

~~~
检索字体的某一个属性值。

config( _** options_)¶
~~~
    

~~~
修改字体的某一个属性值。

copy()¶
~~~
    

~~~
返回当前字体的新实例。

measure( _text_ , _displayof =None_)¶
~~~
    

~~~
返回以当前字体格式化时文本将在指定显示上占用的空间量。如果未指定显示，则假定为主应用程序窗口。

metrics( _* options_, _** kw_)¶
~~~
    

~~~
返回特定字体的数据。选项包括：

_ascent_ \- 基线和最高点之间的距离
~~~
    

~~~
（在该字体中的一个字符可以占用的空间中）

_descent_ \- 基线和最低点之间的距离
~~~
    

~~~
（在该字体中的一个字符可以占用的空间中）

_linespace_ \- 所需最小垂直间距（在两个
~~~
    

~~~
该字体的字符间，使得这两个字符在垂直方向上不重叠）。

_fixed_ \- 如果该字体宽度被固定则为1，否则为0。

tkinter.font.families( _root =None_, _displayof =None_)¶
~~~
    

~~~
返回不同的字体系列。

tkinter.font.names( _root =None_)¶
~~~
    

~~~
返回定义字体的名字。

tkinter.font.nametofont( _name_ , _root =None_)¶
~~~
    

~~~
