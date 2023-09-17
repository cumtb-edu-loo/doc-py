# 网络和进程间通信¶

本章介绍的模块提供了网络和进程间通信的机制。

某些模块仅适用于同一台机器上的两个进程，例如 [`signal`](signal.md#module-signal "signal: Set handlers for asynchronous events.") 和 [`mmap`](mmap.md#module-mmap "mmap: Interface to memory-mapped files for Unix and Windows.") 。 其他模块支持两个或多个进程可用于跨机器通信的网络协议。

本章中描述的模块列表是：

  * [`asyncio` \--- 异步 I/O](asyncio.md)
  * [`socket` \--- 底层网络接口](socket.md)
  * [`ssl` \--- 套接字对象的 TLS/SSL 包装器](ssl.md)
  * [`select` \--- 等待 I/O 完成](select.md)
  * [`selectors` \--- 高级 I/O 复用库](selectors.md)
  * [`signal` \--- 设置异步事件处理程序](signal.md)
  * [`mmap` \--- 内存映射文件支持](mmap.md)

