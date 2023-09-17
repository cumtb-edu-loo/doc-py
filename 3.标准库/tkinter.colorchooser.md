# `tkinter.colorchooser` \--- 颜色选择对话框¶

**源代码:** [Lib/tkinter/colorchooser.py](https://github.com/python/cpython/tree/3.12/Lib/tkinter/colorchooser.py)

* * *

`tkinter.colorchooser` 模块提供了 `Chooser` 类作为原生颜色选择对话框的接口。 `Chooser` 实现了一个模式颜色选择对话框窗口。 `Chooser` 类继承自 [`Dialog`](dialog.md#tkinter.commondialog.Dialog "tkinter.commondialog.Dialog") 类。

_class _tkinter.colorchooser.Chooser( _master =None_, _** options_)¶

    

~~~
tkinter.colorchooser.askcolor( _color =None_, _** options_)¶
~~~
    

~~~
创建一个颜色选择对话框。 调用此方法将显示相应窗口，等待用户进行选择，并将选择的颜色 (或 `None`) 返回给调用者。

参见

模块 [`tkinter.commondialog`](dialog.md#module-tkinter.commondialog "tkinter.commondialog: Tkinter base class for dialogs \(Tk\)")
~~~
    

~~~
