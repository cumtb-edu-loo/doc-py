# 抽象对象层¶

本章中的函数与 Python对象交互，无论其类型，或具有广泛类的对象类型（例如，所有数值类型，或所有序列类型）。当使用对象类型并不适用时，他们会产生一个 Python 异常。

这些函数是不可能用于未正确初始化的对象的，如一个列表对象被 [`PyList_New()`](list.md#c.PyList_New "PyList_New") 创建，但其中的项目没有被设置为一些非 `NULL` 的值。

  * [对象协议](object.md)
  * [调用协议](call.md)
    * [_tp_call_ 协议](call.md#the-tp-call-protocol)
    * [Vectorcall 协议](call.md#the-vectorcall-protocol)
      * [递归控制](call.md#recursion-control)
      * [Vectorcall 支持 API](call.md#vectorcall-support-api)
    * [调用对象的 API](call.md#object-calling-api)
    * [调用支持 API](call.md#call-support-api)
  * [数字协议](number.md)
  * [序列协议](sequence.md)
  * [映射协议](mapping.md)
  * [迭代器协议](iter.md)
  * [缓冲协议](buffer.md)
    * [缓冲区结构](buffer.md#buffer-structure)
    * [缓冲区请求的类型](buffer.md#buffer-request-types)
      * [与请求无关的字段](buffer.md#request-independent-fields)
      * [只读，格式](buffer.md#readonly-format)
      * [形状，步幅，子偏移量](buffer.md#shape-strides-suboffsets)
      * [连续性的请求](buffer.md#contiguity-requests)
      * [复合请求](buffer.md#compound-requests)
    * [复杂数组](buffer.md#complex-arrays)
      * [NumPy-风格：形状和步幅](buffer.md#numpy-style-shape-and-strides)
      * [PIL-风格：形状，步幅和子偏移量](buffer.md#pil-style-shape-strides-and-suboffsets)
    * [缓冲区相关函数](buffer.md#buffer-related-functions)

