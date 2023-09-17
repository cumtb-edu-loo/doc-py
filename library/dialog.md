# Tkinter 对话框¶

## `tkinter.simpledialog` \--- 标准 Tkinter 输入对话框¶

**源码:** [Lib/tkinter/simpledialog.py](https://github.com/python/cpython/tree/3.12/Lib/tkinter/simpledialog.py)

* * *

`tkinter.simpledialog` 模块包含了一些便捷类和函数，用于创建简单的模态对话框，从用户那里读取一个值。

tkinter.simpledialog.askfloat( _title_ , _prompt_ , _** kw_)¶

tkinter.simpledialog.askinteger( _title_ , _prompt_ , _** kw_)¶

tkinter.simpledialog.askstring( _title_ , _prompt_ , _** kw_)¶

    

~~~
以上三个函数提供给用户输入期望值的类型的对话框.

_class _tkinter.simpledialog.Dialog( _parent_ , _title =None_)¶
~~~
    

~~~
自定义对话框的基类.

> body( _master_ )¶
>  
>
> 可以覆盖，用于构建对话框的界面，并返回初始焦点所在的部件。
>
> buttonbox()¶
>  
>
> 加入 OK 和 Cancel 按钮的默认行为. 重写自定义按钮布局.

## `tkinter.filedialog` \--- 文件选择对话框.¶

**源码:** [Lib/tkinter/filedialog.py](https://github.com/python/cpython/tree/3.12/Lib/tkinter/filedialog.py)

* * *

`tkinter.filedialog` 模块提供了用于创建文件/目录选择窗口的类和工厂函数。

### 原生的载入/保存对话框.¶

以下类和函数提供了文件对话窗口，这些窗口带有原生外观，具备可定制行为的配置项。这些关键字参数适用于下列类和函数：

> _parent_ —— 对话框下方的窗口
>
> _title_ —— 窗口的标题
>
> _initialdir_ —— 对话框的启动目录
>
> _initialfile_ —— 打开对话框时选中的文件
>
> _filetypes_ —— （标签，匹配模式）元组构成的列表，允许使用 “*” 通配符
>
> _defaultextension_ —— 默认的扩展名，用于加到文件名后面（保存对话框）。
>
> _multiple_ —— 为 True 则允许多选

** 静态工厂函数 **

调用以下函数时，会创建一个模态的、原生外观的对话框，等待用户选取，然后将选中值或 `None` 返回给调用者。

tkinter.filedialog.askopenfile( _mode ='r'_, _** options_)¶

tkinter.filedialog.askopenfiles( _mode ='r'_, _** options_)¶
~~~
    

~~~
上述两个函数创建了 `Open` 对话框，并返回一个只读模式打开的文件对象。

tkinter.filedialog.asksaveasfile( _mode ='w'_, _** options_)¶
~~~
    

~~~
创建 `SaveAs` 对话框并返回一个写入模式打开的文件对象。

tkinter.filedialog.askopenfilename( _** options_)¶

tkinter.filedialog.askopenfilenames( _** options_)¶
~~~
    

~~~
以上两个函数创建了 `Open` 对话框，并返回选中的文件名，对应着已存在的文件。

tkinter.filedialog.asksaveasfilename( _** options_)¶
~~~
    

~~~
创建 `SaveAs` 对话框，并返回选中的文件名。

tkinter.filedialog.askdirectory( _** options_)¶
~~~
    

~~~
提示用户选择一个目录.

其他关键字参数：

_mustexist_ —— 确定是否必须为已存在的目录。

_class _tkinter.filedialog.Open( _master =None_, _** options_)¶

_class _tkinter.filedialog.SaveAs( _master =None_, _** options_)¶
~~~
    

~~~
上述两个类提供了用于保存和加载文件的原生对话窗口。

** 便捷类 **

以下类用于从头开始创建文件/目录窗口。不会模仿当前系统的原生外观。

_class _tkinter.filedialog.Directory( _master =None_, _** options_)¶
~~~
    

~~~
创建对话框，提示用户选择一个目录。

备注

为了实现自定义的事件处理和行为，应继承 _FileDialog_ 类。

_class _tkinter.filedialog.FileDialog( _master_ , _title =None_)¶
~~~
    

~~~
创建一个简单的文件选择对话框。

cancel_command( _event =None_)¶
~~~
    

~~~
触发对话窗口的终止。

dirs_double_event( _event_ )¶
~~~
    

~~~
目录双击事件的处理程序。

dirs_select_event( _event_ )¶
~~~
    

~~~
目录单击事件的处理程序。

files_double_event( _event_ )¶
~~~
    

~~~
文件双击事件的处理程序。

files_select_event( _event_ )¶
~~~
    

~~~
文件单击事件的处理程序。

filter_command( _event =None_)¶
~~~
    

~~~
按目录筛选文件。

get_filter()¶
~~~
    

~~~
获取当前使用的文件筛选器。

get_selection()¶
~~~
    

~~~
获取当前选中项。

go( _dir_or_file =os.curdir_, _pattern ='*'_, _default =''_, _key =None_)¶
~~~
    

~~~
显示对话框并启动事件循环。

ok_event( _event_ )¶
~~~
    

~~~
退出对话框并返回当前选中项。

quit( _how =None_)¶
~~~
    

~~~
退出对话框并返回文件名。

set_filter( _dir_ , _pat_ )¶
~~~
    

~~~
设置文件筛选器。

set_selection( _file_ )¶
~~~
    

~~~
将当前选中文件更新为 _file_ 。

_class _tkinter.filedialog.LoadFileDialog( _master_ , _title =None_)¶
~~~
    

~~~
FileDialog 的一个子类，创建用于选取已有文件的对话窗口。

ok_command()¶
~~~
    

~~~
检测有否给出文件，以及选中的文件是否存在。

_class _tkinter.filedialog.SaveFileDialog( _master_ , _title =None_)¶
~~~
    

~~~
FileDialog 的一个子类，创建用于选择目标文件的对话窗口。

ok_command()¶
~~~
    

~~~
检测选中文件是否为目录。如果选中了已存在文件，则需要用户进行确认。

## `tkinter.commondialog` \--- 对话窗口模板¶

**源码:** ：[Lib/tkinter/commondialog.py](https://github.com/python/cpython/tree/3.12/Lib/tkinter/commondialog.py)

* * *

`tkinter.commondialog` 模块提供了 `Dialog` 类，是其他模块定义的对话框的基类。

_class _tkinter.commondialog.Dialog( _master =None_, _** options_)¶
~~~
    

~~~
show( _color =None_, _** options_)¶
~~~
    

~~~
