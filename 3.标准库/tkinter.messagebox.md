# `tkinter.messagebox` \--- Tkinter 消息提示¶

**源代码:** [Lib/tkinter/messagebox.py](https://github.com/python/cpython/tree/3.12/Lib/tkinter/messagebox.py)

* * *

`tkinter.messagebox` 模块提供了一个模板基类以及多个常用配置的便捷方法。 消息框为模式窗口并将基于用户的选择返回 (True, False, OK, None, Yes, No) 的一个子集。 常用消息框风格和布局包括但不限于:

![../_images/tk_msg.png](../_images/tk_msg.png)

_class _tkinter.messagebox.Message( _master =None_, _** options_)¶

    

~~~
创建一个默认信息消息框。

**信息消息框**

tkinter.messagebox.showinfo( _title =None_, _message =None_, _** options_)¶
~~~
    

~~~
**警告消息框**

tkinter.messagebox.showwarning( _title =None_, _message =None_, _** options_)¶

tkinter.messagebox.showerror( _title =None_, _message =None_, _** options_)¶
~~~
    

~~~
**疑问消息框**

tkinter.messagebox.askquestion( _title =None_, _message =None_, _** options_)¶

tkinter.messagebox.askokcancel( _title =None_, _message =None_, _** options_)¶

tkinter.messagebox.askretrycancel( _title =None_, _message =None_, _** options_)¶

tkinter.messagebox.askyesno( _title =None_, _message =None_, _** options_)¶

tkinter.messagebox.askyesnocancel( _title =None_, _message =None_, _** options_)¶
~~~
    

