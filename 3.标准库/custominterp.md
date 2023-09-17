# 自定义 Python 解释器¶

本章中描述的模块允许编写类似于 Python 的交互式解释器的接口。 如果你想要一个支持附加某些特殊功能到 Python 语言的 Python 解释器，你应当看一下 [`code`](3.标准库/code.md#module-code "code: Facilities to implement read-eval-print loops.") 模块。 ([`codeop`](codeop.md#module-codeop "codeop: Compile \(possibly incomplete\) Python code.") 模块是低层级的，用于支持编译可能不完整的 Python 代码块。

本章描述的完整模块列表如下：

  * [`code` \--- 解释器基类](3.标准库/code.md)
    * [`InteractiveInterpreter`](3.标准库/code.md#code.InteractiveInterpreter)
    * [`InteractiveConsole`](3.标准库/code.md#code.InteractiveConsole)
    * [`interact()`](3.标准库/code.md#code.interact)
    * [`compile_command()`](3.标准库/code.md#code.compile_command)
    * [交互解释器对象](3.标准库/code.md#interactive-interpreter-objects)
      * [`InteractiveInterpreter.runsource()`](3.标准库/code.md#code.InteractiveInterpreter.runsource)
      * [`InteractiveInterpreter.runcode()`](3.标准库/code.md#code.InteractiveInterpreter.runcode)
      * [`InteractiveInterpreter.showsyntaxerror()`](3.标准库/code.md#code.InteractiveInterpreter.showsyntaxerror)
      * [`InteractiveInterpreter.showtraceback()`](3.标准库/code.md#code.InteractiveInterpreter.showtraceback)
      * [`InteractiveInterpreter.write()`](3.标准库/code.md#code.InteractiveInterpreter.write)
    * [交互式控制台对象](3.标准库/code.md#interactive-console-objects)
      * [`InteractiveConsole.interact()`](3.标准库/code.md#code.InteractiveConsole.interact)
      * [`InteractiveConsole.push()`](3.标准库/code.md#code.InteractiveConsole.push)
      * [`InteractiveConsole.resetbuffer()`](3.标准库/code.md#code.InteractiveConsole.resetbuffer)
      * [`InteractiveConsole.raw_input()`](3.标准库/code.md#code.InteractiveConsole.raw_input)
  * [`codeop` \--- 编译Python代码](codeop.md)
    * [`compile_command()`](codeop.md#codeop.compile_command)
    * [`Compile`](codeop.md#codeop.Compile)
    * [`CommandCompiler`](codeop.md#codeop.CommandCompiler)

