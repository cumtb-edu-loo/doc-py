# Python 语言参考手册¶

本参考手册介绍了 Python 句法与“核心语义”。在力求简明扼要的同时，我们也尽量做到准确、完整。有关内置对象类型、内置函数、模块的语义在 [Python 标准库](../library/index.md#library-index) 中介绍。有关本语言的非正式介绍，请参阅 [Python 教程](../tutorial/index.md#tutorial-index) 。对于 C 或 C++ 程序员，我们还提供了两个手册：[扩展和嵌入 Python 解释器](../extending/index.md#extending-index) 介绍了如何编写 Python 扩展模块，[Python/C API 参考手册](../c-api/index.md#c-api-index) 则详细介绍了 C/C++ 的可用接口。

  * [1\. 概述](introduction.md)
    * [1.1. 其他实现](introduction.md#alternate-implementations)
    * [1.2. 标注](introduction.md#notation)
  * [2\. 词法分析](lexical_analysis.md)
    * [2.1. 行结构](lexical_analysis.md#line-structure)
    * [2.2. 其他形符](lexical_analysis.md#other-tokens)
    * [2.3. 标识符和关键字](lexical_analysis.md#identifiers)
    * [2.4. 字面值](lexical_analysis.md#literals)
    * [2.5. 运算符](lexical_analysis.md#operators)
    * [2.6. 分隔符](lexical_analysis.md#delimiters)
  * [3\. 数据模型](datamodel.md)
    * [3.1. 对象、值与类型](datamodel.md#objects-values-and-types)
    * [3.2. 标准类型层级结构](datamodel.md#the-standard-type-hierarchy)
    * [3.3. 特殊方法名称](datamodel.md#special-method-names)
    * [3.4. 协程](datamodel.md#coroutines)
  * [4\. 执行模型](executionmodel.md)
    * [4.1. 程序的结构](executionmodel.md#structure-of-a-program)
    * [4.2. 命名与绑定](executionmodel.md#naming-and-binding)
    * [4.3. 异常](executionmodel.md#exceptions)
  * [5\. 导入系统](import.md)
    * [5.1. `importlib`](import.md#importlib)
    * [5.2. 包](import.md#packages)
    * [5.3. 搜索](import.md#searching)
    * [5.4. 加载](import.md#loading)
    * [5.5. 基于路径的查找器](import.md#the-path-based-finder)
    * [5.6. 替换标准导入系统](import.md#replacing-the-standard-import-system)
    * [5.7. 包相对导入](import.md#package-relative-imports)
    * [5.8. 有关 __main__ 的特殊事项](import.md#special-considerations-for-main)
    * [5.9. 参考文献](import.md#references)
  * [6\. 表达式](expressions.md)
    * [6.1. 算术转换](expressions.md#arithmetic-conversions)
    * [6.2. 原子](expressions.md#atoms)
    * [6.3. 原型](expressions.md#primaries)
    * [6.4. await 表达式](expressions.md#await-expression)
    * [6.5. 幂运算符](expressions.md#the-power-operator)
    * [6.6. 一元算术和位运算](expressions.md#unary-arithmetic-and-bitwise-operations)
    * [6.7. 二元算术运算符](expressions.md#binary-arithmetic-operations)
    * [6.8. 移位运算](expressions.md#shifting-operations)
    * [6.9. 二元位运算](expressions.md#binary-bitwise-operations)
    * [6.10. 比较运算](expressions.md#comparisons)
    * [6.11. 布尔运算](expressions.md#boolean-operations)
    * [6.12. 赋值表达式](expressions.md#assignment-expressions)
    * [6.13. 条件表达式](expressions.md#conditional-expressions)
    * [6.14. lambda 表达式](expressions.md#lambda)
    * [6.15. 表达式列表](expressions.md#expression-lists)
    * [6.16. 求值顺序](expressions.md#evaluation-order)
    * [6.17. 运算符优先级](expressions.md#operator-precedence)
  * [7\. 简单语句](simple_stmts.md)
    * [7.1. 表达式语句](simple_stmts.md#expression-statements)
    * [7.2. 赋值语句](simple_stmts.md#assignment-statements)
    * [7.3. `assert` 语句](simple_stmts.md#the-assert-statement)
    * [7.4. `pass` 语句](simple_stmts.md#the-pass-statement)
    * [7.5. `del` 语句](simple_stmts.md#the-del-statement)
    * [7.6. `return` 语句](simple_stmts.md#the-return-statement)
    * [7.7. `yield` 语句](simple_stmts.md#the-yield-statement)
    * [7.8. `raise` 语句](simple_stmts.md#the-raise-statement)
    * [7.9. `break` 语句](simple_stmts.md#the-break-statement)
    * [7.10. `continue` 语句](simple_stmts.md#the-continue-statement)
    * [7.11. `import` 语句](simple_stmts.md#the-import-statement)
    * [7.12. `global` 语句](simple_stmts.md#the-global-statement)
    * [7.13. `nonlocal` 语句](simple_stmts.md#the-nonlocal-statement)
    * [7.14. `type` 语句](simple_stmts.md#the-type-statement)
  * [8\. 复合语句](compound_stmts.md)
    * [8.1. `if` 语句](compound_stmts.md#the-if-statement)
    * [8.2. `while` 语句](compound_stmts.md#the-while-statement)
    * [8.3. `for` 语句](compound_stmts.md#the-for-statement)
    * [8.4. `try` 语句](compound_stmts.md#the-try-statement)
    * [8.5. `with` 语句](compound_stmts.md#the-with-statement)
    * [8.6. `match` 语句](compound_stmts.md#the-match-statement)
    * [8.7. 函数定义](compound_stmts.md#function-definitions)
    * [8.8. 类定义](compound_stmts.md#class-definitions)
    * [8.9. 协程](compound_stmts.md#coroutines)
    * [8.10. 类型形参列表](compound_stmts.md#type-parameter-lists)
  * [9\. 顶级组件](toplevel_components.md)
    * [9.1. 完整的 Python 程序](toplevel_components.md#complete-python-programs)
    * [9.2. 文件输入](toplevel_components.md#file-input)
    * [9.3. 交互式输入](toplevel_components.md#interactive-input)
    * [9.4. 表达式输入](toplevel_components.md#expression-input)
  * [10\. 完整的语法规范](grammar.md)
