# 具体的对象层¶

本章中的函数特定于某些 Python 对象类型。 将错误类型的对象传递给它们并不是一个好主意；如果您从 Python 程序接收到一个对象，但不确定它是否具有正确的类型，则必须首先执行类型检查；例如，要检查对象是否为字典，请使用 [`PyDict_Check()`](dict.md#c.PyDict_Check "PyDict_Check")。 本章的结构类似于 Python 对象类型的“家族树”。

警告

虽然本章所描述的函数会仔细检查传入对象的类型，但是其中许多函数不会检查传入的对象是否为 `NULL`。 允许传入 `NULL` 可能导致内存访问冲突和解释器的立即终止。

## 基本对象¶

本节描述Python类型对象和单一实例对象 象None。

  * [类型对象](type.md)
    * [创建堆分配类型](type.md#creating-heap-allocated-types)
  * [`None` 对象](none.md)

## 数值对象¶

  * [整数型对象](long.md)
  * [布尔对象](bool.md)
  * [浮点数对象](float.md)
    * [打包与解包函数](float.md#pack-and-unpack-functions)
      * [打包函数](float.md#pack-functions)
      * [解包函数](float.md#unpack-functions)
  * [复数对象](complex.md)
    * [表示复数的C结构体](complex.md#complex-numbers-as-c-structures)
    * [表示复数的Python对象](complex.md#complex-numbers-as-python-objects)

## 序列对象¶

序列对象的一般操作在前一章中讨论过;本节介绍Python语言固有的特定类型的序列对象。

  * [bytes 对象](bytes.md)
  * [字节数组对象](bytearray.md)
    * [类型检查宏](bytearray.md#type-check-macros)
    * [直接 API 函数](bytearray.md#direct-api-functions)
    * [宏](bytearray.md#macros)
  * [Unicode对象和编码解码器](unicode.md)
    * [Unicode对象](unicode.md#unicode-objects)
      * [Unicode类型](unicode.md#unicode-type)
      * [Unicode字符属性](unicode.md#unicode-character-properties)
      * [Creating and accessing Unicode strings](unicode.md#creating-and-accessing-unicode-strings)
      * [Locale Encoding](unicode.md#locale-encoding)
      * [文件系统编码格式](unicode.md#file-system-encoding)
      * [wchar_t Support](unicode.md#wchar-t-support)
    * [Built-in Codecs](unicode.md#built-in-codecs)
      * [Generic Codecs](unicode.md#generic-codecs)
      * [UTF-8 编解码器](unicode.md#utf-8-codecs)
      * [UTF-32 Codecs](unicode.md#utf-32-codecs)
      * [UTF-16 Codecs](unicode.md#utf-16-codecs)
      * [UTF-7 Codecs](unicode.md#utf-7-codecs)
      * [Unicode-Escape 编解码器](unicode.md#unicode-escape-codecs)
      * [Raw-Unicode-Escape Codecs](unicode.md#raw-unicode-escape-codecs)
      * [Latin-1 Codecs](unicode.md#latin-1-codecs)
      * [ASCII Codecs](unicode.md#ascii-codecs)
      * [Character Map Codecs](unicode.md#character-map-codecs)
      * [MBCS codecs for Windows](unicode.md#mbcs-codecs-for-windows)
      * [Methods & Slots](unicode.md#methods-slots)
    * [方法与槽位函数](unicode.md#methods-and-slot-functions)
  * [元组对象](tuple.md)
  * [结构序列对象](tuple.md#struct-sequence-objects)
  * [列表对象](list.md)

## 容器对象¶

  * [字典对象](dict.md)
  * [集合对象](set.md)

## Function 对象¶

  * [Function 对象](function.md)
  * [实例方法对象](method.md)
  * [方法对象](method.md#method-objects)
  * [Cell 对象](cell.md)
  * [代码对象](code.md)
  * [附加信息](code.md#extra-information)

## 其他对象¶

  * [文件对象](file.md)
  * [模块对象](module.md)
    * [初始化 C 模块](module.md#initializing-c-modules)
      * [单阶段初始化](module.md#single-phase-initialization)
      * [多阶段初始化](module.md#multi-phase-initialization)
      * [底层模块创建函数](module.md#low-level-module-creation-functions)
      * [支持函数](module.md#support-functions)
    * [查找模块](module.md#module-lookup)
  * [迭代器对象](iterator.md)
  * [描述符对象](descriptor.md)
  * [切片对象](slice.md)
    * [Ellipsis 对象](slice.md#ellipsis-object)
  * [MemoryView 对象](memoryview.md)
  * [弱引用对象](weakref.md)
  * [Capsule 对象](capsule.md)
  * [帧对象](frame.md)
    * [内部帧](frame.md#internal-frames)
  * [生成器对象](gen.md)
  * [协程对象](coro.md)
  * [上下文变量对象](contextvars.md)
  * [DateTime 对象](datetime.md)
  * [类型注解对象](typehints.md)

