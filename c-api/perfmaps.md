# 对 Perf Maps 的支持¶

在受支持的平台上（在撰写本文档时，只有 Linux），运行时可以利用 _perf map 文件_ 来使得 Python 函数对于外部性能分析工具可见（例如 [perf](https://perf.wiki.kernel.org/index.php/Main_Page) 等）。 正在运行的进行可以在 `/tmp` 目录中创建一个文件，其中包含可将部分可执行代码映射到特定名称的条目。 本接口的描述参见 [Linux Perf 工具文档](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/tools/perf/Documentation/jit-interface.txt)。

在 Python 中，这些辅助 API 可供依赖于动态生成机器码的库和特性使用。

请注意这些 API 并不要求持有全局解释器锁（GIL）。

int PyUnstable_PerfMapState_Init(void)¶  

    

~~~
_This is[ Unstable API](stable.md#unstable-c-api). It may change without warning in minor releases._

打开 `/tmp/perf-$pid.map` 文件，除非它已经被打开，并创建一个锁来确保线程安全地写入该文件（如果写入是通过 `PyUnstable_WritePerfMapEntry()` 执行的）。 通常，没有必要显式地调用此函数；只需使用 `PyUnstable_WritePerfMapEntry()` 这样它将在第一次调用时初始化状态。

成功时返回 `0`，创建/打开 perf map 文件失败时返回 `-1`，或者创建锁失败时返回 `-2`。 可检查 `errno` 获取有关失败原因的更多信息。

int PyUnstable_WritePerfMapEntry(const void *code_addr, unsigned int code_size, const char *entry_name)¶  
~~~
    

~~~
_This is[ Unstable API](stable.md#unstable-c-api). It may change without warning in minor releases._

向 `/tmp/perf-$pid.map` 文件写入一个单独条目。 此函数是线程安全的。 下面显示了一个示例条目:
~~~
    
    
~~~
# address      size  name
7f3529fcf759 b     py::bar:/run/t.py
~~~

将在写入条目之前调用 `PyUnstable_PerfMapState_Init()`，如果 perf map 文件尚未打开。 成功时返回 `0`，或者在失败时返回与 `PyUnstable_PerfMapState_Init()` 相同的错误代码。

void PyUnstable_PerfMapState_Fini(void)¶  

    

~~~
