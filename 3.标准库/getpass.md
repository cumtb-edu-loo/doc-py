# `getpass` \--- 便携式密码输入工具¶

**源代码:** [Lib/getpass.py](https://github.com/python/cpython/tree/3.12/Lib/getpass.py)

* * *

[可用性](3.标准库/intro.md#availability): 非 Emscripten，非 WASI。

此模块在 WebAssembly 平台 `wasm32-emscripten` 和 `wasm32-wasi` 上不适用或不可用。 请参阅 [WebAssembly 平台](3.标准库/intro.md#wasm-availability) 了解详情。

`getpass` 模块提供了两个函数：

getpass.getpass( _prompt ='Password: '_, _stream =None_)¶

    

~~~
提示用户输入一个密码且不会回显。 用户会看到字符串 _prompt_ 作为提示，其默认值为 `'Password: '`。 在 Unix 上，如有必要提示会使用替换错误句柄写入到文件类对象 _stream_ 。 _stream_ 默认指向控制终端 (`/dev/tty`)，如果不可用则指向 `sys.stderr` (此参数在 Windows 上会被忽略)。

如果回显自由输入不可用则 getpass() 将回退为打印一条警告消息到 _stream_ 并且从 `sys.stdin` 读取同时发出 `GetPassWarning`。

备注

如果你从 IDLE 内部调用 getpass，输入可能是在你启动 IDLE 的终端中而非在 IDLE 窗口本身中完成。

_exception _getpass.GetPassWarning¶
~~~
    

~~~
一个当密码输入可能被回显时发出的 [`UserWarning`](exceptions.md#UserWarning "UserWarning") 子类。

getpass.getuser()¶
~~~
    

~~~
