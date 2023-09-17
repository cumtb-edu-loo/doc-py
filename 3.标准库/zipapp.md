# `zipapp` —— 管理可执行的 Python zip 打包文件¶

在 3.5 版本加入.

**源代码：** [Lib/zipapp.py](https://github.com/python/cpython/tree/3.12/Lib/zipapp.py)

* * *

本模块提供了一套管理工具，用于创建包含 Python 代码的压缩文件，这些文件可以 [直接由 Python 解释器执行](cmdline.md#using-on-interface-options)。 本模块提供 命令行接口 和 Python API。

## 简单示例¶

下述例子展示了用 命令行接口 根据含有 Python 代码的目录创建一个可执行的打包文件。 运行后该打包文件时，将会执行 `myapp` 模块中的 `main` 函数。

    
    
~~~
$ python -m zipapp myapp -m "myapp:main"
$ python myapp.pyz
<output from myapp>
~~~

## 命令行接口¶

若要从命令行调用，则采用以下形式：

    
    
~~~
$ python -m zipapp source [options]
~~~

如果 _source_ 是个目录，将根据 _source_ 的内容创建一个打包文件。如果 _source_ 是个文件，则应为一个打包文件，将会复制到目标打包文件中（如果指定了 -info 选项，将会显示 shebang 行的内容）。

可以接受以下参数：

-o <output>, \--output=<output>¶
    

将程序的输出写入名为 _output_ 的文件中。若未指定此参数，输出的文件名将与输入的 _source_ 相同，并添加扩展名 `.pyz`。如果显式给出了文件名，将会原样使用（因此必要时应包含扩展名 `.pyz`）。

如果 _source_ 是个打包文件，必须指定一个输出文件名（这时 _output_ 必须与 _source_ 不同）。

-p <interpreter>, \--python=<interpreter>¶
    

给打包文件加入 `#!` 行，以便指定 _解释器_ 作为运行的命令行。另外，还让打包文件在 POSIX 平台上可执行。默认不会写入 `#!` 行，也不让文件可执行。

-m <mainfn>, \--main=<mainfn>¶
    

在打包文件中写入一个 `__main__.py` 文件，用于执行 _mainfn_ 。 _mainfn_ 参数的形式应为 “pkg.mod:fn”，其中 “pkg.mod”是打包文件中的某个包/模块，“fn”是该模块中的一个可调用对象。`__main__.py` 文件将会执行该可调用对象。

在复制打包文件时，不能设置 `--main` 参数。

-c, \--compress¶
    

利用 deflate 方法压缩文件，减少输出文件的大小。默认情况下，打包文件中的文件是不压缩的。

在复制打包文件时，`--compress` 无效。

在 3.7 版本加入.

\--info¶

    

~~~
显示嵌入在打包文件中的解释器程序，以便诊断问题。这时会忽略其他所有参数，SOURCE 必须是个打包文件，而不是目录。

-h, \--help¶
~~~

打印简短的用法信息并退出。

## Python API¶

该模块定义了两个快捷函数：

zipapp.create_archive( _source_ , _target =None_, _interpreter =None_, _main =None_, _filter =None_, _compressed =False_)¶

    

~~~
由 _source_ 创建一个应用程序打包文件。source 可以是以下形式之一：

  * 一个目录名，或指向目录的 [path-like object](../glossary.md#term-path-like-object) ，这时将根据目录内容新建一个应用程序打包文件。

  * 一个已存在的应用程序打包文件名，或指向这类文件的 [path-like object](../glossary.md#term-path-like-object)，这时会将该文件复制为目标文件（会稍作修改以反映出 _interpreter_ 参数的值）。必要时文件名中应包括 `.pyz` 扩展名。

  * 一个以字节串模式打开的文件对象。该文件的内容应为应用程序打包文件，且假定文件对象定位于打包文件的初始位置。

_target_ 参数定义了打包文件的写入位置：

  * 若是个文件名，或是 [path-like object](../glossary.md#term-path-like-object)，打包文件将写入该文件中。

  * 若是个打开的文件对象，打包文件将写入该对象，该文件对象必须在字节串写入模式下打开。

  * 如果省略了 target （或为 `None`），则 source 必须为一个目录，target 将是与 source 同名的文件，并加上 `.pyz` 扩展名。

参数 _interpreter_ 指定了 Python 解释器程序名，用于执行打包文件。这将以 “释伴（shebang）”行的形式写入打包文件的头部。在 POSIX 平台上，操作系统会进行解释，而在 Windows 平台则会由 Python 启动器进行处理。省略 _interpreter_ 参数则不会写入释伴行。如果指定了解释器，且目标为文件名，则会设置目标文件的可执行属性位。

参数 _main_ 指定某个可调用程序的名称，用作打包文件的主程序。仅当 source 为目录且不含 `__main__.py` 文件时，才能指定该参数。 _main_ 参数应采用 “pkg.module:callable”的形式，通过导入“pkg.module”并不带参数地执行给出的可调用对象，即可执行打包文件。如果 source 是目录且不含 `__main__.py` 文件，省略 _main_ 将会出错，生成的打包文件将无法执行。

可选参数 _filter_ 指定了回调函数，将传给代表被添加文件路径的 Path 对象（相对于源目录）。如若文件需要加入打包文件，则回调函数应返回 `True`。

可选参数 _compressed_ 指定是否要压缩打包文件。若设为 `True`，则打包中的文件将用 deflate 方法进行压缩；否则就不会压缩。本参数在复制现有打包文件时无效。

若 _source_ 或 _target_ 指定的是文件对象，则调用者有责任在调用 create_archive 之后关闭这些文件对象。

当复制已有的打包文件时，提供的文件对象只需 `read` 和 `readline` 方法，或 `write` 方法。当由目录创建打包文件时，若目标为文件对象，将会将其传给 类，且必须提供 `zipfile.ZipFile` 类所需的方法。

在 3.7 版本加入: 加入了 _filter_ 和 _compressed_ 参数。

zipapp.get_interpreter( _archive_ )¶
~~~
    

~~~
返回打包文件开头的 行指定的解释器程序。如果没有 `#!` 行，则返回 [`None`](constants.md#None "None")。参数 _archive_ 可为文件名或在字节串模式下打开以供读取的文件类对象。`#!` 行假定是在打包文件的开头。

## 例子¶

将目录打包成一个文件并运行它。
~~~
    
    
~~~
$ python -m zipapp myapp
$ python myapp.pyz
<output from myapp>
~~~

同样还可用 `create_archive()` 函数完成：

    
    
~~~shell
>>> import zipapp
>>> zipapp.create_archive('myapp', 'myapp.pyz')
~~~

要让应用程序能在 POSIX 平台上直接执行，需要指定所用的解释器。

    
    
~~~
$ python -m zipapp myapp -p "/usr/bin/env python"
$ ./myapp.pyz
<output from myapp>
~~~

若要替换已有打包文件中的释伴行，请用 `create_archive()` 函数另建一个修改好的打包文件：

    
    
~~~shell
>>> import zipapp
>>> zipapp.create_archive('old_archive.pyz', 'new_archive.pyz', '/usr/bin/python3')
~~~

若要原地更新打包文件，可用 [`BytesIO`](io.md#io.BytesIO "io.BytesIO") 对象在内存中进行替换，然后再覆盖源文件。 请注意原地覆盖文件存在发生错误时丢失原始文件的风险。 这段代码没有考虑发生错误的情况，但生产性代码应该要考虑。 另外，此方法将仅在内存能容纳打包文件时才适用:

    
    
~~~shell
>>> import zipapp
>>> import io
>>> temp = io.BytesIO()
>>> zipapp.create_archive('myapp.pyz', temp, '/usr/bin/python2')
>>> with open('myapp.pyz', 'wb') as f:
>>>     f.write(temp.getvalue())
~~~

## 指定解释器程序¶

注意，如果指定了解释器程序再发布应用程序打包文件，需要确保所用到的解释器是可移植的。Windows 的 Python 启动器支持大多数常见的 POSIX `#!` 行，但还需要考虑一些其他问题。

  * 如果采用“/usr/bin/env python”（或其他格式的 python 调用命令，比如“/usr/bin/python”)，需要考虑默认版本既可能是 Python 2 又可能是 Python 3，应让代码在两个版本下均能正常运行。

  * 如果用到的 Python 版本明确，如“/usr/bin/env python3”，则没有该版本的用户将无法运行应用程序。（如果代码不兼容 Python 2，可能正该如此）。

  * 因为无法指定“python X.Y以上版本”，所以应小心“/usr/bin/env python3.4”这种精确版本的指定方式，因为对于 Python 3.5 的用户就得修改释伴行，比如：

通常应该用“/usr/bin/env python2”或“/usr/bin/env python3”的格式，具体根据代码适用于 Python 2 还是 3 而定。

## 用 zipapp 创建独立运行的应用程序¶

利用 `zipapp` 模块可以创建独立运行的 Python 程序，以便向最终用户发布，仅需在系统中装有合适版本的 Python 即可运行。操作的关键就是把应用程序代码和所有依赖项一起放入打包文件中。

创建独立运行打包文件的步骤如下：

  1. 照常在某个目录中创建应用程序，于是会有一个 `myapp` 目录，里面有个 `__main__.py` 文件，以及所有支持性代码。

  2. 用 pip 将应用程序的所有依赖项装入 `myapp` 目录。
    
        $ python -m pip install -r requirements.txt --target myapp
    

（这里假定在 `requirements.txt` 文件中列出了项目所需的依赖项，也可以在 pip 命令行中列出依赖项）。

  3. 用以下命令打包：
    
        $ python -m zipapp -p "interpreter" myapp
    

这会生成一个独立的可执行文件，可在任何装有合适解释器的机器上运行。详情参见 指定解释器程序。可以单个文件的形式分发给用户。

在 Unix 系统中， `myapp.pyz` 文件将以原有文件名执行。如果喜欢 “普通”的命令名，可以重命名该文件，去掉扩展名 `.pyz` 。在 Windows 系统中， `myapp.pyz[w]` 是可执行文件，因为 Python 解释器在安装时注册了扩展名 `.pyz` 和 `.pyzw` 。

### 注意事项¶

如果应用程序依赖某个带有 C 扩展的包，则此程序包无法由打包文件运行（这是操作系统的限制，因为可执行代码必须存在于文件系统中，操作系统才能加载）。这时可去除打包文件中的依赖关系，然后要求用户事先安装好该程序包，或者与打包文件一起发布并在 `__main__.py` 中增加代码，将未打包模块的目录加入 `sys.path` 中。采用增加代码方式时，一定要为目标架构提供合适的二进制文件（可能还需在运行时根据用户的机器选择正确的版本加入 `sys.path`）。

## Python 打包应用程序的格式¶

自 2.6 版开始，Python 即能够执行包含 文件的打包文件了。为了能被 Python 执行，应用程序的打包文件必须为包含 `__main__.py` 文件的标准 zip 文件，`__main__.py` 文件将作为应用程序的入口运行。类似于常规的 Python 脚本，父级（这里指打包文件）将放入 [`sys.path`](3.标准库/sys.md#sys.path "sys.path") ，因此可从打包文件中导入更多的模块。

zip 文件格式允许在文件中预置任意数据。利用这种能力，zip 应用程序格式在文件中预置了一个标准的 POSIX “释伴”行（`#!/path/to/interpreter`）。

因此，Python zip 应用程序的格式会如下所示：

  1. 可选的释伴行，包含字符 `b'#!'`，后面是解释器名，然后是换行符 （`b'\n'`）。 解释器名可为操作系统 “释伴”处理所能接受的任意值，或为 Windows 系统中的 Python 启动程序。解释器名在 Windows 中应用 UTF-8 编码，在 POSIX 中则用 [`sys.getfilesystemencoding()`](3.标准库/sys.md#sys.getfilesystemencoding "sys.getfilesystemencoding")。

  2. 标准的打包文件由 [`zipfile`](zipfile.md#module-zipfile "zipfile: Read and write ZIP-format archive files.") 模块生成。其中 _必须_ 包含一个名为 `__main__.py` 的文件（必须位于打包文件的“根”目录——不能位于某个子目录中）。打包文件中的数据可以是压缩或未压缩的。

如果应用程序的打包文件带有释伴行，则在 POSIX 系统中可能需要启用可执行属性，以允许直接执行。

不一定非要用本模块中的工具创建应用程序打包文件，本模块只是提供了便捷方案，上述格式的打包文件可用任何方式创建，均可被 Python 接受。

