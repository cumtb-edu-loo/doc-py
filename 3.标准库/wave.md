# `wave` \--- 读写WAV格式文件¶

**源代码:** [Lib/wave.py](https://github.com/python/cpython/tree/3.12/Lib/wave.py)

* * *

`wave` 模块提供了一个处理 Waveform Audio "WAVE" (或称 "WAV") 文件格式的便利接口。 仅支持未压缩的 PCM 编码波形文件。

在 3.12 版本发生变更: 增加了对 `WAVE_FORMAT_EXTENSIBLE` 标头的支持，要求扩展格式为 `KSDATAFORMAT_SUBTYPE_PCM`。

`wave` 模块定义了以下函数和异常:

wave.open( _file_ , _mode =None_)¶

    

~~~
如果 _file_ 是一个字符串，打开对应文件名的文件。否则就把它作为文件类对象来处理。 _mode_ 可以为以下值：

`'rb'`
~~~
    

~~~
只读模式。

`'wb'`
~~~
    

~~~
只写模式。

注意不支持同时读写WAV文件。

_mode_ 设为 `'rb'` 时返回一个 `Wave_read` 对象，而 _mode_ 设为 `'wb'` 时返回一个 `Wave_write` 对象。如果省略 _mode_ 并指定 _file_ 来传入一个文件类对象，则 `file.mode` 会被用作 _mode_ 的默认值。

如果你传入一个文件类对象，当调用 wave 对象的 `close()` 方法时并不会真正关闭它；调用者需要负责关闭文件对象。

`open()` 函数可以在 [`with`](../reference/compound_stmts.md#with) 语句中使用。 当 `with` 阻塞结束时，`Wave_read.close()` 或 `Wave_write.close()` 方法会被调用。

在 3.4 版本发生变更: 添加了对不可搜索文件的支持。

_exception _wave.Error¶
~~~
    

~~~
当不符合WAV格式或无法操作时引发的错误。

## Wave_read对象¶

_class _wave.Wave_read¶
~~~
    

~~~
读取一个 WAV 文件。

由 `open()` 返回的 Wave_read 对象，有以下几种方法:

close()¶
~~~
    

~~~
关闭 `wave` 打开的数据流并使对象不可用。当对象销毁时会自动调用。

getnchannels()¶
~~~
    

~~~
返回声道数量（`1` 为单声道，`2` 为立体声）

getsampwidth()¶
~~~
    

~~~
返回采样字节长度。

getframerate()¶
~~~
    

~~~
返回采样频率。

getnframes()¶
~~~
    

~~~
返回音频总帧数。

getcomptype()¶
~~~
    

~~~
返回压缩类型（只支持 `'NONE'` 类型）

getcompname()¶
~~~
    

~~~
`getcomptype()` 的通俗版本。使用 `'not compressed'` 代替 `'NONE'`。

getparams()¶
~~~
    

~~~
返回一个 [`namedtuple()`](collections.md#collections.namedtuple "collections.namedtuple") `(nchannels, sampwidth, framerate, nframes, comptype, compname)`，等价于 `get*()` 方法的输出。

readframes( _n_ )¶
~~~
    

~~~
读取并返回以 [`bytes`](stdtypes.md#bytes "bytes") 对象表示的最多 _n_ 帧音频。

rewind()¶
~~~
    

~~~
重置文件指针至音频开头.

The following two methods are defined for compatibility with the old `aifc` module, and don't do anything interesting.

getmarkers()¶
~~~
    

~~~
返回 `None`。

从 3.13 版起不建议使用，将在 3.15 版中移除: The method only existed for compatibility with the `aifc` module which has been removed in Python 3.13.

getmark( _id_ )¶
~~~
    

~~~
引发错误异常。

从 3.13 版起不建议使用，将在 3.15 版中移除: The method only existed for compatibility with the `aifc` module which has been removed in Python 3.13.

以下两个方法都使用指针，具体实现由其底层决定。

setpos( _pos_ )¶
~~~
    

~~~
设置文件指针到指定位置。

tell()¶
~~~
    

~~~
返回当前文件指针位置。

## Wave_write 对象¶

_class _wave.Wave_write¶
~~~
    

~~~
写入一个 WAV 文件。

Wave_write 对象，由 `open()` 返回。

对于可查找的输出流，`wave` 头将自动更新以反映实际写入的帧数。 对于不可查找的流，当写入第一帧时 _nframes_ 值必须是准确的。 要获取准确的 _nframes_ 值可以通过调用 `setnframes()` 或 `setparams()` 并附带 `close()` 被调用之前将要写入的帧数然后使用 `writeframesraw()` 来写入帧数据，或者通过调用 `writeframes()` 并附带所有要写入的帧。 在后一种情况下 `writeframes()` 将计算数据中的帧数并在写入帧数据之前相应地设置 _nframes_ 。

在 3.4 版本发生变更: 添加了对不可搜索文件的支持。

Wave_write 对象具有以下方法:

close()¶
~~~
    

~~~
确保 _nframes_ 是正确的，并在文件被 `wave` 打开时关闭它。 此方法会在对象收集时被调用。 如果输出流不可查找且 _nframes_ 与实际写入的帧数不匹配时引发异常。

setnchannels( _n_ )¶
~~~
    

~~~
设置声道数。

setsampwidth( _n_ )¶
~~~
    

~~~
设置采样字节长度为 _n_ 。

setframerate( _n_ )¶
~~~
    

~~~
设置采样频率为 _n_ 。

在 3.2 版本发生变更: 对此方法的非整数输入会被舍入到最接近的整数。

setnframes( _n_ )¶
~~~
    

~~~
设置总帧数为 _n_ 。 如果与之后实际写入的帧数不一致此值将会被更改（ 如果输出流不可查找则此更改尝试将引发错误）。

setcomptype( _type_ , _name_ )¶
~~~
    

~~~
设置压缩格式。目前只支持 `NONE` 即无压缩格式。

setparams( _tuple_ )¶
~~~
    

~~~
_tuple_ 应该是 `(nchannels, sampwidth, framerate, nframes, comptype, compname)`，每项的值可用于 `set*()` 方法。 设置所有形参。

tell()¶
~~~
    

~~~
返回当前文件指针，其指针含义和 `Wave_read.tell()` 以及 `Wave_read.setpos()` 是一致的。

writeframesraw( _data_ )¶
~~~
    

~~~
写入音频数据但不更新 _nframes_ 。

在 3.4 版本发生变更: 现在可接受任意 [bytes-like object](../glossary.md#term-bytes-like-object)。

writeframes( _data_ )¶
~~~
    

~~~
