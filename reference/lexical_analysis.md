# 2\. 词法分析¶

Python 程序由 _解析器_ 读取，输入解析器的是 _词法分析器_ 生成的 _形符_ 流。本章介绍词法分析器怎样把文件拆成形符。

Python 将读取的程序文本转为 Unicode 代码点；编码声明用于指定源文件的编码，默认为 UTF-8，详见 [**PEP 3120**](https://peps.python.org/pep-3120/)。源文件不能解码时，触发 [`SyntaxError`](../library/exceptions.md#SyntaxError "SyntaxError")。

## 2.1. 行结构¶

Python 程序可以拆分为多个 _逻辑行_ 。

### 2.1.1. 逻辑行¶

NEWLINE 形符表示结束逻辑行。语句不能超出逻辑行的边界，除非句法支持 NEWLINE （例如，复合语句中的多行子语句）。根据显式或隐式 _行拼接_ 规则，一个或多个 _物理行_ 可组成逻辑行。

### 2.1.2. 物理行¶

物理行是一序列字符，由行尾序列终止。源文件和字符串可使用任意标准平台行终止序列 - Unix ASCII 字符 LF （换行）、 Windows ASCII 字符序列 CR LF （回车换行）、或老式 Macintosh ASCII 字符 CR （回车）。不管在哪个平台，这些形式均可等价使用。输入结束也可以用作最终物理行的隐式终止符。

嵌入 Python 时，传入 Python API 的源码字符串应使用 C 标准惯例换行符（`\n`，代表 ASCII 字符 LF， 行终止符）。

### 2.1.3. 注释¶

注释以井号 （`#`） 开头，在物理行末尾截止。注意，井号不是字符串字面值。除非应用隐式行拼接规则，否则，注释代表逻辑行结束。句法不解析注释。

### 2.1.4. 编码声明¶

Python 脚本第一或第二行的注释匹配正则表达式 `coding[=:]\s*([-\w.]+)` 时，该注释会被当作编码声明；这个表达式的第一组指定了源码文件的编码。编码声明必须独占一行，在第二行时，则第一行必须也是注释。编码表达式的形式如下：

    
    
~~~
# -*- coding: <encoding-name> -*-
~~~

这也是 GNU Emacs 认可的形式，此外，还支持如下形式：

    
    
~~~
# vim:fileencoding=<encoding-name>
~~~

这是 Bram Moolenaar 的 VIM 认可的形式。

没有编码声明时，默认编码为 UTF-8。此外，如果文件的首字节为 UTF-8 字节顺序标志（`b'\xef\xbb\xbf'`），文件编码也声明为 UTF-8（这是 Microsoft 的 **notepad** 等软件支持的形式）。

如果声明了编码格式，该编码格式的名称必须是 Python 可识别的 (参见 [标准编码](../library/codecs.md#standard-encodings))。 编码格式会被用于所有的词法分析，包括字符串字面值、注释和标识符等。

### 2.1.5. 显式拼接行¶

两个及两个以上的物理行可用反斜杠（`\`）拼接为一个逻辑行，规则如下：以不在字符串或注释内的反斜杠结尾时，物理行将与下一行拼接成一个逻辑行，并删除反斜杠及其后的换行符。例如：

    
    
~~~
if 1900 < year < 2100 and 1 <= month <= 12 \
   and 1 <= day <= 31 and 0 <= hour < 24 \
   and 0 <= minute < 60 and 0 <= second < 60:   # Looks like a valid date
        return 1
~~~

以反斜杠结尾的行，不能加注释；反斜杠也不能拼接注释。除字符串字面值外，反斜杠不能拼接形符（如，除字符串字面值外，不能用反斜杠把形符切分至两个物理行）。反斜杠只能在代码的字符串字面值里，在其他任何位置都是非法的。

### 2.1.6. 隐式拼接行¶

圆括号、方括号、花括号内的表达式可以分成多个物理行，不必使用反斜杠。例如：

    
    
~~~
month_names = ['Januari', 'Februari', 'Maart',      # These are the
               'April',   'Mei',      'Juni',       # Dutch names
               'Juli',    'Augustus', 'September',  # for the months
               'Oktober', 'November', 'December']   # of the year
~~~

隐式行拼接可含注释；后续行的缩进并不重要；还支持空的后续行。隐式拼接行之间没有 NEWLINE 形符。三引号字符串支持隐式拼接行（见下文），但不支持注释。

### 2.1.7. 空白行¶

只包含空格符、制表符、换页符、注释的逻辑行会被忽略（即不生成 NEWLINE 形符）。交互模式输入语句时，空白行的处理方式可能因读取 - 求值 - 打印循环（REPL）的具体实现方式而不同。标准交互模式解释器中，完全空白的逻辑行（即连空格或注释都没有）将结束多行复合语句。

### 2.1.8. 缩进¶

逻辑行开头的空白符（空格符和制表符）用于计算该行的缩进层级，决定语句组块。

制表符（从左至右）被替换为一至八个空格，缩进空格的总数是八的倍数（与 Unix 的规则保持一致）。首个非空字符前的空格数决定了该行的缩进层次。缩进不能用反斜杠进行多行拼接；首个反斜杠之前的空白符决定了缩进的层次。

源文件混用制表符和空格符缩进时，因空格数量与制表符相关，由此产生的不一致将导致不能正常识别缩进层次，从而触发 [`TabError`](../library/exceptions.md#TabError "TabError")。

**跨平台兼容性说明：** 鉴于非 UNIX 平台文本编辑器本身的特性，请勿在源文件中混用制表符和空格符。另外也请注意，不同平台有可能会显式限制最大缩进层级。

行首含换页符时，缩进计算将忽略该换页符。换页符在行首空白符内其他位置的效果未定义（例如，可能导致空格计数重置为零）。

连续行的缩进层级以堆栈形式生成 INDENT 和 DEDENT 形符，说明如下。

读取文件第一行前，先向栈推入一个零值，该零值不会被移除。推入栈的层级值从底至顶持续增加。每个逻辑行开头的行缩进层级将与栈顶行比较。如果相等，则不做处理。如果新行层级较高，则会被推入栈顶，并生成一个 INDENT 形符。如果新行层级较低，则 _应当_ 是栈中的层级数值之一；栈中高于该层级的所有数值都将被移除，每移除一级数值生成一个 DEDENT 形符。文件末尾，栈中剩余的每个大于零的数值生成一个 DEDENT 形符。

下面的 Python 代码缩进示例虽然正确，但含混不清：

    
    
~~~python
def perm(l):
        # Compute the list of all permutations of l
    if len(l) <= 1:
                  return [l]
    r = []
    for i in range(len(l)):
             s = l[:i] + l[i+1:]
             p = perm(s)
             for x in p:
              r.append(l[i:i+1] + x)
    return r
~~~

下例展示了多种缩进错误：

    
    
~~~python
 def perm(l):                       # error: first line indented
for i in range(len(l)):             # error: not indented
    s = l[:i] + l[i+1:]
        p = perm(l[:i] + l[i+1:])   # error: unexpected indent
        for x in p:
                r.append(l[i:i+1] + x)
            return r                # error: inconsistent dedent
~~~

（实际上，解析器可以识别前三个错误；只有最后一个错误由词法分析器识别 --- `return r` 的缩进无法匹配从栈里移除的缩进层级。）

### 2.1.9. 形符间的空白字符¶

除非在逻辑行开头或字符串内，空格符、制表符、换页符等空白符都可以分隔形符。要把两个相连形符解读为不同形符，需要用空白符分隔（例如，ab 是一个形符，a b 则是两个形符）。

## 2.2. 其他形符¶

除 NEWLINE、INDENT、DEDENT 外，还有 _标识符_ 、 _关键字_ 、 _字面值_ 、 _运算符_ 、 _分隔符_ 等形符。 空白符（前述的行终止符除外）不是形符，可用于分隔形符。存在二义性时，将从左至右，读取尽量长的字符串组成合法形符。

## 2.3. 标识符和关键字¶

标识符（也称为 _名称_ ）的词法定义说明如下。

Python 标识符的句法基于 Unicode 标准附件 UAX-31，并加入了下文定义的细化与修改；详见 [**PEP 3131**](https://peps.python.org/pep-3131/) 。

与 Python 2.x 一样，在 ASCII 范围内（U+0001..U+007F），有效标识符字符为： 大小写字母 `A` 至 `Z`、下划线 `_` 、数字 `0` 至 `9`，但不能以数字开头。

Python 3.0 引入了 ASCII 之外的更多字符（请参阅 [**PEP 3131**](https://peps.python.org/pep-3131/)）。这些字符的分类使用 [`unicodedata`](../library/unicodedata.md#module-unicodedata "unicodedata: Access the Unicode Database.") 模块中的 Unicode 字符数据库版本。

标识符的长度没有限制，但区分大小写。

    
    
~~~
**identifier** ::=  xid_start xid_continue*
**id_start** ::=  <all characters in general categories Lu, Ll, Lt, Lm, Lo, Nl, the underscore, and characters with the Other_ID_Start property>
**id_continue** ::=  <all characters in id_start, plus characters in the categories Mn, Mc, Nd, Pc and others with the Other_ID_Continue property>
**xid_start** ::=  <all characters in id_start whose NFKC normalization is in "id_start xid_continue*">
**xid_continue** ::=  <all characters in id_continue whose NFKC normalization is in "id_continue*">
~~~

上述 Unicode 类别码的含义:

  * _Lu_ \- 大写字母

  * _Ll_ \- 小写字母

  * _Lt_ \- 词首大写字母

  * _Lm_ \- 修饰符字母

  * _Lo_ \- 其他字母

  * _Nl_ \- 字母数字

  * _Mn_ \- 非空白标识

  * _Mc_ \- 含空白标识

  * _Nd_ \- 十进制数字

  * _Pc_ \- 连接标点

  * _Other_ID_Start_ \- 在 [PropList.txt](https://www.unicode.org/Public/15.0.0/ucd/PropList.txt) 中显式定义的用于支持向下兼容的字符列表。

  * _Other_ID_Continue_ \- 同上

在解析时，所有标识符都会被转换为规范形式 NFKC；标识符的比较都是基于 NFKC。

一份列出 Unicode 15.0.0 中所有可用标识符字符的非规范 HTML 文件可在 <https://www.unicode.org/Public/15.0.0/ucd/DerivedCoreProperties.txt> 获取

### 2.3.1. 关键字¶

以下标识符为保留字，或称 _关键字_ ，不可用于普通标识符。关键字的拼写必须与这里列出的完全一致：

    
    
~~~
False      await      else       import     pass
None       break      except     in         raise
True       class      finally    is         return
and        continue   for        lambda     try
as         def        from       nonlocal   while
assert     del        global     not        with
async      elif       if         or         yield
~~~

### 2.3.2. 软关键字¶

在 3.10 版本加入.

某些标识符仅在特定上下文中被保留。 它们被称为 _软关键字_ 。 `match`, `case`, `type` 和 `_` 等标识符在特定上下文中具有关键字的语义，但这种区分是在解析器层级完成的，而不是在分词的时候。

作为软关键字，它们能够在用于相应语法的同时仍然保持与用作标识符名称的现有代码的兼容性。

`match`, `case` 和 `_` 是在 [`match`](compound_stmts.md#match) 语句中使用。 `type` 是在 [`type`](simple_stmts.md#type) 语句中使用。

在 3.12 版本发生变更: `type` 现在是一个软关键字。

### 2.3.3. 保留的标识符类¶

某些标识符类（除了关键字）具有特殊含义。这些类的命名模式以下划线字符开头，并以下划线结尾：

`_*`

    

~~~
不会被 `from module import *` 所导入。

`_`
~~~
    

~~~
在 [`match`](compound_stmts.md#match) 语句内部的 `case` 模式中，`_` 是一个 软关键字，它表示 [通配符](compound_stmts.md#wildcard-patterns)。

在此之外，交互式解释器会将最后一次求值的结果放到变量 `_` 中。 （它与 `print` 等内置函数一起被存储于 [`builtins`](../library/builtins.md#module-builtins "builtins: The module that provides the built-in namespace.") 模块。）

在其他地方，`_` 是一个常规标识符。 它常常被用来命名 "特殊" 条目，但对 Python 本身来说毫无特殊之处。

备注

`_` 常用于连接国际化文本；详见 [`gettext`](../library/gettext.md#module-gettext "gettext: Multilingual internationalization services.") 模块文档。

它还经常被用来命名无需使用的变量。

`__*__`
~~~
    

~~~
系统定义的名称，通常简称为 "dunder" 。这些名称由解释器及其实现（包括标准库）定义。现有系统定义名称相关的论述详见 [特殊方法名称](datamodel.md#specialnames) 等章节。Python 未来版本中还将定义更多此类名称。任何情况下， _任何_ 不显式遵从 `__*__` 名称的文档用法，都可能导致无警告提示的错误。

`__*`
~~~
    

~~~
类的私有名称。类定义时，此类名称以一种混合形式重写，以避免基类及派生类的 "私有" 属性之间产生名称冲突。详见 [标识符（名称）](expressions.md#atom-identifiers)。

## 2.4. 字面值¶

字面值是内置类型常量值的表示法。

### 2.4.1. 字符串与字节串字面值¶

字符串字面值的词法定义如下：
~~~
    
    
~~~
**stringliteral** ::=  [stringprefix](shortstring | longstring)
**stringprefix** ::=  "r" | "u" | "R" | "U" | "f" | "F"
                     | "fr" | "Fr" | "fR" | "FR" | "rf" | "rF" | "Rf" | "RF"
**shortstring** ::=  "'" shortstringitem* "'" | '"' shortstringitem* '"'
**longstring** ::=  "'''" longstringitem* "'''" | '"""' longstringitem* '"""'
**shortstringitem** ::=  shortstringchar | stringescapeseq
**longstringitem** ::=  longstringchar | stringescapeseq
**shortstringchar** ::=  <any source character except "\" or newline or the quote>
**longstringchar** ::=  <any source character except "\">
**stringescapeseq** ::=  "\" <any source character>
~~~
    
    
~~~
**bytesliteral** ::=  bytesprefix(shortbytes | longbytes)
**bytesprefix** ::=  "b" | "B" | "br" | "Br" | "bR" | "BR" | "rb" | "rB" | "Rb" | "RB"
**shortbytes** ::=  "'" shortbytesitem* "'" | '"' shortbytesitem* '"'
**longbytes** ::=  "'''" longbytesitem* "'''" | '"""' longbytesitem* '"""'
**shortbytesitem** ::=  shortbyteschar | bytesescapeseq
**longbytesitem** ::=  longbyteschar | bytesescapeseq
**shortbyteschar** ::=  <any ASCII character except "\" or newline or the quote>
**longbyteschar** ::=  <any ASCII character except "\">
**bytesescapeseq** ::=  "\" <any ASCII character>
~~~

这些产生式未指明的一个句法限制是空白符不允许在 `stringprefix` 或 `bytesprefix` 与字面值的其余部分之间出现。 源字符集是由编码格式声明来定义的；如果源文件没有给出编码格式声明则默认 UTF-8；参见 编码声明 一节。

直白的说明：两种类型的字面值都可用成对的单引号 (`'`) 或双引号 (`"`) 括起来。 它们还可以用成对的连续三个单引号或双引号括起来 (这通常被称为 _三重引号字符串_ )。 反斜杠 (`\`) 字符被用来给予普通的字符特殊含义例如 `n`，当用斜杠转义时 (`\n`) 表示 '换行'。 它还可以被用来对具有特殊含义的字符进行转义，例如换行符、反斜杠本身或者引号等。 请参阅下面的 转义序列 查看示例。

字节串字面值要加前缀 `'b'` 或 `'B'`；生成的是类型 [`bytes`](../library/stdtypes.md#bytes "bytes") 的实例，不是类型 [`str`](../library/stdtypes.md#str "str") 的实例；字节串只能包含 ASCII 字符；字节串数值大于等于 128 时，必须用转义表示。

字符串和字节串都可以加前缀 `'r'` 或 `'R'`，称为 _原始字符串_ ，原始字符串把反斜杠当作原义字符，不执行转义操作。因此，原始字符串不转义 `'\U'` 和 `'\u'`。与 Python 2.x 的原始 unicode 字面值操作不同，Python 3.x 现已不支持 `'ur'` 句法。

在 3.3 版本加入: 新增原始字节串 `'rb'` 前缀，是 `'br'` 的同义词。

在 3.3 版本加入: 支持 unicode 字面值（`u'value'`）遗留代码，简化 Python 2.x 和 3.x 并行代码库的维护工作。详见 [**PEP 414**](https://peps.python.org/pep-0414/)。

前缀为 `'f'` 或 `'F'` 的字符串称为 _格式字符串_ ；详见 格式字符串字面值。`'f'` 可与 `'r'` 连用，但不能与 `'b'` 或 `'u'` 连用，因此，可以使用原始格式字符串，但不能使用格式字节串字面值。

三引号字面值可以包含未转义的换行和引号（原样保留），除了连在一起的，用于终止字面值的，未经转义的三个引号。（"引号" 是启用字面值的字符，可以是 `'`，也可以是 `"`。）

#### 2.4.1.1. 转义序列¶

如未标注 `'r'` 或 `'R'` 前缀，字符串和字节串字面值中，转义序列以类似 C 标准的规则进行解释。可用的转义序列如下：

转义序列

|

含意

|

备注  
  
---|---|---  
  
`\`<newline>

|

忽略反斜杠与换行符

|

(1)  
  
`\\`

|

反斜杠（`\`）

|  
  
`\'`

|

单引号（`'`）

|  
  
`\"`

|

双引号（`"`）

|  
  
`\a`

|

ASCII 响铃（BEL）

|  
  
`\b`

|

ASCII 退格符（BS）

|  
  
`\f`

|

ASCII 换页符（FF）

|  
  
`\n`

|

ASCII 换行符（LF）

|  
  
`\r`

|

ASCII 回车符（CR）

|  
  
`\t`

|

ASCII 水平制表符（TAB）

|  
  
`\v`

|

ASCII 垂直制表符（VT）

|  
  
`\ooo`

|

八进制数 _ooo_ 字符

|

(2,4)  
  
`\xhh`

|

十六进制数 _hh_ 字符

|

(3,4)  
  
字符串字面值专用的转义序列：

转义序列

|

含意

|

备注  
  
---|---|---  
  
`\N{name}`

|

Unicode 数据库中名为 _name_ 的字符

|

(5)  
  
`\uxxxx`

|

16 位十六进制数 _xxxx_ 码位的字符

|

(6)  
  
`\Uxxxxxxxx`

|

32 位 16 进制数 _xxxxxxxx_ 码位的字符

|

(7)  
  
注释：

  1. 可以在行尾添加一个反斜杠来忽略换行符:
    
        >>> 'This string will not include \
    ... backslashes or newline characters.'
    'This string will not include backslashes or newline characters.'
    

同样的效果也可以使用 三重引号字符串，或者圆括号和 字符串字面值拼接 来达成。

  2. 与 C 标准一致，接受最多三个八进制数字。

在 3.11 版本发生变更: 取值大于 `0o377` 的八进制数转义序列会产生 [`DeprecationWarning`](../library/exceptions.md#DeprecationWarning "DeprecationWarning")。

在 3.12 版本发生变更: 数值大于 `0o377` 的八进制转义符会产生 [`SyntaxWarning`](../library/exceptions.md#SyntaxWarning "SyntaxWarning")。 在未来的 Python 版本中将最终改为 [`SyntaxError`](../library/exceptions.md#SyntaxError "SyntaxError")。

  3. 与 C 标准不同，必须为两个十六进制数字。

  4. _字节串_ 字面值中，十六进制数和八进制数的转义码以相应数值代表每个字节。 _字符串_ 字面值中，这些转义码以相应数值代表每个 Unicode 字符。

  5. 在 3.3 版本发生变更: 加入了对别名 [1] 的支持。

  6. 必须为 4 个十六进制数码。

  7. 表示任意 Unicode 字符。必须为 8 个十六进制数码。

与 C 标准不同，无法识别的转义序列在字符串里原样保留，即， _输出结果保留反斜杠_ 。（调试时，这种方式很有用：输错转义序列时，更容易在输出结果中识别错误。）注意，在字节串字面值内，字符串字面值专用的转义序列属于无法识别的转义序列。

> 在 3.6 版本发生变更: 不可识别的转义序列会产生 [`DeprecationWarning`](../library/exceptions.md#DeprecationWarning "DeprecationWarning")。
>
> 在 3.12 版本发生变更: 不可识别的转义序列会产生 [`SyntaxWarning`](../library/exceptions.md#SyntaxWarning "SyntaxWarning")。 在未来的 Python 版本中将最终改为 [`SyntaxError`](../library/exceptions.md#SyntaxError "SyntaxError")。

即使在原始字面值中，引号也可以用反斜杠转义，但反斜杠会保留在输出结果里；例如 `r"\""` 是由两个字符组成的有效字符串字面值：反斜杠和双引号；`r"\"` 则不是有效字符串字面值（原始字符串也不能以奇数个反斜杠结尾）。尤其是， _原始字面值不能以单个反斜杠结尾_ （反斜杠会转义其后的引号)。还要注意，反斜杠加换行在字面值中被解释为两个字符，而 _不是_ 连续行。

### 2.4.2. 字符串字面值合并¶

以空白符分隔的多个相邻字符串或字节串字面值，可用不同引号标注，等同于合并操作。因此，`"hello" 'world'` 等价于 `"helloworld"`。此功能不需要反斜杠，即可将长字符串分为多个物理行，还可以为不同部分的字符串添加注释，例如：

    
    
~~~
re.compile("[A-Za-z_]"       # letter or underscore
           "[A-Za-z0-9_]*"   # letter, digit or underscore
          )
~~~

注意，此功能在句法层面定义，在编译时实现。在运行时，合并字符串表达式必须使用 '+' 运算符。还要注意，字面值合并可以为每个部分应用不同的引号风格（甚至混用原始字符串和三引号字符串)，格式字符串字面值也可以与纯字符串字面值合并。

### 2.4.3. 格式字符串字面值¶

在 3.6 版本加入.

_格式字符串字面值_ 或称 _f-string_ 是标注了 `'f'` 或 `'F'` 前缀的字符串字面值。这种字符串可包含替换字段，即以 `{}` 标注的表达式。其他字符串字面值只是常量，格式字符串字面值则是可在运行时求值的表达式。

除非字面值标记为原始字符串，否则，与在普通字符串字面值中一样，转义序列也会被解码。解码后，用于字符串内容的语法如下：

    
    
~~~
**f_string** ::=  (literal_char | "{{" | "}}" | replacement_field)*
**replacement_field** ::=  "{" f_expression ["="] ["!" conversion] [":" format_spec] "}"
**f_expression** ::=  ([conditional_expression](expressions.md#grammar-token-python-grammar-conditional_expression) | "*" [or_expr](expressions.md#grammar-token-python-grammar-or_expr))
                         ("," [conditional_expression](expressions.md#grammar-token-python-grammar-conditional_expression) | "," "*" [or_expr](expressions.md#grammar-token-python-grammar-or_expr))* [","]
                       | [yield_expression](expressions.md#grammar-token-python-grammar-yield_expression)
**conversion** ::=  "s" | "r" | "a"
**format_spec** ::=  (literal_char | NULL | replacement_field)*
**literal_char** ::=  <any code point except "{", "}" or NULL>
~~~

双花括号 `'{{'` 或 `'}}'` 被替换为单花括号，花括号外的字符串仍按字面值处理。单左花括号 `'{'` 标记以 Python 表达式开头的替换字段。在表达式后加等于号 `'='`，可在求值后，同时显示表达式文本及其结果（用于调试）。 随后是用叹号 `'!'` 标记的转换字段。还可以在冒号 `':'` 后附加格式说明符。替换字段以右花括号 `'}'` 为结尾。

格式化字符串字面值中的表达式会与用圆括号包围的常规 Python 表达式一样处理，但有少量例外。 空表达式是不被允许的，而 [`lambda`](expressions.md#lambda) 和赋值表达式 `:=` 都必须显式地用括号包围。 每个表达式都将在格式化字符串字面值出现的上下文中按从左到右的顺序进行求值。 替换表达式可在单引号和三引号f-字符串中包含换行符并可包含注释。 替换字段内 `#` 后面的所有内容都是注释（即使结尾花括号和引号也是）。 在这种情况下，替换字段必须在另一行中结束。

    
    
~~~shell
>>> f"abc{a # This is a comment }"
... + 3}"
'abc5'
~~~

在 3.7 版本发生变更: Python 3.7 以前， 因为实现的问题，不允许在格式字符串字面值表达式中使用 [`await`](expressions.md#await) 表达式与包含 [`async for`](compound_stmts.md#async-for) 子句的推导式。

在 3.12 版本发生变更: 在 Python 3.12 之前，不允许在 f-字符串的替换字段中使用注释。

表达式里含等号 `'='` 时，输出内容包括表达式文本、`'='` 、求值结果。输出内容可以保留表达式中左花括号 `'{'` 后，及 `'='` 后的空格。没有指定格式时，`'='` 默认调用表达式的 [`repr()`](../library/functions.md#repr "repr")。指定了格式时，默认调用表达式的 [`str()`](../library/stdtypes.md#str "str")，除非声明了转换字段 `'!r'`。

在 3.8 版本加入: 等号 `'='`。

指定了转换符时，表达式求值的结果会先转换，再格式化。转换符 `'!s'` 调用 [`str()`](../library/stdtypes.md#str "str") 转换求值结果，`'!r'` 调用 [`repr()`](../library/functions.md#repr "repr")，`'!a'` 调用 [`ascii()`](../library/functions.md#ascii "ascii")。

然后使用 [`format()`](../library/functions.md#format "format") 协议对结果进行格式化。 格式说明符将传给表达式或转换结果的 [`__format__()`](datamodel.md#object.__format__ "object.__format__") 方法。 如果省略格式说明符则将传入空字符串。 格式化后的结果将包括在整个字符串的最终值中。

最高层级的格式说明符可以包括嵌套的替换字段。 这些嵌套字段也可以包括它们自己的转换字段和 [格式说明符](../library/string.md#formatspec)，但是不可再包括更深层嵌套的替换字段。 这里的 [格式说明符微语言](../library/string.md#formatspec) 与 [`str.format()`](../library/stdtypes.md#str.format "str.format") 方法所使用的相同。

格式化字符串字面值可以拼接，但是一个替换字段不能拆分到多个字面值。

格式字符串字面值示例如下：

    
    
~~~shell
>>> name = "Fred"
>>> f"He said his name is {name!r}."
"He said his name is 'Fred'."
>>> f"He said his name is {repr(name)}."  # repr() is equivalent to !r
"He said his name is 'Fred'."
>>> width = 10
>>> precision = 4
>>> value = decimal.Decimal("12.34567")
>>> f"result: {value:{width}.{precision}}"  # nested fields
'result:      12.35'
>>> today = datetime(year=2017, month=1, day=27)
>>> f"{today:%B %d, %Y}"  # using date format specifier
'January 27, 2017'
>>> f"{today=:%B %d, %Y}" # using date format specifier and debugging
'today=January 27, 2017'
>>> number = 1024
>>> f"{number:#0x}"  # using integer format specifier
'0x400'
>>> foo = "bar"
>>> f"{ foo = }" # preserves whitespace
" foo = 'bar'"
>>> line = "The mill's closed"
>>> f"{line = }"
'line = "The mill\'s closed"'
>>> f"{line = :20}"
"line = The mill's closed   "
>>> f"{line = !r:20}"
'line = "The mill\'s closed" '
~~~

允许在替换字段中重用外层 f-字符串的引号类型:

    
    
~~~shell
>>> a = dict(x=2)
>>> f"abc {a["x"]} def"
'abc 2 def'
~~~

在 3.12 版本发生变更: 在 Python 3.12 之前不允许在替换字段中重用与外层 f-字符串相同的引号类型。

替换字段中也允许使用反斜杠并会以与在其他场景下相同的方式求值:

    
    
~~~shell
>>> a = ["a", "b", "c"]
>>> print(f"List a contains:\n{"\n".join(a)}")
List a contains:
a
b
c
~~~

在 3.12 版本发生变更: 在 Python 3.12 之前，f-字符串的替换字段内不允许使用反斜杠。

即便未包含表达式，格式字符串字面值也不能用作文档字符串。

    
    
~~~shell
>>> def foo():
...     f"Not a docstring"
...
>>> foo.__doc__ is None
True
~~~

参阅 [**PEP 498**](https://peps.python.org/pep-0498/)，了解格式字符串字面值的提案，以及与格式字符串机制相关的 [`str.format()`](../library/stdtypes.md#str.format "str.format")。

### 2.4.4. 数值字面值¶

数值字面值有三种类型：整数、浮点数、虚数。没有复数字面值（复数由实数加虚数构成）。

注意，数值字面值不含正负号；实际上，`-1` 等负数是由一元运算符 '`-`' 和字面值 `1` 合成的。

### 2.4.5. 整数字面值¶

整数字面值词法定义如下：

    
    
~~~
**integer** ::=  decinteger | bininteger | octinteger | hexinteger
**decinteger** ::=  nonzerodigit (["_"] digit)* | "0"+ (["_"] "0")*
**bininteger** ::=  "0" ("b" | "B") (["_"] bindigit)+
**octinteger** ::=  "0" ("o" | "O") (["_"] octdigit)+
**hexinteger** ::=  "0" ("x" | "X") (["_"] hexdigit)+
**nonzerodigit** ::=  "1"..."9"
**digit** ::=  "0"..."9"
**bindigit** ::=  "0" | "1"
**octdigit** ::=  "0"..."7"
**hexdigit** ::=  digit | "a"..."f" | "A"..."F"
~~~

整数字面值的长度没有限制，能一直大到占满可用内存。

确定数值时，会忽略字面值中的下划线。下划线只是为了分组数字，让数字更易读。下划线可在数字之间，也可在 `0x` 等基数说明符后。

注意，除了 0 以外，十进制数字的开头不允许有零。以免与 Python 3.0 版之前使用的 C 样式八进制字面值混淆。

整数字面值示例如下：

    
    
~~~
7     2147483647                        0o177    0b100110111
3     79228162514264337593543950336     0o377    0xdeadbeef
      100_000_000_000                   0b_1110_0101
~~~

在 3.6 版本发生变更: 现已支持在字面值中，用下划线分组数字。

### 2.4.6. 浮点数字面值¶

浮点数字面值词法定义如下：

    
    
~~~
**floatnumber** ::=  pointfloat | exponentfloat
**pointfloat** ::=  [digitpart] fraction | digitpart "."
**exponentfloat** ::=  (digitpart | pointfloat) exponent
**digitpart** ::=  digit (["_"] digit)*
**fraction** ::=  "." digitpart
**exponent** ::=  ("e" | "E") ["+" | "-"] digitpart
~~~

注意，解析时，整数和指数部分总以 10 为基数。例如，`077e010` 是合法的，表示的数值与 `77e10` 相同。浮点数字面值的支持范围取决于具体实现。整数字面值支持用下划线分组数字。

浮点数字面值示例如下：

    
    
~~~
3.14    10.    .001    1e100    3.14e-10    0e0    3.14_15_93
~~~

在 3.6 版本发生变更: 现已支持在字面值中，用下划线分组数字。

### 2.4.7. 虚数字面值¶

虚数字面值词法定义如下：

    
    
~~~
**imagnumber** ::=  (floatnumber | digitpart) ("j" | "J")
~~~

虚数字面值生成实部为 0.0 的复数。复数由一对浮点数表示，它们的取值范围相同。创建实部不为零的复数，则需添加浮点数，例如 `(3+4j)`。虚数字面值示例如下：

    
    
~~~
3.14j   10.j    10j     .001j   1e100j   3.14e-10j   3.14_15_93j
~~~

## 2.5. 运算符¶

运算符如下所示：

    
    
~~~
+       -       *       **      /       //      %      @
<<      >>      &       |       ^       ~       :=
<       >       <=      >=      ==      !=
~~~

## 2.6. 分隔符¶

以下形符在语法中为分隔符：

    
    
~~~
(       )       [       ]       {       }
,       :       .       ;       @       =       ->
+=      -=      *=      /=      //=     %=      @=
&=      |=      ^=      >>=     <<=     **=
~~~

句点也可以用于浮点数和虚数字面值。三个连续句点表示省略符。列表后半部分是增强赋值操作符，用作词法分隔符，但也可以执行运算。

以下 ASCII 字符具有特殊含义，对词法分析器有重要意义：

    
    
~~~
'       "       #       \
~~~

以下 ASCII 字符不用于 Python。在字符串字面值或注释外使用时，将直接报错：

    
    
~~~
$       ?       `
~~~

备注

[1]

<https://www.unicode.org/Public/15.0.0/ucd/NameAliases.txt>

