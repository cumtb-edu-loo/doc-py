# `tkinter.dnd` \--- 拖放操作支持¶

**源代码:** [Lib/tkinter/dnd.py](https://github.com/python/cpython/tree/3.12/Lib/tkinter/dnd.py)

* * *

备注

此模块是实验性的且在为 Tk DND 所替代后将被弃用。

`tkinter.dnd` 模块为单个应用内部的对象提供了在同一窗口中或多个窗口间的拖放操作支持。 要将对象设为可拖放，你必须为其创建启动拖放进程的事件绑定。 通常，你要将 ButtonPress 事件绑定到你所编写的回调函数 (参见 [绑定和事件](tkinter.md#bindings-and-events))。 该函数应当调用 `dnd_start()`，其中 'source' 为要拖动的对象，而 'event' 为发起调用的事件（你的回调函数的参数）。

目标对象的选择方式如下:

  1. 从顶至底地在鼠标之下的区域中搜索目标控件

>   * 目标控件应当具有一个指向可调用对象的 _dnd_accept_ 属性
>
>   * 如果 _dnd_accept_ 不存在或是返回 None，则将转至父控件中搜索
>
>   * 如果目标控件未找到，则目标对象为 None
>
>

  2. 调用 _< old_target>.dnd_leave(source, event)_

  3. 调用 _< new_target>.dnd_enter(source, event)_

  4. 调用 _< target>.dnd_commit(source, event)_ 来通知释放

  5. 调用 _< source>.dnd_end(target, event)_ 来表明拖放的结束

_class _tkinter.dnd.DndHandler( _source_ , _event_ )¶

    

~~~
_DndHandler_ 类处理拖放事件，在事件控件的根对象上跟踪 Motion 和 ButtonRelease 事件。

cancel( _event =None_)¶
~~~
    

~~~
取消拖放进程。

finish( _event_ , _commit =0_)¶
~~~
    

~~~
执行结束播放函数。

on_motion( _event_ )¶
~~~
    

~~~
在执行拖动期间为目标对象检查鼠标之下的区域。

on_release( _event_ )¶
~~~
    

~~~
当释放模式被触发时表明拖动的结束。

tkinter.dnd.dnd_start( _source_ , _event_ )¶
~~~
    

~~~
