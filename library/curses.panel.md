# `curses.panel` \--- curses 的面板栈扩展¶

* * *

面板是具有添加深度功能的窗口，因此它们可以从上至下堆叠为栈，只有显示每个窗口的可见部分会显示出来。 面板可以在栈中被添加、上移或下移，也可以被移除。

## 函数¶

`curses.panel` 模块定义了以下函数:

curses.panel.bottom_panel()¶

    

~~~
返回面板栈中的底部面板。

curses.panel.new_panel( _win_ )¶
~~~
    

~~~
返回一个面板对象，将其与给定的窗口 _win_ 相关联。 请注意你必须显式地保持所返回的面板对象。 如果你不这样做，面板对象会被垃圾回收并从面板栈中被移除。

curses.panel.top_panel()¶
~~~
    

~~~
返回面板栈中的顶部面板。

curses.panel.update_panels()¶
~~~
    

~~~
在面板栈发生改变后更新虚拟屏幕。 这不会调用 [`curses.doupdate()`](curses.md#curses.doupdate "curses.doupdate")，因此你不必自己执行此操作。

## Panel 对象¶

Panel 对象，如上面 `new_panel()` 所返回的对象，是带有栈顺序的多个窗口。 总是会有一个窗口与确定内容的面板相关联，面板方法会负责窗口在面板栈中的深度。

Panel 对象具有以下方法:

Panel.above()¶
~~~
    

~~~
返回当前面板之上的面板。

Panel.below()¶
~~~
    

~~~
返回当前面板之下的面板。

Panel.bottom()¶
~~~
    

~~~
将面板推至栈底部。

Panel.hidden()¶
~~~
    

~~~
如果面板被隐藏（不可见）则返回 `True`，否则返回 `False`。

Panel.hide()¶
~~~
    

~~~
隐藏面板。 这不会删除对象，它只是让窗口在屏幕上不可见。

Panel.move( _y_ , _x_ )¶
~~~
    

~~~
将面板移至屏幕坐标 `(y, x)`。

Panel.replace( _win_ )¶
~~~
    

~~~
将与面板相关联的窗口改为窗口 _win_ 。

Panel.set_userptr( _obj_ )¶
~~~
    

~~~
将面板的用户指向设为 _obj_ 。 这被用来将任意数据与面板相关联，数据可以是任何 Python 对象。

Panel.show()¶
~~~
    

~~~
显示面板（面板可能已被隐藏）。

Panel.top()¶
~~~
    

~~~
将面板推至栈顶部。

Panel.userptr()¶
~~~
    

~~~
返回面板的用户指针。 这可以是任何 Python 对象。

Panel.window()¶
~~~
    

~~~
