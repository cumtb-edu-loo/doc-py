# `webbrowser` \--- 方便的 Web 浏览器控制工具¶

**源码：** [Lib/webbrowser.py](https://github.com/python/cpython/tree/3.12/Lib/webbrowser.py)

* * *

`webbrowser` 模块提供了一个高层级接口，允许向用户显示基于 Web 的文档。 在大多数情况下，只需调用此模块的 `open()` 函数就可以了。

在 Unix 下，图形浏览器在 X11 下是首选，但如果图形浏览器不可用或 X11 显示不可用，则将使用文本模式浏览器。 如果使用文本模式浏览器，则调用进程将阻塞，直到用户退出浏览器。

如果存在环境变量 `BROWSER` ，则将其解释为 [`os.pathsep`](os.md#os.pathsep "os.pathsep") 分隔的浏览器列表，以便在平台默认值之前尝试。 当列表部分的值包含字符串 `％s` 时，它被解释为一个文字浏览器命令行，用于替换 `％s` 的参数 URL ；如果该部分不包含 `％s`，则它只被解释为要启动的浏览器的名称。 [1]

对于非 Unix 平台，或者当 Unix 上有远程浏览器时，控制过程不会等待用户完成浏览器，而是允许远程浏览器在显示界面上维护自己的窗口。 如果 Unix 上没有远程浏览器，控制进程将启动一个新的浏览器并等待。

脚本 **webbrowser** 可以用作模块的命令行界面。它接受一个 URL 作为参数。还接受以下可选参数：`-n` 如果可能，在新的浏览器窗口中打开 URL ； `-t` 在新的浏览器页面（“标签”）中打开 URL。这些选择当然是相互排斥的。用法示例:

    
    
~~~
python -m webbrowser -t "https://www.python.org"
~~~

[可用性](intro.md#availability): 非 Emscripten，非 WASI。

此模块在 WebAssembly 平台 `wasm32-emscripten` 和 `wasm32-wasi` 上不适用或不可用。 请参阅 [WebAssembly 平台](intro.md#wasm-availability) 了解详情。

定义了以下异常：

_exception _webbrowser.Error¶

    

~~~
发生浏览器控件错误时引发异常。

定义了以下函数：

webbrowser.open( _url_ , _new =0_, _autoraise =True_)¶
~~~
    

~~~
使用默认浏览器显示 _url_ 。 如果 _new_ 为 0，则尽可能在同一浏览器窗口中打开 _url_ 。 如果 _new_ 为 1，则尽可能打开新的浏览器窗口。 如果 _new_ 为 2，则尽可能打开新的浏览器页面（“标签”）。 如果 _autoraise_ 为 “True”，则会尽可能置前窗口（请注意，在许多窗口管理器下，无论此变量的设置如何，都会置前窗口）。

请注意，在某些平台上，尝试使用此函数打开文件名，可能会起作用并启动操作系统的关联程序。 但是，这种方式不被支持也不可移植。

使用 `url` 参数会引发 [auditing event](sys.md#auditing) `webbrowser.open` 。

webbrowser.open_new( _url_ )¶
~~~
    

~~~
如果可能，在默认浏览器的新窗口中打开 _url_ ，否则，在唯一的浏览器窗口中打开 _url_ 。

webbrowser.open_new_tab( _url_ )¶
~~~
    

~~~
如果可能，在默认浏览器的新页面（“标签”）中打开 _url_ ，否则等效于 `open_new()`。

webbrowser.get( _using =None_)¶
~~~
    

~~~
返回浏览器类型为 _using_ 指定的控制器对象。 如果 _using_ 为 `None`，则返回适用于调用者环境的默认浏览器的控制器。

webbrowser.register( _name_ , _constructor_ , _instance =None_, _*_ , _preferred =False_)¶
~~~
    

~~~
注册 _name_ 浏览器类型。 注册浏览器类型后， `get()` 函数可以返回该浏览器类型的控制器。 如果没有提供 _instance_ ，或者为 `None`， _constructor_ 将在没有参数的情况下被调用，以在需要时创建实例。 如果提供了 _instance_ ，则永远不会调用 _constructor_ ，并且可能是 `None`。

将 _preferred_ 设置为 `True` 使得这个浏览器成为 `get()` 不带参数调用的首选结果。 否则，只有在您计划设置 `BROWSER` 变量，或使用与您声明的处理程序的名称相匹配的非空参数调用 `get()` 时，此入口点才有用。

在 3.7 版本发生变更: 添加了仅关键字参数 _preferred_ 。

预定义了许多浏览器类型。 此表给出了可以传递给 `get()` 函数的类型名称以及控制器类的相应实例化，这些都在此模块中定义。

类型名

|

类名

|

备注  
  
---|---|---  
  
`'mozilla'`

|

`Mozilla('mozilla')`

|  
  
`'firefox'`

|

`Mozilla('mozilla')`

|  
  
`'epiphany'`

|

`Epiphany('epiphany')`

|  
  
`'kfmclient'`

|

`Konqueror()`

|

(1)  
  
`'konqueror'`

|

`Konqueror()`

|

(1)  
  
`'kfm'`

|

`Konqueror()`

|

(1)  
  
`'opera'`

|

`Opera()`

|  
  
`'links'`

|

`GenericBrowser('links')`

|  
  
`'elinks'`

|

`Elinks('elinks')`

|  
  
`'lynx'`

|

`GenericBrowser('lynx')`

|  
  
`'w3m'`

|

`GenericBrowser('w3m')`

|  
  
`'windows-default'`

|

`WindowsDefault`

|

(2)  
  
`'macosx'`

|

`MacOSXOSAScript('default')`

|

(3)  
  
`'safari'`

|

`MacOSXOSAScript('safari')`

|

(3)  
  
`'google-chrome'`

|

`Chrome('google-chrome')`

|  
  
`'chrome'`

|

`Chrome('chrome')`

|  
  
`'chromium'`

|

`Chromium('chromium')`

|  
  
`'chromium-browser'`

|

`Chromium('chromium-browser')`

|  
  
注释：

  1. "Konqueror" is the file manager for the KDE desktop environment for Unix, and only makes sense to use if KDE is running. Some way of reliably detecting KDE would be nice; the `KDEDIR` variable is not sufficient. Note also that the name "kfm" is used even when using the **konqueror** command with KDE 2 --- the implementation selects the best strategy for running Konqueror.

  2. 仅限 Windows 平台。

  3. 仅限 macOS 平台。

在 3.2 版本加入: A new `MacOSXOSAScript` class has been added and is used on Mac instead of the previous `MacOSX` class. This adds support for opening browsers not currently set as the OS default.

在 3.3 版本加入: 添加了对 Chrome/Chromium 的支持。

在 3.12 版本发生变更: 对某些过时浏览器的支持已被移除。 被移除的浏览器包括 Grail, Mosaic, Netscape, Galeon, Skipstone, Iceape 和 Firefox 35 及以下的版本。

以下是一些简单的例子:
~~~
    
    
~~~
url = 'https://docs.python.org/'

# Open URL in a new tab, if a browser window is already open.
webbrowser.open_new_tab(url)

# Open URL in new window, raising the window if possible.
webbrowser.open_new(url)
~~~

## 浏览器控制器对象¶

浏览器控制器提供三个与模块级便捷函数相同的方法：

controller.name¶

    

~~~
浏览器依赖于系统的名称。

controller.open( _url_ , _new =0_, _autoraise =True_)¶
~~~
    

~~~
使用此控制器处理的浏览器显示 _url_ 。 如果 _new_ 为 1，则尽可能打开新的浏览器窗口。 如果 _new_ 为 2，则尽可能打开新的浏览器页面（“标签”）。

controller.open_new( _url_ )¶
~~~
    

~~~
如果可能，在此控制器处理的浏览器的新窗口中打开 _url_ ，否则，在唯一的浏览器窗口中打开 _url_ 。 别名 `open_new()`。

controller.open_new_tab( _url_ )¶
~~~
    

~~~
