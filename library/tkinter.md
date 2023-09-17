# `tkinter` —— Tcl/Tk 的 Python 接口¶

**源代码:** [Lib/tkinter/__init__.py](https://github.com/python/cpython/tree/3.12/Lib/tkinter/__init__.py)

* * *

`tkinter` 包 ("Tk 接口") 是针对 Tcl/Tk GUI 工具包的标准 Python 接口。 Tk 和 `tkinter` 在大多数 Unix 平台，包括 macOS，以及 Windows 系统上均可使用。

若在命令行执行 `python -m tkinter`，应会弹出一个简单的 Tk 界面窗口， 表明 `tkinter` 包已安装完成，还会显示当前安装的 Tcl/Tk 版本，以便阅读对应版本的 Tcl/Tk 文档。

Tkinter 支持众多的 Tcl/Tk 版本，带或不带多线程版本均可。官方的 Python 二进制版本捆绑了 Tcl/Tk 8.6 多线程版本。关于可支持版本的更多信息，请参阅 `_tkinter` 模块的源代码。

Tkinter 并不只是做了简单的封装，而是增加了相当多的代码逻辑，让使用体验更具 Python 风格（pythonic） 。本文将集中介绍这些增加和变化部分，关于未改动部分的细节，请参考 Tcl/Tk 官方文档。

备注

Tcl/Tk 8.5 (2007) 引入了支持主题的现代风格用户界面组件集以及使用这些组件的新版 API。 旧版和新版 API 都可以使用。 你在网上所能找到的大多数文档仍然是使用旧版 API 因此也许已经相当过时。

参见

  * [TkDocs](https://tkdocs.com/)
    

关于使用 Tkinter 创建用户界面的详细教程。 讲解了关键概念，并介绍了使用现代 API 的推荐方式。

  * [Tkinter 8.5 参考手册：一种 Python GUI](https://www.tkdocs.com/shipman/)
    

详细讲解可用的类、方法和选项的 Tkinter 8.5 参考文档。

Tcl/Tk 资源:

  * [Tk 命令](https://www.tcl.tk/man/tcl8.6/TkCmd/contents.htm)
    

有关 Tkinter 所使用的每个底层 Tcl/Tk 命令的完整参考文档。

  * [Tcl/Tk 主页](https://www.tcl.tk)
    

额外的文档，以及 Tcl/Tk 核心开发相关链接。

书籍:

  * [Modern Tkinter for Busy Python Developers](https://tkdocs.com/book.md)
    

Mark Roseman 著。 (ISBN 978-1999149567)

  * [Python and Tkinter Programming](https://www.packtpub.com/product/python-gui-programming-with-tkinter/9781788835886)
    

Alan Moore 著。 (ISBN 978-1788835886)

  * [Programming Python](https://learning-python.com/about-pp4e.md)
    

Mark Lutz 著；对 Tkinter 进行了精彩的讲解。 (ISBN 978-0596158101)

  * [Tcl and the Tk Toolkit (2nd edition)](https://www.amazon.com/exec/obidos/ASIN/032133633X)
    

John Ousterhout ，Tcl/Tk 的创造者，与 Ken Jones 合著；未涉及 Tkinter。 (ISBN 978-0321336330)

## 架构¶

Tcl/Tk 不是只有单个库，而是由几个不同的模块组成的，每个模块都有各自的功能和各自的官方文档。 Python 的二进制发行版还会再附加一个模块。

Tcl

    

~~~
Tcl 是一种动态解释型编程语言，正如 Python 一样。尽管它可作为一种通用的编程语言单独使用，但最常见的用法还是作为脚本引擎或 Tk 工具包的接口嵌入到 C 程序中。Tcl 库有一个 C 接口，用于创建和管理一个或多个 Tcl 解释器实例，并在这些实例中运行 Tcl 命令和脚本，添加用 Tcl 或 C 语言实现的自定义命令。每个解释器都拥有一个事件队列，某些部件可向解释器发送事件交由其处理。与 Python 不同，Tcl 的执行模型是围绕协同多任务而设计的，Tkinter 协调了两者的差别（详见 Threading model ）。

Tk
~~~
    

~~~
Tk 是一个用 C 语言实现的 [Tcl 包](https://wiki.tcl-lang.org/37432)，它添加了用于创建和操纵 GUI 部件的自定义命令。 每个 `Tk` 对象都嵌入了自己的 Tcl 解释器实例并将 Tk 加载到其中。 Tk 的部件是高度可定制的，但其代价则是过时的外观。 Tk 使用 Tcl 的事件队列来生成并处理 GUI 事件。

Ttk
~~~
    

~~~
带有主题的 Tk（Ttk）是较新加入的 Tk 部件，相比很多经典的 Tk 部件，在各平台提供的界面更加美观。自 Tk 8.5 版本开始，Ttk 作为 Tk 的成员进行发布。Python 则捆绑在一个单独的模块中， [`tkinter.ttk`](tkinter.ttk.md#module-tkinter.ttk "tkinter.ttk: Tk themed widget set")。

在内部，Tk 和 Ttk 使用下层操作系统的工具库，例如在 Unix/X11 上是 Xlib，在 macOS 上是 Cocoa，在 Windows 上是 GDI。

当你的 Python 应用程序使用 Tkinter 中的某个类，例如创建一个部件时，`tkinter` 模块将首先生成一个 Tcl/Tk 命令字符串。 它会把这个 Tcl 命令字符串传给内部的 `_tkinter` 二进制模块，后者将随后调用 Tcl 解释器来对其求值。 Tcl 解释器随后将对 Tk 和/和 Ttk 包发起调用，它们又将继续对 Xlib, Cocoa 或 GDI 发起调用。

## Tkinter 模块¶

对 Tkinter 的支持分布在多个模块中。 大多数应用程序将需要主模块 `tkinter`，以及 [`tkinter.ttk`](tkinter.ttk.md#module-tkinter.ttk "tkinter.ttk: Tk themed widget set") 模块，后者提供了带主题的现代部件集及相应的 API:
~~~
    
    
~~~
from tkinter import *
from tkinter import ttk
~~~

_class _tkinter.Tk( _screenName =None_, _baseName =None_, _className ='Tk'_, _useTk =True_, _sync =False_, _use =None_)¶

    

~~~
构造一个最高层级的 Tk 部件，这通常是一个应用程序的主窗口，并为这个部件初始化 Tcl 解释器。 每个实例都有其各自所关联的 Tcl 解释器。

`Tk` 类通常全部使用默认值来初始化。 不过，目前还可识别下列关键字参数:

_screenName_
~~~
    

~~~
当（作为字符串）给出时，设置 `DISPLAY` 环境变量。 （仅限 X11）

_baseName_
~~~
    

~~~
预置文件的名称。 在默认情况下， _baseName_ 是来自于程序名称 (`sys.argv[0]`)。

_className_
~~~
    

~~~
控件类的名称。 会被用作预置文件同时也作为 Tcl 发起调用的名称 ( _interp_ 中的 _argv0_ )。

_useTk_
~~~
    

~~~
如果为 `True`，则初始化 Tk 子系统。 `tkinter.Tcl()` 函数会将其设为 `False`。

_sync_
~~~
    

~~~
如果为 `True`，则同步执行所有 X 服务器命令，以便立即报告错误。 可被用于调试。 （仅限 X11）

_use_
~~~
    

~~~
指定嵌入应用程序的窗口 _id_ ，而不是将其创建为独立的顶层窗口。 _id_ 必须以与顶层控件的 -use 选项值相同的方式来指定（也就是说，它具有与 `winfo_id()` 的返回值相同的形式）。

请注意在某些平台上只有当 _id_ 是指向一个启用了 -container 选项的 Tk 框架或顶层窗口时此参数才能正确生效。

`Tk` 读取并解释预置文件，其名称为 `. _className_.tcl` 和 `. _baseName_.tcl`，进入 Tcl 解释器并基于 `. _className_.py` 和 `. _baseName_.py` 的内容来调用 [`exec()`](functions.md#exec "exec")。 预置文件的路径为 `HOME` 环境变量，或者如果它未被定义，则为 [`os.curdir`](os.md#os.curdir "os.curdir")。

tk¶
~~~
    

~~~
通过实例化 `Tk` 创建的 Tk 应用程序对象。 这提供了对 Tcl 解释器的访问。 每个被附加到相同 `Tk` 实例的控件都具有相同的 `tk` 属性值。

master¶
~~~
    

~~~
包含此控件的控件对象。 对于 `Tk`， _master_ 将为 [`None`](constants.md#None "None") 因为它是主窗口。 术语 _master_ 和 _parent_ 是类似的且有时作为参数名称被交替使用；但是，调用 `winfo_parent()` 将返回控件名称字符串而 `master` 将返回控件对象。 _parent_ / _child_ 反映了树型关系而 _master_ / _slave_ 反映了容器结构。

children¶
~~~
    

~~~
以 [`dict`](stdtypes.md#dict "dict") 表示的此控件的直接下级其中的键为子控件名称而值为子实例对象。

tkinter.Tcl( _screenName =None_, _baseName =None_, _className ='Tk'_, _useTk =False_)¶
~~~
    

~~~
`Tcl()` 函数是一个工厂函数，它创建的对象类似于 `Tk` 类创建的，只是不会初始化 Tk 子系统。这在调动 Tcl 解释器时最为有用，这时不想创建多余的顶层窗口，或者无法创建（比如不带 X 服务的 Unix/Linux 系统）。由 `Tcl()` 创建的对象可调用 `loadtk()` 方法创建一个顶层窗口（且会初始化 Tk 子系统）。

提供 Tk 支持的模块包括:

`tkinter`
~~~
    

~~~
主 Tkinter 模块。

[`tkinter.colorchooser`](tkinter.colorchooser.md#module-tkinter.colorchooser "tkinter.colorchooser: Color choosing dialog \(Tk\)")
~~~
    

~~~
让用户选择颜色的对话框。

[`tkinter.commondialog`](dialog.md#module-tkinter.commondialog "tkinter.commondialog: Tkinter base class for dialogs \(Tk\)")
~~~
    

~~~
本文其他模块定义的对话框的基类。

[`tkinter.filedialog`](dialog.md#module-tkinter.filedialog "tkinter.filedialog: Dialog classes for file selection \(Tk\)")
~~~
    

~~~
允许用户指定文件的通用对话框，用于打开或保存文件。

[`tkinter.font`](tkinter.font.md#module-tkinter.font "tkinter.font: Tkinter font-wrapping class \(Tk\)")
~~~
    

~~~
帮助操作字体的工具。

[`tkinter.messagebox`](tkinter.messagebox.md#module-tkinter.messagebox "tkinter.messagebox: Various types of alert dialogs \(Tk\)")
~~~
    

~~~
访问标准的 Tk 对话框。

[`tkinter.scrolledtext`](tkinter.scrolledtext.md#module-tkinter.scrolledtext "tkinter.scrolledtext: Text widget with a vertical scroll bar. \(Tk\)")
~~~
    

~~~
内置纵向滚动条的文本组件。

[`tkinter.simpledialog`](dialog.md#module-tkinter.simpledialog "tkinter.simpledialog: Simple dialog windows \(Tk\)")
~~~
    

~~~
基础对话框和一些便捷功能。

[`tkinter.ttk`](tkinter.ttk.md#module-tkinter.ttk "tkinter.ttk: Tk themed widget set")
~~~
    

~~~
在 Tk 8.5 中引入的带主题的控件集，提供了对应于 `tkinter` 模块中许多经典控件的现代替代。

附加模块:

`_tkinter`
~~~
    

~~~
一个包含低层级 Tcl/Tk 接口的二进制模块。 它会被主 `tkinter` 模块自动导入，且永远不应被应用程序员所直接使用。 它通常是一个共享库（或 DLL），但在某些情况下可能被动态链接到 Python 解释器。

[`idlelib`](idle.md#module-idlelib "idlelib: Implementation package for the IDLE shell/editor.")
~~~
    

~~~
Python 的集成开发与学习环境（IDLE）。 基于 `tkinter`。

`tkinter.constants`
~~~
    

~~~
当向 Tkinter 调用传入各种形参时可被用来代替字符串的符号常量。 由主 `tkinter` 模块自动导入。

[`tkinter.dnd`](tkinter.dnd.md#module-tkinter.dnd "tkinter.dnd: Tkinter drag-and-drop interface \(Tk\)")
~~~
    

~~~
针对 `tkinter` 的（实验性的）拖放支持。 当以 Tk DND 代替时它将会被弃用。

[`turtle`](turtle.md#module-turtle "turtle: An educational framework for simple graphics applications")
~~~
    

~~~
Tk 窗口中的海龟绘图库。

## Tkinter 拾遗¶

这一章节的设计目的不是要编写有关 Tk 或 Tkinter 的冗长教程。 要获取教程，请参阅之前列出的外部资源之一。 相反地，这一章节提供了对于 Tkinter 应用程序大致样貌的快速指导，列出了基本的 Tk 概念，并解释了 Tkinter 包装器的构造是什么样的。

这一章节的剩余部分将帮助你识别在你的 Tkinter 应用程序中需要的类、方法和选项，以及在哪里可以找到有关它们的更详细文档，包括官方 Tcl/Tk 参考手册等。

### Hello World 程序¶

让我们先来看一个 Tkinter 的 "Hello World" 应用程序。 这并不是我们所能写出的最简短版本，但也足够说明你所需要了解的一些关键概念。
~~~
    
    
~~~
from tkinter import *
from tkinter import ttk
root = Tk()
frm = ttk.Frame(root, padding=10)
frm.grid()
ttk.Label(frm, text="Hello World!").grid(column=0, row=0)
ttk.Button(frm, text="Quit", command=root.destroy).grid(column=1, row=0)
root.mainloop()
~~~

在导入语句之后，下一行语句创建了一个 `Tk` 类的实例，它会初始化 Tk 并创建与其关联的 Tcl 解释器。 它还会创建一个顶层窗口，名为 root 窗口，它将被作为应用程序的主窗口。

下一行创建了一个框架控件，在本示例中它会包含我们即将创建的一个标签和一个按钮。 框架被嵌在 root 窗口内部。

下一行创建了一个包含静态文本字符串的标签控件。 `grid()` 方法被用来指明标签在包含它的框架控件中的相对布局（定位），作用类似于 HTML 中的表格。

接下来创建了一个按钮控件，并被放置到标签的右侧。 当被按下时，它将调用 root 窗口的 `destroy()` 方法。

最后，`mainloop()` 方法将所有控件显示出来，并响应用户输入直到程序终结。

### 重要的 Tk 概念¶

即便是这样简单的程序也阐明了以下关键 Tk 概念:

控件

    

~~~
Tkinter 用户界面是由一个个 _控件_ 组成的。 每个控件都由相应的 Python 对象表示，由 `ttk.Frame`, `ttk.Label` 以及 `ttk.Button` 这样的类来实例化。

控件层级结构
~~~
    

~~~
控件按 _层级结构_ 来组织。 标签和按钮包含在框架中，框架又包含在根窗口中。 当创建每个 _子_ 控件时，它的 _父_ 控件会作为控件构造器的第一个参数被传入。

配置选项
~~~
    

~~~
控件具有 _配置选项_ ，配置选项会改变控件的外观和行为，例如要在标签或按钮中显示的文本。 不同的控件类会具有不同的选项集。

几何管理
~~~
    

~~~
小部件在创建时不会自动添加到用户界面。一个像 `grid` 的 _几何管理器_ 控制这些小部件在用户界面的位置。

事件循环
~~~
    

~~~
只有主动运行一个 _事件循环_ ，Tkinter 才会对用户的输入做出反应，改变你的程序，以及刷新显示。如果你的程序没有运行事件循环，你的用户界面不会更新。

### 了解 Tkinter 如何封装 Tcl/Tk¶

当你的应用程序使用 Tkinter 的类和方法时，Tkinter 内部汇编代表 Tcl/Tk 命令的字符串，并在连接到你的应用程序的 `Tk` 实例的 Tcl 解释器中执行这些命令。

无论是试图浏览参考文档，或是试图找到正确的方法或选项，调整一些现有的代码，亦或是调试 Tkinter 应用程序，有时候理解底层 Tcl/Tk 命令是什么样子的会很有用。

为了说明这一点，下面是 Tcl/Tk 等价于上面 Tkinter 脚本的主要部分。
~~~
    
    
~~~
ttk::frame .frm -padding 10
grid .frm
grid [ttk::label .frm.lbl -text "Hello World!"] -column 0 -row 0
grid [ttk::button .frm.btn -text "Quit" -command "destroy ."] -column 1 -row 0
~~~

Tcl 的语法类似于许多 shell 语言，其中第一个单词是要执行的命令，后面是该命令的参数，用空格分隔。不谈太多细节，请注意以下几点：

  * 用于创建窗口小部件（如 `ttk::frame`）的命令对应于 Tkinter 中的 widget 类。

  * Tcl 窗口控件选项（如 `-tex`）对应于 Tkinter 中的关键字参数。

  * 在 Tcl 中，小部件是通过 _路径名_ 引用的（例如 `.frm.btn`），而 Tkinter 不使用名称，而是使用对象引用。

  * 控件在控件层次结构中的位置在其（层次结构）路径名中编码，该路径名使用一个 `.` （点）作为路径分隔符。根窗口的路径名是 `.` （点）。在 Tkinter 中，层次结构不是通过路径名定义的，而是通过在创建每个子控件时指定父控件来定义的。

  * 在 Tcl 中以独立的 _命令_ 实现的操作（比如 `grid` 和 `destroy` ）在 Tkinter 控件对象上以 _方法_ 表示。稍后您将看到，在其他时候，Tcl 在控件对象调用的方法，在 Tkinter 也有对应的使用。

### 我该如何...？这个选项会做...？¶

如果您不确定如何在 Tkinter 中做一些事情，并且您不能立即在您正在使用的教程或参考文档中找到它，这里有一些策略可以帮助您。

首先，请记住，在不同版本的 Tkinter 和 Tcl/Tk 中，各个控件如何工作的细节可能会有所不同。如果您正在搜索文档，请确保它与安装在系统上的 Python 和 Tcl/Tk 版本相对应。

在搜索如何使用 API 时，知道正在使用的类、选项或方法的确切名称会有所帮助。内省，无论是在交互式 Python shell 中，还是在 [`print()`](functions.md#print "print") 中，都可以帮助你确定你需要什么。

要找出控件上可用的配置选项，请调用其 `configure()` 方法，该方法返回一个字典，其中包含每个对象的各种信息，包括其默认值和当前值。使用 `keys()` 获取每个选项的名称。

    
    
~~~
btn = ttk.Button(frm, ...)
print(btn.configure().keys())
~~~

由于大多数控件都有许多共同的配置选项，因此找出特定于特定控件类的配置选项可能会很有用。将选项列表与更简单的控件（如框架）的列表进行比较是一种方法。

    
    
~~~
print(set(btn.configure().keys()) - set(frm.configure().keys()))
~~~

类似地，你可以使用标准函数 [`dir()`](functions.md#dir "dir") 来查找控件对象的可用方法。如果您尝试一下，您会发现有超过200种常见的控件方法，因此再次确认那些特定于控件类的方法是有帮助的。

    
    
~~~
print(dir(btn))
print(set(dir(btn)) - set(dir(frm)))
~~~

### 浏览 Tcl/Tk 参考手册¶

如上所述，官方的 [Tk commands](https://www.tcl.tk/man/tcl8.6/TkCmd/contents.htm) 参考手册（手册页）通常有对控件特定操作的最准确描述。即使您知道需要的选项或方法的名称，您可能仍然有一些地方可以查找。

虽然 Tkinter 中的所有操作都是通过对控件对象的方法调用来实现的，但您已经看到许多 Tcl/Tk 操作都是以命令的形式出现的，这些命令以小部件的路径名作为它的第一个参数，然后是可选参数，例如：

    
    
~~~
destroy .
grid .frm.btn -column 0 -row 0
~~~

但是，其他方法看起来更像在控件对象上调用的方法（实际上，当您在 Tcl/Tk 中创建小部件时，它会使用控件路径名创建 Tcl 命令，该命令的第一个参数是要调用的方法名）。

    
    
~~~
.frm.btn invoke
.frm.lbl configure -text "Goodbye"
~~~

在 Tcl/Tk 官方参考文档中，你会发现手册页上大多数操作看起来都像是特定控件的的方法调用（例如，你会在 [ttk::button](https://www.tcl.tk/man/tcl8.6/TkCmd/ttk_button.htm) 手册页上找到 `invoke()` 方法），而以控件作为参数的函数通常有自己的手册页（例如，[grid](https://www.tcl.tk/man/tcl8.6/TkCmd/grid.htm) ）。

您将在 [options](https://www.tcl.tk/man/tcl8.6/TkCmd/options.htm) 或 [ttk::widget](https://www.tcl.tk/man/tcl8.6/TkCmd/ttk_widget.htm) 手册页中找到许多常见的选项和方法，而其他的选项和方法可以在特定控件类的手册页中找到。

你还会发现许多 Tkinter 方法有复合名称，例如 `winfo_x()`，`winfo_height()`，`winfo_viewable()`。你可以在 [winfo](https://www.tcl.tk/man/tcl8.6/TkCmd/winfo.htm) 页面找到这些文档。

备注

有些令人困惑的是，所有 Tkinter 小部件上还有一些方法实际上并不在控件上操作，而是在全局范围内操作，独立于任何控件。例如访问剪贴板或系统响铃的方法。（它们恰好被实现为所有 Tkinter 小部件都继承自的基类 `Widget` 中的方法）。

## 线程模型¶

Python 和 Tcl/Tk 的线程模型大不相同，而 `tkinter` 则会试图进行调和。若要用到线程，可能需要注意这一点。

一个 Python 解释器可能会关联很多线程。在 Tcl 中，可以创建多个线程，但每个线程都关联了单独的 Tcl 解释器实例。线程也可以创建一个以上的解释器实例，尽管每个解释器实例只能由创建它的那个线程使用。

Each `Tk` object created by `tkinter` contains a Tcl interpreter. It also keeps track of which thread created that interpreter. Calls to `tkinter` can be made from any Python thread. Internally, if a call comes from a thread other than the one that created the `Tk` object, an event is posted to the interpreter's event queue, and when executed, the result is returned to the calling Python thread.

Tcl/Tk 应用程序通常是事件驱动的，这意味着在完成初始化以后，解释器会运行一个事件循环（即 `Tk.mainloop()`）并对事件做出响应。因为它是单线程的，所以事件处理程序必须快速响应，否则会阻塞其他事件的处理。为了避免阻塞，不应在事件处理程序中执行任何耗时很久的计算，而应利用计时器将任务分块，或者在其他线程中运行。而其他很多工具包的 GUI 是在一个完全独立的线程中运行的，独立于包括事件处理程序在内的所有代码。

如果 Tcl 解释器没有运行事件循环并处理解释器事件，则除运行 Tcl 解释器的线程外，任何其他线程发起的 `tkinter` 调用都会失败。

存在一些特殊情况：

>   * Tcl/Tk 库可编译为不支持多线程的版本。这时 `tkinter` 会从初始 Python 线程调用底层库，即便那不是创建 Tcl 解释器的线程。会有一个全局锁来确保每次只会发生一次调用。
>
>   * 虽然 `tkinter` 允许创建一个以上的 `Tk` 实例（都带有自己的解释器），但所有属于同一线程的解释器均会共享同一个事件队列，这样很快就会一团糟。在实际编程时，一次创建的 `Tk` 实例不要超过一个。否则最好在不同的线程中创建，并确保运行的是支持多线程的 Tcl/Tk 版本。
>
>   * 为了防止 Tcl 解释器重新进入事件循环，阻塞事件处理程序并不是唯一的做法。甚至可以运行多个嵌套的事件循环，或者完全放弃事件循环。如果在处理事件或线程时碰到棘手的问题，请小心这些可能的事情。
>
>   * 有几个 `tkinter` 函数，目前只在创建 Tcl 解释器的线程中调用才行。
>
>

## 快速参考¶

### 可选配置项¶

配置参数可以控制组件颜色和边框宽度等。可通过三种方式进行设置：

在对象创建时，使用关键字参数

    
    
    
~~~
fred = Button(self, fg="red", bg="blue")
~~~

在对象创建后，将参数名用作字典索引

    
    
    
~~~
fred["fg"] = "red"
fred["bg"] = "blue"
~~~

利用 config() 方法修改对象的多个属性

    
    
    
~~~
fred.config(fg="red", bg="blue")
~~~

关于这些参数及其表现的完整解释，请参阅 Tk 手册中有关组件的 man 帮助页。

请注意，man 手册页列出了每个部件的“标准选项”和“组件特有选项”。前者是很多组件通用的选项列表，后者是该组件特有的选项。标准选项在 _[options(3)](https://manpages.debian.org/options\(3\))_ man 手册中有文档。

本文没有区分标准选项和部件特有选项。有些选项不适用于某类组件。组件是否对某选项做出响应，取决于组件的类别；按钮组件有一个 `command` 选项，而标签组件就没有。

组件支持的选项在其手册中有列出，也可在运行时调用 `config()` 方法（不带参数）查看，或者通过调用组件的 `keys()` 方法进行查询。这些调用的返回值为字典，字典的键是字符串格式的选项名（比如 `'relief'`），字典的值为五元组。

有些选项，比如 `bg` 是全名通用选项的同义词（`bg` 是 “background”的简写）。向 `config()` 方法传入选项的简称将返回一个二元组，而不是五元组。传回的二元组将包含同义词的全名和“真正的”选项（比如 `('bg', 'background')`）。

索引

|

含意

|

示例  
  
---|---|---  
  
0

|

选项名称

|

`'relief'`  
  
1

|

数据库查找的选项名称

|

`'relief'`  
  
2

|

数据库查找的选项类

|

`'Relief'`  
  
3

|

默认值

|

`'raised'`  
  
4

|

当前值

|

`'groove'`  
  
示例:

    
    
~~~shell
>>> print(fred.config())
{'relief': ('relief', 'relief', 'Relief', 'raised', 'groove')}
~~~

当然，输出的字典将包含所有可用选项及其值。这里只是举个例子。

### 包装器¶

包装器是 Tk 的形状管理机制之一。 形状（geometry ）管理器用于指定多个部件在容器（共同的 _主_ 组件）内的相对位置。与更为麻烦的 _定位器_ 相比（不太常用，这里不做介绍），包装器可接受定性的相对关系—— _上面_ 、 _左边_ 、 _填充_ 等，并确定精确的位置坐标。

_主_ 部件的大小都由其内部的 “从属部件” 的大小决定。包装器用于控制从属部件在主部件中出现的位置。可以把部件包入框架，再把框架包入其他框架中，搭建出所需的布局。此外，只要完成了包装，组件的布局就会进行动态调整，以适应布局参数的变化。

请注意，只有用形状管理器指定几何形状后，部件才会显示出来。忘记设置形状参数是新手常犯的错误，惊讶于创建完部件却啥都没出现。部件只有在应用了类似于打包器的 `pack()` 方法之后才会显示在屏幕上。

调用 pack() 方法时可以给出由关键字/参数值组成的键值对，以便控制组件在其容器中出现的位置，以及主程序窗口大小变动时的行为。下面是一些例子：

    
    
~~~
fred.pack()                     # defaults to side = "top"
fred.pack(side="left")
fred.pack(expand=1)
~~~

### 包装器的参数¶

关于包装器及其可接受的参数，更多信息请参阅 man 手册和 John Ousterhout 书中的第 183 页。

anchor

    

~~~
anchor 类型。 表示包装器要放置的每个从属组件的位置。

expand
~~~
    

~~~
布尔型，`0` 或 `1` 。

fill
~~~
    

~~~
合法值为：`'x'` 、`'y'` 、`'both'` 、`'none'`。

ipadx 和 ipady
~~~
    

~~~
距离值，指定从属部件的内边距。

padx 和 pady
~~~
    

~~~
距离值，指定从属部件的外边距。

side
~~~
    

~~~
合法值为：`'left'`、 `'right'` 、 `'top'`、 `'bottom'`。

### 部件与变量的关联¶

通过一些特定参数，某些组件（如文本输入组件）的当前设置可直接与应用程序的变量关联。这些参数包括 `variable` 、 `textvariable` 、 `onvalue` 、 `offvalue` 、 `value`。这种关联是双向的：只要这些变量因任何原因发生变化，其关联的部件就会更新以反映新的参数值。

不幸的是，在目前 `tkinter` 的实现代码中，不可能通过 `variable` 或 `textvariable` 参数将任意 Python 变量移交给组件。变量只有是 `tkinter` 中定义的 Variable 类的子类，才能生效。

已经定义了很多有用的 Variable 子类： `StringVar` 、 `IntVar` 、`DoubleVar` 和 `BooleanVar`。调用 `get()` 方法可以读取这些变量的当前值；调用 `set()` 方法则可改变变量值。只要遵循这种用法，组件就会保持跟踪变量的值，而不需要更多的干预。

例如：
~~~
    
    
~~~
import tkinter as tk

class App(tk.Frame):
    def __init__(self, master):
        super().__init__(master)
        self.pack()

        self.entrythingy = tk.Entry()
        self.entrythingy.pack()

        # Create the application variable.
        self.contents = tk.StringVar()
        # Set it to some value.
        self.contents.set("this is a variable")
        # Tell the entry widget to watch this variable.
        self.entrythingy["textvariable"] = self.contents

        # Define a callback for when the user hits return.
        # It prints the current value of the variable.
        self.entrythingy.bind('<Key-Return>',
                             self.print_contents)

    def print_contents(self, event):
        print("Hi. The current entry content is:",
              self.contents.get())

root = tk.Tk()
myapp = App(root)
myapp.mainloop()
~~~

### 窗口管理器¶

Tk 有个实用命令 `wm`，用于与窗口管理器进行交互。`wm` 命令的参数可用于控制标题、位置、图标之类的东西。在 `tkinter` 中，这些命令已被实现为 `Wm` 类的方法。顶层部件是 `Wm` 类的子类，所以可以直接调用 `Wm` 的这些方法。

要获得指定部件所在的顶层窗口，通常只要引用该部件的主窗口即可。当然，如果该部件是包装在框架内的，那么主窗口不代表就是顶层窗口。为了获得任意组件所在的顶层窗口，可以调用 `_root()` 方法。该方法以下划线开头，表明其为 Python 实现的代码，而非 Tk 提供的某个接口。

以下是一些典型用法：

    
    
~~~
import tkinter as tk

class App(tk.Frame):
    def __init__(self, master=None):
        super().__init__(master)
        self.pack()

# create the application
myapp = App()

#
# here are method calls to the window manager class
#
myapp.master.title("My Do-Nothing Application")
myapp.master.maxsize(1000, 400)

# start the program
myapp.mainloop()
~~~

### Tk 参数的数据类型¶

anchor

    

~~~
合法值是罗盘的方位点：`"n"` 、`"ne"` 、`"e"` 、`"se"` 、`"s"` 、`"sw"` 、`"w"` 、`"nw"` 和 `"center"` 。

bitmap
~~~
    

~~~
内置已命名的位图有八个：`'error'`、 `'gray25'` 、`'gray50'` 、`'hourglass'`、 `'info'` 、`'questhead'` 、`'question'` 、`'warning'` 。若要指定位图的文件名，请给出完整路径，前面加一个 `@`，比如 `"@/usr/contrib/bitmap/gumby.bit"`。

boolean
~~~
    

~~~
可以传入整数 0 或 1，或是字符串 `"yes"` 或 `"no"`。

callback -- 回调
~~~
    

~~~
指任何无需调用参数的 Python 函数。 例如：
~~~
    
    
~~~python
def print_it():
    print("hi there")
fred["command"] = print_it
~~~

color

    

~~~
可在 rgb.txt 文件中以颜色名的形式给出，或是 RGB 字符串的形式，4 位 ：`"#RGB"` ，8 位 ：`"#RRGGBB"`，12 位：`"#RRRGGGBBB"`，16 位：`"#RRRRGGGGBBBB"`，其中R、G、B 为合法的十六进制数值。 详见 Ousterhout 书中的第 160 页。

cursor
~~~
    

~~~
可采用 `cursorfont.h` 中的标准光标名称，去掉 `XC_` 前缀。 比如要获取一个手形光标（`XC_hand2`），可以用字符串 `"hand2"`。也可以指定自己的位图和掩码文件作为光标。参见 Ousterhout 书中的第 179 页。

distance
~~~
    

~~~
屏幕距离可以用像素或绝对距离来指定。像素是数字，绝对距离是字符串，后面的字符表示单位：`c` 是厘米，`i` 是英寸，`m` 是毫米，`p` 则表示打印机的点数。例如，3.5 英寸可表示为 `"3.5i"`。

font
~~~
    

~~~
Tk 采用一串名称的格式表示字体，例如 `{courier 10 bold}`。正数的字体大小以点为单位，负数的大小以像素为单位。

geometry
~~~
    

~~~
这是一个 `widthxheight` 形式的字符串，其中宽度和高度对于大多数部件来说是以像素为单位的（对于显示文本的部件来说是以字符为单位的）。例如：fred["geometry"] = "200x100"。

justify
~~~
    

~~~
合法的值为字符串： `"left"` 、 `"center"` 、 `"right"` 和 `"fill"` 。

region
~~~
    

~~~
这是包含四个元素的字符串，以空格分隔，每个元素是表示一个合法的距离值（见上文）。例如：`"2 3 4 5"` 、 `"3i 2i 4.5i 2i"` 和 `"3c 2c 4c 10.43c"` 都是合法的区域值。

relief
~~~
    

~~~
决定了组件的边框样式。 合法值包括：`"raised"`、 `"sunken"` 、`"flat"` 、`"groove"` 和 `"ridge"` 。

scrollcommand
~~~
    

~~~
这几乎就是带滚动条部件的 `set()` 方法，但也可是任一只有一个参数的部件方法。

wrap
~~~
    

~~~
只能是以下值之一：`"none"` 、 `"char"` 、 `"word"`。

### 绑定和事件¶

部件命令中的 bind 方法可觉察某些事件，并在事件发生时触发一个回调函数。bind 方法的形式是：
~~~
    
    
~~~python
def bind(self, sequence, func, add=''):
~~~

其中：

sequence

    

~~~
是一个表示事件的目标种类的字符串。（详情请看 _[bind(3tk) 的手册页和 John Outsterhout 的书，:title-reference:`Tcl and the Tk Toolkit (2nd edition)](https://manpages.debian.org/bind\(3tk\) 的手册页和 John Outsterhout 的书，:title-reference:`Tcl and the Tk Toolkit \(2nd edition\))_ ，第 201 页。）

func
~~~
    

~~~
是带有一个参数的 Python 函数，发生事件时将会调用。传入的参数为一个 Event 实例。（以这种方式部署的函数通常称为 _回调函数_ 。）

add
~~~
    

~~~
可选项， `''` 或 `'+'` 。传入空字符串表示本次绑定将替换与此事件关联的其他所有绑定。传递 `'+'` 则意味着加入此事件类型已绑定函数的列表中。

例如：
~~~
    
    
~~~python
def turn_red(self, event):
    event.widget["activeforeground"] = "red"

self.button.bind("<Enter>", self.turn_red)
~~~

请注意，在 `turn_red()` 回调函数中如何访问事件的 widget 字段。该字段包含了捕获 X 事件的控件。下表列出了事件可供访问的其他字段，及其在 Tk 中的表示方式，这在查看 Tk 手册时很有用处。

Tk

|

Tkinter 事件字段

|

Tk

|

Tkinter 事件字段  
  
---|---|---|---  
  
%f

|

focus

|

%A

|

char  
  
%h

|

height

|

%E

|

send_event  
  
%k

|

keycode

|

%K

|

keysym  
  
%s

|

state

|

%N

|

keysym_num  
  
%t

|

time

|

%T

|

type  
  
%w

|

width

|

%W

|

widget  
  
%x

|

x

|

%X

|

x_root  
  
%y

|

y

|

%Y

|

y_root  
  
### index 参数¶

很多控件都需要传入 index 参数。该参数用于指明 Text 控件中的位置，或指明 Entry 控件中的字符，或指明 Menu 控件中的菜单项。

Entry 控件的索引（index、view index 等）

    

~~~
Entry 控件带有索引属性，指向显示文本中的字符位置。这些 `tkinter` 函数可用于访问文本控件中的这些特定位置：

Text 控件的索引
~~~
    

~~~
Text 控件的索引语法非常复杂，最好还是在 Tk 手册中查看。

Menu 索引（menu.invoke()、menu.entryconfig() 等）
~~~
    

~~~
菜单的某些属性和方法可以操纵特定的菜单项。只要属性或参数需要用到菜单索引，就可用以下方式传入：

  * 一个整数，指的是菜单项的数字位置，从顶部开始计数，从 0 开始；

  * 字符串 `"active"`，指的是当前光标所在的菜单；

  * 字符串 `"last"`，指的是上一个菜单项；

  * 带有 `@` 前缀的整数，比如 `@6`，这里的整数解释为菜单坐标系中的 y 像素坐标；

  * 表示没有任何菜单条目的字符串 `"none"` 经常与 menu.activate() 一同被用来停用所有条目，以及 ——

  * 与菜单项的文本标签进行模式匹配的文本串，从菜单顶部扫描到底部。请注意，此索引类型是在其他所有索引类型之后才会考虑的，这意味着文本标签为 `last`、`active` 或 `none` 的菜单项匹配成功后，可能会视为这些单词文字本身。

### 图片¶

通过 `tkinter.Image` 的各种子类可以创建相应格式的图片：

  * `BitmapImage` 对应 XBM 格式的图片。

  * `PhotoImage` 对应 PGM、PPM、GIF 和 PNG 格式的图片。后者自 Tk 8.6 开始支持。

这两种图片可通过 `file` 或 `data` 属性创建的（也可能由其他属性创建）。

然后可在某些支持 `image` 属性的控件中（如标签、按钮、菜单）使用图片对象。这时，Tk 不会保留对图片对象的引用。当图片对象的最后一个 Python 引用被删除时，图片数据也会删除，并且 Tk 会在用到图片对象的地方显示一个空白框。

参见

[Pillow](https://python-pillow.org/) 包增加了对 BMP, JPEG, TIFF 和 WebP 等多种格式的支持。

## 文件处理程序¶

Tk 允许为文件操作注册和注销一个回调函数，当对文件描述符进行 I/O 时，Tk 的主循环会调用该回调函数。每个文件描述符只能注册一个处理程序。示例代码如下：
~~~
    
    
~~~
import tkinter
widget = tkinter.Tk()
mask = tkinter.READABLE | tkinter.WRITABLE
widget.tk.createfilehandler(file, mask, callback)
...
widget.tk.deletefilehandler(file)
~~~

在 Windows 系统中不可用。

由于不知道可读取多少字节，你可能不希望使用 [`BufferedIOBase`](io.md#io.BufferedIOBase "io.BufferedIOBase") 或 [`TextIOBase`](io.md#io.TextIOBase "io.TextIOBase") 的 [`read()`](io.md#io.BufferedIOBase.read "io.BufferedIOBase.read") 或 [`readline()`](io.md#io.IOBase.readline "io.IOBase.readline") 方法，因为这些方法必须读取预定数量的字节。 对于套接字，可使用 [`recv()`](socket.md#socket.socket.recv "socket.socket.recv") 或 [`recvfrom()`](socket.md#socket.socket.recvfrom "socket.socket.recvfrom") 方法；对于其他文件，可使用原始读取方法或 `os.read(file.fileno(), maxbytecount)`。

Widget.tk.createfilehandler( _file_ , _mask_ , _func_ )¶

    

~~~
注册文件处理程序的回调函数 _func_ 。 _file_ 参数可以是具备 [`fileno()`](io.md#io.IOBase.fileno "io.IOBase.fileno") 方法的对象（例如文件或套接字对象），也可以是整数文件描述符。 _mask_ 参数是下述三个常量的逻辑“或”组合。回调函数将用以下格式调用：
~~~
    
    
~~~
callback(file, mask)
~~~

Widget.tk.deletefilehandler( _file_ )¶

    

~~~
注销文件处理函数。

tkinter.READABLE¶

tkinter.WRITABLE¶

tkinter.EXCEPTION¶
~~~
    

~~~
