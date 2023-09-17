# `token` \--- 与Python解析树一起使用的常量¶

**源码：** [Lib/token.py](https://github.com/python/cpython/tree/3.12/Lib/token.py)

* * *

该模块提供了一些代表解析树的叶子节点的数字值的常量（终端形符）。 请参阅 Python 发布版中的 `Grammar/Tokens` 文件获取在该语言语法情境下的名称定义。 这些名称所映射的特定数字值有可能在各 Python 版本间发生变化。

该模块还提供从数字代码到名称和一些函数的映射。 这些函数镜像了 Python C 头文件中的定义。

token.tok_name¶

    

~~~
将此模块中定义的常量的数值映射回名称字符串的字典，允许生成更加人类可读的解析树表示。

token.ISTERMINAL( _x_ )¶
~~~
    

~~~
对终端形符值返回 `True`。

token.ISNONTERMINAL( _x_ )¶
~~~
    

~~~
对非终端形符值返回 `True`。

token.ISEOF( _x_ )¶
~~~
    

~~~
如果 _x_ 是表示输入结束的标记则返回 `True`。

形符常量有：

token.ENDMARKER¶
~~~
    

~~~
token.NAME¶
~~~
    

~~~
token.NUMBER¶
~~~
    

~~~
token.STRING¶
~~~
    

~~~
token.NEWLINE¶
~~~
    

~~~
token.INDENT¶
~~~
    

~~~
token.DEDENT¶
~~~
    

~~~
token.LPAR¶
~~~
    

~~~
`"("` 的形符值。

token.RPAR¶
~~~
    

~~~
`")"` 的形符值。

token.LSQB¶
~~~
    

~~~
`"["` 的形符值。

token.RSQB¶
~~~
    

~~~
`"]"` 的形符值。

token.COLON¶
~~~
    

~~~
`":"` 的形符值。

token.COMMA¶
~~~
    

~~~
`","` 的形符值。

token.SEMI¶
~~~
    

~~~
`";"` 的形符值。

token.PLUS¶
~~~
    

~~~
`"+"` 的形符值。

token.MINUS¶
~~~
    

~~~
`"-"` 的形符值。

token.STAR¶
~~~
    

~~~
`"*"` 的形符值。

token.SLASH¶
~~~
    

~~~
`"/"` 的形符值。

token.VBAR¶
~~~
    

~~~
`"|"` 的形符值。

token.AMPER¶
~~~
    

~~~
`"&"` 的形符值。

token.LESS¶
~~~
    

~~~
`"<"` 的形符值。

token.GREATER¶
~~~
    

~~~
`">"` 的形符值。

token.EQUAL¶
~~~
    

~~~
`"="` 的形符值。

token.DOT¶
~~~
    

~~~
`"."` 的形符值。

token.PERCENT¶
~~~
    

~~~
`"%"` 的形符值。

token.LBRACE¶
~~~
    

~~~
Token value for `"{"`.

token.RBRACE¶
~~~
    

~~~
`"}"` 的形符值。

token.EQEQUAL¶
~~~
    

~~~
`"=="` 的形符值。

token.NOTEQUAL¶
~~~
    

~~~
`"!="` 的形符值。

token.LESSEQUAL¶
~~~
    

~~~
`"<="` 的形符值。

token.GREATEREQUAL¶
~~~
    

~~~
`">="` 的形符值。

token.TILDE¶
~~~
    

~~~
`"~"` 的形符值。

token.CIRCUMFLEX¶
~~~
    

~~~
`"^"` 的形符值。

token.LEFTSHIFT¶
~~~
    

~~~
`"<<"` 的形符值。

token.RIGHTSHIFT¶
~~~
    

~~~
`">>"` 的形符值。

token.DOUBLESTAR¶
~~~
    

~~~
`"**"` 的形符值。

token.PLUSEQUAL¶
~~~
    

~~~
`"+="` 的形符值。

token.MINEQUAL¶
~~~
    

~~~
`"-="` 的形符值。

token.STAREQUAL¶
~~~
    

~~~
`"*="` 的形符值。

token.SLASHEQUAL¶
~~~
    

~~~
`"/="` 的形符值。

token.PERCENTEQUAL¶
~~~
    

~~~
`"%="` 的形符值。

token.AMPEREQUAL¶
~~~
    

~~~
`"&="` 的形符值。

token.VBAREQUAL¶
~~~
    

~~~
`"|="` 的形符值。

token.CIRCUMFLEXEQUAL¶
~~~
    

~~~
`"^="` 的形符值。

token.LEFTSHIFTEQUAL¶
~~~
    

~~~
`"<<="` 的形符值。

token.RIGHTSHIFTEQUAL¶
~~~
    

~~~
`">>="` 的形符值。

token.DOUBLESTAREQUAL¶
~~~
    

~~~
`"**="` 的形符值。

token.DOUBLESLASH¶
~~~
    

~~~
`"//"` 的形符值。

token.DOUBLESLASHEQUAL¶
~~~
    

~~~
`"//="` 的形符值。

token.AT¶
~~~
    

~~~
`"@"` 的形符值。

token.ATEQUAL¶
~~~
    

~~~
`"@="` 的形符值。

token.RARROW¶
~~~
    

~~~
`"->"` 的形符值。

token.ELLIPSIS¶
~~~
    

~~~
`"..."` 的形符值。

token.COLONEQUAL¶
~~~
    

~~~
`":="` 的形符值。

token.EXCLAMATION¶
~~~
    

~~~
`"!"` 的形符值。

token.OP¶
~~~
    

~~~
token.TYPE_IGNORE¶
~~~
    

~~~
token.TYPE_COMMENT¶
~~~
    

~~~
token.SOFT_KEYWORD¶
~~~
    

~~~
token.FSTRING_START¶
~~~
    

~~~
token.FSTRING_MIDDLE¶
~~~
    

~~~
token.FSTRING_END¶
~~~
    

~~~
token.COMMENT¶
~~~
    

~~~
token.NL¶
~~~
    

~~~
token.ERRORTOKEN¶
~~~
    

~~~
token.N_TOKENS¶
~~~
    

~~~
token.NT_OFFSET¶
~~~
    

~~~
C 形符生成器不使用以下形符类型值，但 [`tokenize`](tokenize.md#module-tokenize "tokenize: Lexical scanner for Python source code.") 模块需要它们。

token.COMMENT
~~~
    

~~~
形符值用于表示注释。

token.NL
~~~
    

~~~
形符值用于表示非终止换行符。 `NEWLINE` 形符表示 Python 代码逻辑行的结束；当在多条物理线路上继续执行逻辑代码行时，会生成 `NL` 形符。

token.ENCODING¶
~~~
    

~~~
指示用于将源字节解码为文本的编码的形符值。 [`tokenize.tokenize()`](tokenize.md#tokenize.tokenize "tokenize.tokenize") 返回的第一个形符将始终是一个 `ENCODING` 形符。

token.TYPE_COMMENT
~~~
    

~~~
