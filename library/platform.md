# `platform` \--- 获取底层平台的标识数据¶

**源代码：** [Lib/platform.py](https://github.com/python/cpython/tree/3.12/Lib/platform.py)

* * *

备注

特定平台按字母顺序排列，Linux 包括在 Unix 小节之中。

## 跨平台¶

platform.architecture( _executable =sys.executable_, _bits =''_, _linkage =''_)¶

    

~~~
查询给定的可执行文件（默认为 Python 解释器二进制码文件）来获取各种架构信息。

返回一个元素 `(bits, linkage)`，其中包含可执行文件所使用的位架构和链接格式信息。 这两个值均以字符串形式返回。

无法确定的值将返回为形参预设所给出的值。 如果给出的位数为 `''`，则会使用 `sizeof(pointer)` (或者当 Python 版本 < 1.5.2 时为 `sizeof(long)`) 作为所支持的指针大小的提示。

此函数依赖于系统的 `file` 命令来执行实际的操作。 这在几乎所有 Unix 平台和某些非 Unix 平台上只有当可执行文件指向 Python 解释器时才可用。 当以上要求不满足时将会使用合理的默认值。

备注

在 macOS (也许还有其他平台) 上，可执行文件可能是包含多种架构的通用文件。

要获取当前解释器的“64 位性”，更可靠的做法是查询 [`sys.maxsize`](sys.md#sys.maxsize "sys.maxsize") 属性:
~~~
    
    
~~~
is_64bits = sys.maxsize > 2**32
~~~

platform.machine()¶

    

~~~
返回机器类型，例如 `'AMD64'` 。 如果该值无法确定则会返回一个空字符串。

platform.node()¶
~~~
    

~~~
返回计算机的网络名称（可能不是完整限定名称！）。 如果该值无法确定则会返回一个空字符串。

platform.platform( _aliased =False_, _terse =False_)¶
~~~
    

~~~
返回一个标识底层平台的字符串，其中带有尽可能多的有用信息。

输出信息的目标是“人类易读”而非机器易解析。 它在不同平台上可能看起来不一致，这是有意为之的。

如果 _aliased_ 为真值，此函数将使用各种平台不同与其通常名称的别名来报告系统名称，例如 SunOS 将被报告为 Solaris。 `system_alias()` 函数将被用于实现此功能。

将 _terse_ 设为真值将导致此函数只返回标识平台所必须的最小量信息。

在 3.8 版本发生变更: 在 macOS 上，此函数现在会在 `mac_ver()` 返回的发布版字符串非空时使用它，以便获取 macOS 版本而非 darwin 版本。

platform.processor()¶
~~~
    

~~~
返回（真实的）处理器名称，例如 `'amdk6'`。

如果该值无法确定则将返回空字符串。 请注意许多平台都不提供此信息或是简单地返回与 `machine()` 相同的值。 NetBSD 则会提供此信息。

platform.python_build()¶
~~~
    

~~~
返回一个元组 `(buildno, builddate)`，以字符串表示的 Python 编译代码和日期。

platform.python_compiler()¶
~~~
    

~~~
返回一个标识用于编译 Python 的编译器的的字符串。

platform.python_branch()¶
~~~
    

~~~
返回一个标识 Python 实现的 SCM 分支的字符串。

platform.python_implementation()¶
~~~
    

~~~
返回一个标识 Python 实现的字符串。 可能的返回值有: 'CPython', 'IronPython', 'Jython', 'PyPy'。

platform.python_revision()¶
~~~
    

~~~
返回一个标识 Python 实现的 SCM 修订版的字符串。

platform.python_version()¶
~~~
    

~~~
将 Python 版本以字符串 `'major.minor.patchlevel'` 形式返回。

请注意此返回值不同于 Python `sys.version`，它将总是包括 patchlevel (默认为 0)。

platform.python_version_tuple()¶
~~~
    

~~~
将 Python 版本以字符串元组 `(major, minor, patchlevel)` 形式返回。

请注意此返回值不同于 Python `sys.version`，它将总是包括 patchlevel (默认为 `'0'`)。

platform.release()¶
~~~
    

~~~
返回系统的发布版本，例如 `'2.2.0'` 或 `'NT'`，如果该值无法确定则将返回一个空字符串。

platform.system()¶
~~~
    

~~~
返回系统平台/OS的名称，例如 `'Linux'`, `'Darwin'`, `'Java'`, `'Windows'`。 如果该值无法确定则将返回一个空字符串。

platform.system_alias( _system_ , _release_ , _version_ )¶
~~~
    

~~~
返回别名为某些系统所使用的常见营销名称的 `(system, release, version)`。 它还会在可能导致混淆的情况下对信息进行一些重排序操作。

platform.version()¶
~~~
    

~~~
返回系统的发布版本信息，例如 `'#3 on degas'`。 如果该值无法确定则将返回一个空字符串。

platform.uname()¶
~~~
    

~~~
具有高可移植性的 uname 接口。 返回包含六个属性的 [`namedtuple()`](collections.md#collections.namedtuple "collections.namedtuple"): `system`, `node`, `release`, `version`, `machine` 和 `processor`。

`processor` 将根据需要延后获取。

注意：前两个属性名称与 [`os.uname()`](os.md#os.uname "os.uname") 所提供的名称不同，后者是被命名为 `sysname` 和 `nodename`。

无法确定的条目会被设为 `''`。

在 3.3 版本发生变更: 结果由元组改为 [`namedtuple()`](collections.md#collections.namedtuple "collections.namedtuple") 。

在 3.9 版本发生变更: `processor` 将延后而不是立即被获取。

## Java平台¶

platform.java_ver( _release =''_, _vendor =''_, _vminfo =('', '', '')_, _osinfo =('', '', '')_)¶
~~~
    

~~~
Jython 的版本接口

返回一个元组 `(release, vendor, vminfo, osinfo)`，其中 _vminfo_ 为元组 `(vm_name, vm_release, vm_vendor)` 而 _osinfo_ 为元组 `(os_name, os_version, os_arch)`。 无法确定的值将设为由形参所给出的默认值 (默认均为 `''`)。

## Windows平台¶

platform.win32_ver( _release =''_, _version =''_, _csd =''_, _ptype =''_)¶
~~~
    

~~~
从 Windows 注册表获取额外的版本信息并返回一个元组 `(release, version, csd, ptype)` 表示 OS 发行版, 版本号, CSD 级别 (Service Pack) 和 OS 类型 (多个/单个处理器)。无法确定的值被设置为作为参数给出的默认值（这些参数都默认为一个空字符串）。

一点提示: _ptype_ 在单个处理器的 NT 机器上为 `'Uniprocessor Free'` 而在多个处理器的机器上为 `'Multiprocessor Free'`。 _'Free'_ 是指该 OS 版本没有调试代码。 它还可能显示 _'Checked'_ 表示该 OS 版本使用了调试代码，即检测参数、范围等的代码。

platform.win32_edition()¶
~~~
    

~~~
返回一个代表当前 Windows 版本的字符串，或者在该值无法确定时返回 `None` 。 可能的值包括但不限于 `'Enterprise'`, `'IoTUAP'`, `'ServerStandard'` 和 `'nanoserver'`。

在 3.8 版本加入.

platform.win32_is_iot()¶
~~~
    

~~~
如果 `win32_edition()` 返回的 Windows 版本被识别为 IoT 版则返回 `True`。

在 3.8 版本加入.

## macOS 平台¶

platform.mac_ver( _release =''_, _versioninfo =('', '', '')_, _machine =''_)¶
~~~
    

~~~
获取 macOS 版本信息并将其返回为元组 `(release, versioninfo, machine)`，其中 _versioninfo_ 是一个元组 `(version, dev_stage, non_release_version)`。

无法确定的条目会被设为 `''`。 所有元组条目均为字符串。

## Unix 平台¶

platform.libc_ver( _executable =sys.executable_, _lib =''_, _version =''_, _chunksize =16384_)¶
~~~
    

~~~
尝试确定可执行文件（默认为 Python 解释器）所链接到的 libc 版本。 返回一个字符串元组 `(lib, version)`，当查找失败时其默认值将设为给定的形参值。

请注意此函数对于不同 libc 版本向可执行文件添加符号的方式有深层的关联，可能仅适用于使用 **gcc** 编译出来的可执行文件。

文件将按 _chunksize_ 个字节的分块来读取和扫描。

## Linux 平台¶

platform.freedesktop_os_release()¶
~~~
    

~~~
从 `os-release` 文件获取操作系统标识并将其作为一个字典返回。 `os-release` 文件是 [freedesktop.org 标准](https://www.freedesktop.org/software/systemd/man/os-release.md) 并在大多数 Linux 发行版上可用。 一个重要的例外是 Android 和基于 Android 的发行版。

当 `/etc/os-release` 或 `/usr/lib/os-release` 均无法读取时将引发 [`OSError`](exceptions.md#OSError "OSError") 或其子类。

成功时，该函数将返回一个字典，其中键和值均为字符串。 值当中的特殊字符例如 `"` 和 `{TX-PL-LABEL}#x60;` 会被复原。 字段 `NAME`, `ID` 和 `PRETTY_NAME` 总是会按照标准来定义。 所有其他字段都是可选的。 厂商可能会包括额外的字段。

请注意 `NAME`, `VERSION` 和 `VARIANT` 等字段是适用于向用户展示的字符串。 程序应当使用 `ID`, `ID_LIKE`, `VERSION_ID` 或 `VARIANT_ID` 等字段来标识 Linux 发行版。

示例:
~~~
    
    
~~~python
def get_like_distro():
    info = platform.freedesktop_os_release()
    ids = [info["ID"]]
    if "ID_LIKE" in info:
        # ids are space separated and ordered by precedence
        ids.extend(info["ID_LIKE"].split())
    return ids
~~~

在 3.10 版本加入.

