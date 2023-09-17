# API 和 ABI 版本管理¶

CPython 在下列宏中暴露其版本号。 请注意这对应于 **编译** 用版本代码，而不是 **运行时** 使用的版本。

请参阅 [C API 的稳定性](stable.md#stable) 查看跨版本的 API 和 ABI 稳定情。

PY_MAJOR_VERSION¶  

    

~~~
`3` (`3.4.1a2` 中的第一段)。

PY_MINOR_VERSION¶  
~~~
    

~~~
`4` (`3.4.1a2` 中的第二段)。

PY_MICRO_VERSION¶  
~~~
    

~~~
`1` (`3.4.1a2` 中第三段的数字)。

PY_RELEASE_LEVEL¶  
~~~
    

~~~
`a` (`3.4.1a2` 中第3段的字母)。 可能为 `0xA` 即 alpha, `0xB` 即 beta, `0xC` 即 release candidate 或 `0xF` 即 final。

PY_RELEASE_SERIAL¶  
~~~
    

~~~
`2` (`3.4.1a2` 中的末尾数字)。 零代表最终发布版。

PY_VERSION_HEX¶  
~~~
    

~~~
