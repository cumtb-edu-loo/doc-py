# `re` \--- 正则表达式操作¶

**源代码:** [Lib/re/](https://github.com/python/cpython/tree/3.12/Lib/re/)

* * *

本模块提供了与 Perl 语言类似的正则表达式匹配操作。

模式和被搜索的字符串既可以是 Unicode 字符串 ([`str`](stdtypes.md#str "str")) ，也可以是8位字节串 ([`bytes`](stdtypes.md#bytes "bytes"))。 但是，Unicode 字符串与 8 位字节串不能混用：也就是说，不能用字节串模式匹配 Unicode 字符串，反之亦然；同理，替换操作时，替换字符串的类型也必须与所用的模式和搜索字符串的类型一致。

正则表达式使用反斜杠字符 (`'\'`) 表示特殊形式或是允许在使用特殊字符时不引发它们的特殊含义。 这会与 Python 在字符串字面值中对于相同字符出于相同目的规定的用法发生冲突；例如，要匹配一个反斜杠字面值，用户将必须写成 `'\\\\'` 因为正则表达式必须为 `\\`，而每个反斜杠在普通 Python 字符串字面值中又必须表示为 `\\`。 而且，还要注意在 Python 的字符串字面值中使用的反斜杠现在如果有任何无效的转义序列将会产生 [`SyntaxWarning`](exceptions.md#SyntaxWarning "SyntaxWarning") 并将在未来改为 [`SyntaxError`](exceptions.md#SyntaxError "SyntaxError")。 此行为即使对于正则表达式来说有效的转义字符同样会发生。

解决办法是对于正则表达式模式（patterns）使用 Python 的原始字符串表示法；在带有 `'r'` 前缀的字符串字面值中，反斜杠不必做任何特殊处理。 因此 `r"\n"` 表示包含 `'\'` 和 `'n'` 两个字符的字符串，而 `"\n"` 则表示只包含一个换行符的字符串。 模式在 Python 代码中通常都使用原始字符串表示法。

绝大多数正则表达式操作都提供为模块函数和方法，在 编译正则表达式. 这些函数是一个捷径，不需要先编译正则对象，但是损失了一些优化参数。

参见

第三方模块 [regex](https://pypi.org/project/regex/) , 提供了与标准库 `re` 模块兼容的 API 接口，同时，还提供了更多功能和更全面的 Unicode 支持。

## 正则表达式语法¶

正则表达式（或 RE）指定了一组与之匹配的字符串；模块内的函数可以检查某个字符串是否与给定的正则表达式匹配（或者正则表达式是否匹配到字符串，这两种说法含义相同）。

正则表达式可以拼接；如果 _A_ 和 _B_ 都是正则表达式，则 _AB_ 也是正则表达式。通常，如果字符串 _p_ 匹配 _A_ ，并且另一个字符串 _q_ 匹配 _B_ ，那么 _pq_ 可以匹配 AB。除非 _A_ 或者 _B_ 包含低优先级操作， _A_ 和 _B_ 存在边界条件；或者命名组引用。所以，复杂表达式可以很容易的从这里描述的简单源语表达式构建。更多正则表达式理论和实现，详见 the Friedl book [Frie09] ，或者其他构建编译器的书籍。

以下是正则表达式格式的简要说明。更详细的信息和演示，参考 [正则表达式指南](../howto/regex.md#regex-howto)。

正则表达式可以包含普通或者特殊字符。绝大部分普通字符，比如 `'A'`, `'a'`, 或者 `'0'`，都是最简单的正则表达式。它们就匹配自身。你可以拼接普通字符，所以 `last` 匹配字符串 `'last'`. （在这一节的其他部分，我们将用 `this special style` 这种方式表示正则表达式，通常不带引号，要匹配的字符串用 `'in single quotes'` ，单引号形式。）

有些字符，比如 `'|'` 或者 `'('`，属于特殊字符。 特殊字符既可以表示它的普通含义， 也可以影响它旁边的正则表达式的解释。

重复运算符或数量限定符 (`*`, `+`, `?`, `{m,n}` 等) 不能被直接嵌套。 这避免了非贪婪修饰符后缀 `?` 的歧义，也避免了其他实现中其他修饰符的歧义。 要将第二层重复应用到内层的重复中，可以使用圆括号。 例如，表达式 `(?:a{6})*` 将匹配六个 `'a'` 字符的任意多次重复。

特殊字符有：

`.`

    

~~~
(点号) 在默认模式下，匹配除换行符以外的任意字符。 如果指定了旗标 `DOTALL` ，它将匹配包括换行符在内的任意字符。

`^`
~~~
    

~~~
(插入符) 匹配字符串的开头， 并且在 `MULTILINE` 模式下也匹配换行后的首个符号。

`$`
~~~
    

~~~
匹配字符串尾或者在字符串尾的换行符的前一个字符，在 `MULTILINE` 模式下也会匹配换行符之前的文本。 `foo` 匹配 'foo' 和 'foobar'，但正则表达式 `foo$` 只匹配 'foo'。 更有趣的是，在 `'foo1\nfoo2\n'` 中搜索 `foo.$`，通常匹配 'foo2'，但在 `MULTILINE` 模式下可以匹配到 'foo1'；在 `'foo\n'` 中搜索 `$` 会找到两个（空的）匹配：一个在换行符之前，一个在字符串的末尾。

`*`
~~~
    

~~~
对它前面的正则式匹配0到任意次重复， 尽量多的匹配字符串。 `ab*` 会匹配 `'a'`，`'ab'`，或者 `'a'` 后面跟随任意个 `'b'`。

`+`
~~~
    

~~~
对它前面的正则式匹配1到任意次重复。 `ab+` 会匹配 `'a'` 后面跟随1个以上到任意个 `'b'`，它不会匹配 `'a'`。

`?`
~~~
    

~~~
对它前面的正则式匹配0到1次重复。 `ab?` 会匹配 `'a'` 或者 `'ab'`。

`*?`, `+?`, `??`
~~~
    

~~~
`'*'`, `'+'` 和 `'?'` 数量限定符都是 _贪婪的_ ；它们会匹配尽可能多的文本。 有时这种行为并不被需要；如果 RE `<.*>` 针对 `'<a> b <c>'` 进行匹配，它将匹配整个字符串，而不只是 `'<a>'`。 在数量限定符之后添加 `?` 将使其以 _非贪婪_ 或 _最小_ 风格来执行匹配；也就是将匹配数量尽可能 _少的_ 字符。 使用 RE `<.*?>` 将只匹配 `'<a>'`。

`*+`, `++`, `?+`
~~~
    

~~~
类似于 `'*'`, `'+'` 和 `'?'` 数量限定符，添加了 `'+'` 的形式也将匹配尽可能多的次数。 但是，不同于真正的贪婪型数量限定符，这些形式在之后的表达式匹配失败时不允许反向追溯。 这些形式被称为 _占有型_ 数量限定符。 例如，`a*a` 将匹配 `'aaaa'` 因为 `a*` 将匹配所有的 4 个 `'a'`，但是，当遇到最后一个 `'a'` 时，表达式将执行反向追溯以便最终 `a*` 最后变为匹配总计 3 个 `'a'`，而第四个 `'a'` 将由最后一个 `'a'` 来匹配。 然而，当使用 `a*+a` 时如果要匹配 `'aaaa'`，`a*+` 将匹配所有的 4 个 `'a'`，但是在最后一个 `'a'` 无法找到更多字符来匹配时，表达式将无法被反向追溯并将因此匹配失败。 `x*+`, `x++` 和 `x?+` 分别等价于 `(?>x*)`, `(?>x+)` 和 `(?>x?)`。

> 在 3.11 版本加入.

`{m}`
~~~
    

~~~
对其之前的正则式指定匹配 _m_ 个重复；少于 _m_ 的话就会导致匹配失败。比如， `a{6}` 将匹配6个 `'a'` , 但是不能是5个。

`{m,n}`
~~~
    

~~~
对正则式进行 _m_ 到 _n_ 次匹配，在 _m_ 和 _n_ 之间取尽量多。 比如，`a{3,5}` 将匹配 3 到 5个 `'a'`。忽略 _m_ 意为指定下界为0，忽略 _n_ 指定上界为无限次。 比如 `a{4,}b` 将匹配 `'aaaab'` 或者1000个 `'a'` 尾随一个 `'b'`，但不能匹配 `'aaab'`。逗号不能省略，否则无法辨别修饰符应该忽略哪个边界。

`{m,n}?`
~~~
    

~~~
将导致结果 RE 匹配之前 RE 的 _m_ 至 _n_ 次重复，尝试匹配尽可能 _少的_ 重复次数。 这是之前数量限定符的非贪婪版本。 例如，在 6 个字符的字符串 `'aaaaaa'` 上，`a{3,5}` 将匹配 5 个 `'a'` 字符，而 `a{3,5}?` 将只匹配 3 个字符。

`{m,n}+`
~~~
    

~~~
将导致结果 RE 匹配之前 RE 的 _m_ 至 _n_ 次重复，尝试匹配尽可能多的重复而 _不会_ 建立任何反向追溯点。 这是上述数量限定符的占有型版本。 例如，在 6 个字符的字符串 `'aaaaaa'` 上，`a{3,5}+aa` 将尝试匹配 5 个 `'a'` 字符，然后，要求再有 2 个 `'a'`，这将需要比可用的更多的字符因而会失败，而 `a{3,5}aa` 的匹配将使 `a{3,5}` 先捕获 5 个，然后通过反向追溯再匹配 4 个 `'a'`，然后用模式中最后的 `aa` 来匹配最后的 2 个 `'a'`。 `x{m,n}+` 就等同于 `(?>x{m,n})`。

在 3.11 版本加入.

`\`
~~~
    

~~~
转义特殊字符（允许你匹配 `'*'`, `'?'`, 或者此类其他），或者表示一个特殊序列；特殊序列之后进行讨论。

如果你没有使用原始字符串（ `r'raw'` ）来表达样式，要牢记Python也使用反斜杠作为转义序列；如果转义序列不被Python的分析器识别，反斜杠和字符才能出现在字符串中。如果Python可以识别这个序列，那么反斜杠就应该重复两次。这将导致理解障碍，所以高度推荐，就算是最简单的表达式，也要使用原始字符串。

`[]`
~~~
    

~~~
用于表示一个字符集合。在一个集合中：

  * 字符可以单独列出，比如 `[amk]` 匹配 `'a'`， `'m'`， 或者 `'k'`。

  * 可以表示字符范围，通过用 `'-'` 将两个字符连起来。比如 `[a-z]` 将匹配任何小写ASCII字符， `[0-5][0-9]` 将匹配从 `00` 到 `59` 的两位数字， `[0-9A-Fa-f]` 将匹配任何十六进制数位。 如果 `-` 进行了转义 （比如 `[a\-z]`）或者它的位置在首位或者末尾（如 `[-a]` 或 `[a-]`），它就只表示普通字符 `'-'`。

  * 特殊字符在集合中会失去其特殊意义。比如 `[(+*)]` 只会匹配这几个字面字符之一 `'('`, `'+'`, `'*'`, or `')'`。

  * 字符类如 `\w` 或者 `\S` (如下定义) 在集合内可以接受，它们可以匹配的字符由 `ASCII` 或者 `LOCALE` 模式决定。

  * 不在集合范围内的字符可以通过 _取反_ 来进行匹配。如果集合首字符是 `'^'` ，所有 _不_ 在集合内的字符将会被匹配，比如 `[^5]` 将匹配所有字符，除了 `'5'`， `[^^]` 将匹配所有字符，除了 `'^'`. `^` 如果不在集合首位，就没有特殊含义。

  * 要在集合内匹配一个 `']'` 字面值，可以在它前面加上反斜杠，或是将它放到集合的开头。 例如，`[()[\]{}]` 和 `[]()[{}]` 都可以匹配右方括号，以及左方括号，花括号和圆括号。

  * [Unicode Technical Standard #18](https://unicode.org/reports/tr18/) 里的嵌套集合和集合操作支持可能在未来添加。这将会改变语法，所以为了帮助这个改变，一个 [`FutureWarning`](exceptions.md#FutureWarning "FutureWarning") 将会在有多义的情况里被 `raise`，包含以下几种情况，集合由 `'['` 开始，或者包含下列字符序列 `'--'`, `'&&'`, `'~~'`, 和 `'||'`。为了避免警告，需要将它们用反斜杠转义。

在 3.7 版本发生变更: 如果一个字符串构建的语义在未来会改变的话，一个 [`FutureWarning`](exceptions.md#FutureWarning "FutureWarning") 会 `raise` 。

`|`
~~~
    

~~~
`A|B`， _A_ 和 _B_ 可以是任意正则表达式，创建一个正则表达式，匹配 _A_ 或者 _B_. 任意个正则表达式可以用 `'|'` 连接。它也可以在组合（见下列）内使用。扫描目标字符串时， `'|'` 分隔开的正则样式从左到右进行匹配。当一个样式完全匹配时，这个分支就被接受。意思就是，一旦 _A_ 匹配成功， _B_ 就不再进行匹配，即便它能产生一个更好的匹配。或者说，`'|'` 操作符绝不贪婪。 如果要匹配 `'|'` 字符，使用 `\|`， 或者把它包含在字符集里，比如 `[|]`.

`(...)`
~~~
    

~~~
（组合），匹配括号内的任意正则表达式，并标识出组合的开始和结尾。匹配完成后，组合的内容可以被获取，并可以在之后用 `\number` 转义序列进行再次匹配，之后进行详细说明。要匹配字符 `'('` 或者 `')'`, 用 `\(` 或 `\)`, 或者把它们包含在字符集合里: `[(]`, `[)]`.

`(?…)`
~~~
    

~~~
这是个扩展标记法 （一个 `'?'` 跟随 `'('` 并无含义）。 `'?'` 后面的第一个字符决定了这个构建采用什么样的语法。这种扩展通常并不创建新的组合； `(?P<name>...)` 是唯一的例外。 以下是目前支持的扩展。

`(?aiLmsux)`
~~~
    

~~~
( `'a'`, `'i'`, `'L'`, `'m'`, `'s'`, `'u'`, `'x'` 中的一个或多个) 这个组合匹配一个空字符串；这些字符对正则表达式设置以下标记 `re.A` (只匹配ASCII字符), `re.I` (忽略大小写), `re.L` (语言依赖), `re.M` (多行模式), `re.S` (点dot匹配全部字符), `re.U` (Unicode匹配), and `re.X` (冗长模式)。 (这些标记在 模块内容 中描述) 如果你想将这些标记包含在正则表达式中，这个方法就很有用，免去了在 `re.compile()` 中传递 _flag_ 参数。标记应该在表达式字符串首位表示。

在 3.11 版本发生变更: 此构造只能在表达式的开头使用。

`(?:…)`
~~~
    

~~~
正则括号的非捕获版本。 匹配在括号内的任何正则表达式，但该分组所匹配的子字符串 _不能_ 在执行匹配后被获取或是之后在模式中被引用。

`(?aiLmsux-imsx:…)`
~~~
    

~~~
(`'a'`, `'i'`, `'L'`, `'m'`, `'s'`, `'u'`, `'x'` 中的0或者多个， 之后可选跟随 `'-'` 在后面跟随 `'i'` , `'m'` , `'s'` , `'x'` 中的一到多个 .) 这些字符为表达式的其中一部分 _设置_ 或者 _去除_ 相应标记 `re.A` (只匹配ASCII), `re.I` (忽略大小写), `re.L` (语言依赖), `re.M` (多行), `re.S` (点匹配所有字符), `re.U` (Unicode匹配), and `re.X` (冗长模式)。(标记描述在 模块内容 .)

`'a'`, `'L'` and `'u'` 作为内联标记是相互排斥的， 所以它们不能结合在一起，或者跟随 `'-'` 。 当他们中的某个出现在内联组中，它就覆盖了括号组内的匹配模式。在Unicode样式中， `(?a:...)` 切换为 只匹配ASCII， `(?u:...)` 切换为Unicode匹配 (默认). 在byte样式中 `(?L:...)` 切换为语言依赖模式， `(?a:...)` 切换为 只匹配ASCII (默认)。这种方式只覆盖组合内匹配，括号外的匹配模式不受影响。

在 3.6 版本加入.

在 3.7 版本发生变更: 符号 `'a'`, `'L'` 和 `'u'` 同样可以用在一个组合内。

`(?>...)`
~~~
    

~~~
尝试匹配 `...` 就像它是一个单独的正则表达式，如果匹配成功，则继续匹配在它之后的剩余表达式。 如果之后的表达式匹配失败，则栈只能回溯到 `(?>...)` _之前_ 的点，因为一旦退出，这个被称为 _原子化分组_ 的表达式将会丢弃其自身所有的栈点位。 因此，`(?>.*).` 将永远不会匹配任何东西因为首先 `.*` 将匹配所有可能的字符，然后，由于没有任何剩余的字符可供匹配，最后的 `.` 将匹配失败。 由于原子化分组中没有保存任何栈点位，并且在它之前也没有任何栈点位，因此整个表达式将匹配失败。

在 3.11 版本加入.

`(?P<name>…)`
~~~
    

~~~
与常规的圆括号类似，但分组所匹配到了子字符串可通过符号分组名称 _name_ 来访问。 分组名称必须是有效的 Python 标识符，并且在 [`bytes`](stdtypes.md#bytes "bytes") 模式中它们只能包含 ASCII 范围内的字节值。 每个分组名称在一个正则表达式中只能定义一次。 一个符号分组同时也是一个编号分组，就像这个分组没有被命名过一样。

命名组合可以在三种上下文中引用。如果样式是 `(?P<quote>['"]).*?(?P=quote)` （也就是说，匹配单引号或者双引号括起来的字符串)：

引用组合 "quote" 的上下文

|

引用方法  
  
---|---  
  
在正则式自身内

|

  * `(?P=quote)` (如示)

  * `\1`
~~~
  
  
~~~
处理匹配对象 _m_

|

  * `m.group('quote')`

  * `m.end('quote')` (等)
~~~
  
  
~~~
传递到 `re.sub()` 里的 _repl_ 参数中

|

  * `\g<quote>`

  * `\g<1>`

  * `\1`
~~~
  
  
~~~
在 3.12 版本发生变更: 在 [`bytes`](stdtypes.md#bytes "bytes") 模式中，分组 _name_ 只能包含 ASCII 范围内的字节值 (`b'\x00'`-`b'\x7f'`)。

`(?P=name)`
~~~
    

~~~
反向引用一个命名组合；它匹配前面那个叫 _name_ 的命名组中匹配到的串同样的字串。

`(?#…)`
~~~
    

~~~
注释；里面的内容会被忽略。

`(?=…)`
~~~
    

~~~
当 `…` 匹配时，匹配成功，但不消耗字符串中的任何字符。这个叫做 _前视断言_ （lookahead assertion）。比如， `Isaac (?=Asimov)` 将会匹配 `'Isaac '` ，仅当其后紧跟 `'Asimov'` 。

`(?!…)`
~~~
    

~~~
当 `…` 不匹配时，匹配成功。这个叫 _否定型前视断言_ （negative lookahead assertion）。例如， `Isaac (?!Asimov)` 将会匹配 `'Isaac '` ，仅当它后面 _不是_ `'Asimov'` 。

`(?<=…)`
~~~
    

~~~
如果 `...` 的匹配内容出现在当前位置的左侧，则匹配。这叫做 _肯定型后视断言_ （positive lookbehind assertion）。 `(?<=abc)def` 将会在 `'abcdef'` 中找到一个匹配，因为后视会回退3个字符并检查内部表达式是否匹配。内部表达式（匹配的内容）必须是固定长度的，意思就是 `abc` 或 `a|b` 是允许的，但是 `a*` 和 `a{3,4}` 不可以。注意，以肯定型后视断言开头的正则表达式，匹配项一般不会位于搜索字符串的开头。很可能你应该使用 `search()` 函数，而不是 `match()` 函数：
~~~
    
    
~~~shell
>>> import re
>>> m = re.search('(?<=abc)def', 'abcdef')
>>> m.group(0)
'def'
~~~

这个例子搜索一个跟随在连字符后的单词：

    
    
~~~shell
>>> m = re.search(r'(?<=-)\w+', 'spam-egg')
>>> m.group(0)
'egg'
~~~

在 3.5 版本发生变更: 添加定长组合引用的支持。

`(?<!…)`

    

~~~
如果 `...` 的匹配内容没有出现在当前位置的左侧，则匹配。这个叫做 _否定型后视断言_ （negative lookbehind assertion）。类似于肯定型后视断言，内部表达式（匹配的内容）必须是固定长度的。以否定型后视断言开头的正则表达式，匹配项可能位于搜索字符串的开头。

`(?(id/name)yes-pattern|no-pattern)`
~~~
    

~~~
如果给定的 _id_ 或 _name_ 存在，将会尝试匹配 `yes-pattern` ，否则就尝试匹配 `no-pattern`，`no-pattern` 可选，也可以被忽略。比如， `(<)?(\w+@\w+(?:\.\w+)+)(?(1)>|$)` 是一个email样式匹配，将匹配 `'<user@host.com>'` 或 `'user@host.com'` ，但不会匹配 `'<user@host.com'` ，也不会匹配 `'user@host.com>'`。

在 3.12 版本发生变更: 分组 _id_ 只能包含 ASCII 数码。 在 [`bytes`](stdtypes.md#bytes "bytes") 模式中，分组 _name_ 只能包含 ASCII 范围内的字节值 (`b'\x00'`-`b'\x7f'`)。

由 `'\'` 和一个字符组成的特殊序列在以下列出。 如果普通字符不是ASCII数位或者ASCII字母，那么正则样式将匹配第二个字符。比如，`\$` 匹配字符 `'$'`.

`\number`
~~~
    

~~~
匹配数字代表的组合。每个括号是一个组合，组合从1开始编号。比如 `(.+) \1` 匹配 `'the the'` 或者 `'55 55'`, 但不会匹配 `'thethe'` (注意组合后面的空格)。这个特殊序列只能用于匹配前面99个组合。如果 _number_ 的第一个数位是0， 或者 _number_ 是三个八进制数，它将不会被看作是一个组合，而是八进制的数字值。在 `'['` 和 `']'` 字符集合内，任何数字转义都被看作是字符。

`\A`
~~~
    

~~~
只匹配字符串开始。

`\b`
~~~
    

~~~
匹配空字符串，但只在单词开始或结尾的位置。一个单词被定义为一个单词字符的序列。注意，通常 `\b` 定义为 `\w` 和 `\W` 字符之间，或者 `\w` 和字符串开始/结尾的边界， 意思就是 `r'\bfoo\b'` 匹配 `'foo'`, `'foo.'`, `'(foo)'`, `'bar foo baz'` 但不匹配 `'foobar'` 或者 `'foo3'`。

默认情况下，Unicode字母和数字是在Unicode样式中使用的，但是可以用 `ASCII` 标记来更改。如果 `LOCALE` 标记被设置的话，词的边界是由当前语言区域设置决定的，`\b` 表示退格字符，以便与Python字符串文本兼容。

`\B`
~~~
    

~~~
匹配空字符串，但 _不_ 能在词的开头或者结尾。意思就是 `r'py\B'` 匹配 `'python'`, `'py3'`, `'py2'`, 但不匹配 `'py'`, `'py.'`, 或者 `'py!'`. `\B` 是 `\b` 的取非，所以Unicode样式的词语是由Unicode字母，数字或下划线构成的，虽然可以用 `ASCII` 标志来改变。如果使用了 `LOCALE` 标志，则词的边界由当前语言区域设置。

`\d`
~~~
    

~~~
对于 Unicode (str) 样式：
~~~
    

~~~
匹配任何Unicode十进制数（就是在Unicode字符目录[Nd]里的字符）。这包括了 `[0-9]` ，和很多其他的数字字符。如果设置了 `ASCII` 标志，就只匹配 `[0-9]` 。

对于8位(bytes)样式：
~~~
    

~~~
匹配任何十进制数，就是 `[0-9]`。

`\D`
~~~
    

~~~
匹配任何非十进制数字的字符。就是 `\d` 取非。 如果设置了 `ASCII` 标志，就相当于 `[^0-9]` 。

`\s`
~~~
    

~~~
对于 Unicode (str) 样式：
~~~
    

~~~
匹配任何Unicode空白字符（包括 `[ \t\n\r\f\v]` ，还有很多其他字符，比如不同语言排版规则约定的不换行空格）。如果 `ASCII` 被设置，就只匹配 `[ \t\n\r\f\v]` 。

对于8位(bytes)样式：
~~~
    

~~~
匹配ASCII中的空白字符，就是 `[ \t\n\r\f\v]` 。

`\S`
~~~
    

~~~
匹配任何非空白字符。就是 `\s` 取非。如果设置了 `ASCII` 标志，就相当于 `[^ \t\n\r\f\v]` 。

`\w`
~~~
    

~~~
对于 Unicode (str) 样式：
~~~
    

~~~
匹配 Unicode 单词类字符；这包括字母数字字符 (如 [`str.isalnum()`](stdtypes.md#str.isalnum "str.isalnum") 所定义的) 以及下划线 (`_`)。 如果使用了 `ASCII` 旗标，则将只匹配 `[a-zA-Z0-9_]`。

对于8位(bytes)样式：
~~~
    

~~~
匹配ASCII字符中的数字和字母和下划线，就是 `[a-zA-Z0-9_]` 。如果设置了 `LOCALE` 标记，就匹配当前语言区域的数字和字母和下划线。

`\W`
~~~
    

~~~
匹配非单词字符的字符。这与 `\w` 正相反。如果使用了 `ASCII` 旗标，这就等价于 `[^a-zA-Z0-9_]`。如果使用了 `LOCALE` 旗标，则会匹配当前区域中既非字母数字也非下划线的字符。

`\Z`
~~~
    

~~~
只匹配字符串尾。

Python 字符串字面值支持的大多数 [转义序列](../reference/lexical_analysis.md#escape-sequences) 也被正则表达式解析器所接受:
~~~
    
    
~~~
\a      \b      \f      \n
\N      \r      \t      \u
\U      \v      \x      \\
~~~

（注意 `\b` 被用于表示词语的边界，它只在字符集合内表示退格，比如 `[\b]` 。）

`'\u'`, `'\U'` 和 `'\N'` 转义序列只在 Unicode 模式中可被识别。 在 bytes 模式中它们会导致错误。 未知的 ASCII 字母转义序列保留在未来使用，会被当作错误来处理。

八进制转义包含为一个有限形式。如果首位数字是 0， 或者有三个八进制数位，那么就认为它是八进制转义。其他的情况，就看作是组引用。对于字符串文本，八进制转义最多有三个数位长。

在 3.3 版本发生变更: 增加了 `'\u'` 和 `'\U'` 转义序列。

在 3.6 版本发生变更: 由 `'\'` 和一个ASCII字符组成的未知转义会被看成错误。

在 3.8 版本发生变更: 添加了 `'\N{name}'` 转义序列。 与在字符串字面值中一样，它扩展了命名 Unicode 字符 (例如 `'\N{EM DASH}'`)。

## 模块内容¶

模块定义了几个函数、常量，和一个异常。有些函数是编译后的正则表达式方法的简化版本（少了一些特性）。重要的应用程序大多会在使用前先编译正则表达式。

### 标志¶

在 3.6 版本发生变更: 标志常量现在是 `RegexFlag` 类的实例，这个类是 [`enum.IntFlag`](enum.md#enum.IntFlag "enum.IntFlag") 的子类。

_class _re.RegexFlag¶

    

~~~
包含以下列出的正则表达式选项的 [`enum.IntFlag`](enum.md#enum.IntFlag "enum.IntFlag") 类。

在 3.11 版本加入: \- added to `__all__`

re.A¶

re.ASCII¶
~~~
    

~~~
让 `\w`, `\W`, `\b`, `\B`, `\d`, `\D`, `\s` 和 `\S` 只匹配ASCII，而不是Unicode。这只对Unicode样式有效，会被byte样式忽略。相当于前面语法中的内联标志 `(?a)` 。

注意，为了保持向后兼容， `re.U` 标记依然存在（还有他的同义 `re.UNICODE` 和嵌入形式 `(?u)` ) ， 但是这些在 Python 3 是冗余的，因为默认字符串已经是Unicode了（并且Unicode匹配不允许byte出现)。

re.DEBUG¶
~~~
    

~~~
显示编译时的debug信息，没有内联标记。

re.I¶

re.IGNORECASE¶
~~~
    

~~~
进行忽略大小写匹配；表达式如 `[A-Z]` 也会匹配小写字符。Unicode匹配（比如 `Ü` 匹配 `ü`）同样有用，除非设置了 `re.ASCII` 标记来禁用非ASCII匹配。当前语言区域不会改变这个标记，除非设置了 `re.LOCALE` 标记。这个相当于内联标记 `(?i)` 。

注意，当设置了 `IGNORECASE` 标记，搜索Unicode样式 `[a-z]` 或 `[A-Z]` 的结合时，它将会匹配52个ASCII字符和4个额外的非ASCII字符： 'İ' (U+0130, 拉丁大写的 I 带个点在上面), 'ı' (U+0131, 拉丁小写没有点的 I ), 'ſ' (U+017F, 拉丁小写长 s) and 'K' (U+212A, 开尔文符号).如果使用 `ASCII` 标记，就只匹配 'a' 到 'z' 和 'A' 到 'Z' 。

re.L¶

re.LOCALE¶
~~~
    

~~~
由当前语言区域决定 `\w`, `\W`, `\b`, `\B` 和大小写敏感匹配。这个标记只能对byte样式有效。这个标记不推荐使用，因为语言区域机制很不可靠，它一次只能处理一个 "习惯”，而且只对8位字节有效。Unicode匹配在Python 3 里默认启用，并可以处理不同语言。 这个对应内联标记 `(?L)` 。

在 3.6 版本发生变更: `re.LOCALE` 只能用于byte样式，而且不能和 `re.ASCII` 一起用。

在 3.7 版本发生变更: 设置了 `re.LOCALE` 标记的编译正则对象不再在编译时依赖语言区域设置。语言区域设置只在匹配的时候影响其结果。

re.M¶

re.MULTILINE¶
~~~
    

~~~
设置以后，样式字符 `'^'` 匹配字符串的开始，和每一行的开始（换行符后面紧跟的符号）；样式字符 `'$'` 匹配字符串尾，和每一行的结尾（换行符前面那个符号）。默认情况下，`’^’` 匹配字符串头，`'$'` 匹配字符串尾。对应内联标记 `(?m)` 。

re.NOFLAG¶
~~~
    

~~~
表示未应用任何旗标，该值为 `0`。 该旗标可被用作某个函数关键字参数的默认值或者用作将与其他旗标进行有条件 OR 运算的基准值。 用作默认值的例子:
~~~
    
    
~~~python
def myfunc(text, flag=re.NOFLAG):
    return re.match(text, flag)
~~~

在 3.11 版本加入.

re.S¶

re.DOTALL¶

    

~~~
让 `'.'` 特殊字符匹配任何字符，包括换行符；如果没有这个标记，`'.'` 就匹配 _除了_ 换行符的其他任意字符。对应内联标记 `(?s)` 。

re.U¶

re.UNICODE¶
~~~
    

~~~
在 Python 2 中，这个旗标使 特殊序列 在匹配中包含 Unicode 字符。 自 Python 3 起，默认即会匹配 Unicode 字符。

请参阅 `A` 了解如何改为仅限匹配 ASCII 字符。

该旗标仅为向下兼容而保留。

re.X¶

re.VERBOSE¶
~~~
    

~~~
这个旗标允许你通过在视觉上分隔表达式的逻辑段落和添加注释来编写更为友好并更具可读性的正则表达式。 表达式中的空白符会被忽略，除非是在字符类中，或前面有一个未转义的反斜杠，或者是在 `*?`, `(?:` 或 `(?P<...>` 等形符之内。 例如，`(? :` 和 `* ?` 是不被允许的。 当一个行内包含不在字符类中并且前面没有未转义反斜杠的 `#` 时，则从最左边的此 `#` 直至行尾的所有字符都会被忽略。

意思就是下面两个正则表达式等价地匹配一个十进制数字：
~~~
    
    
~~~
a = re.compile(r"""\d +  # the integral part
                   \.    # the decimal point
                   \d *  # some fractional digits""", re.X)
b = re.compile(r"\d+\.\d*")
~~~

对应内联标记 `(?x)` 。

### 函数¶

re.compile( _pattern_ , _flags =0_)¶

    

~~~
将正则表达式的样式编译为一个 正则表达式对象 （正则对象），可以用于匹配，通过这个对象的方法 `match()`, `search()` 以及其他如下描述。

这个表达式的行为可以通过指定 _标记_ 的值来改变。值可以是以下任意变量，可以通过位的OR操作来结合（ `|` 操作符）。

序列
~~~
    
    
~~~
prog = re.compile(pattern)
result = prog.match(string)
~~~

等价于

    
    
~~~
result = re.match(pattern, string)
~~~

如果需要多次使用这个正则表达式的话，使用 `re.compile()` 和保存这个正则对象以便复用，可以让程序更加高效。

备注

通过 `re.compile()` 编译后的样式，和模块级的函数会被缓存， 所以少数的正则表达式使用无需考虑编译的问题。

re.search( _pattern_ , _string_ , _flags =0_)¶

    

~~~
扫描整个 _string_ 查找正则表达式 _pattern_ 产生匹配的第一个位置，并返回相应的 `Match`。 如果字符串中没有与模式匹配的位置则返回 `None`；请注意这不同于在字符串的某个位置上找到零长度匹配。

re.match( _pattern_ , _string_ , _flags =0_)¶
~~~
    

~~~
如果 _string_ 开头的零个或多个字符与正则表达式 _pattern_ 匹配，则返回相应的 `Match`。 如果字符串与模式不匹配则返回 `None`；请注意这与零长度匹配是不同的。

注意即便是 `MULTILINE` 多行模式， `re.match()` 也只匹配字符串的开始位置，而不匹配每行开始。

如果你想定位 _string_ 的任何位置，使用 `search()` 来替代（也可参考 search() vs. match() ）

re.fullmatch( _pattern_ , _string_ , _flags =0_)¶
~~~
    

~~~
如果整个 _string_ 与正则表达式 _pattern_ 匹配，则返回相应的 `Match`。 如果字符串与模式不匹配则返回 `None`；请注意这与零长度匹配是不同的。

在 3.4 版本加入.

re.split( _pattern_ , _string_ , _maxsplit =0_, _flags =0_)¶
~~~
    

~~~
用 _pattern_ 分开 _string_ 。 如果在 _pattern_ 中捕获到括号，那么所有的组里的文字也会包含在列表里。如果 _maxsplit_ 非零， 最多进行 _maxsplit_ 次分隔， 剩下的字符全部返回到列表的最后一个元素。
~~~
    
    
~~~shell
>>> re.split(r'\W+', 'Words, words, words.')
['Words', 'words', 'words', '']
>>> re.split(r'(\W+)', 'Words, words, words.')
['Words', ', ', 'words', ', ', 'words', '.', '']
>>> re.split(r'\W+', 'Words, words, words.', maxsplit=1)
['Words', 'words, words.']
>>> re.split('[a-f]+', '0a3B9', flags=re.IGNORECASE)
['0', '3', '9']
~~~

如果分隔符里有捕获组合，并且匹配到字符串的开始，那么结果将会以一个空字符串开始。对于结尾也是一样

    
    
~~~shell
>>> re.split(r'(\W+)', '...words, words...')
['', '...', 'words', ', ', 'words', '...', '']
~~~

这样的话，分隔组将会出现在结果列表中同样的位置。

样式的空匹配仅在与前一个空匹配不相邻时才会拆分字符串。

    
    
~~~shell
>>> re.split(r'\b', 'Words, words, words.')
['', 'Words', ', ', 'words', ', ', 'words', '.']
>>> re.split(r'\W*', '...words...')
['', '', 'w', 'o', 'r', 'd', 's', '', '']
>>> re.split(r'(\W*)', '...words...')
['', '...', '', '', 'w', '', 'o', '', 'r', '', 'd', '', 's', '...', '', '', '']
~~~

在 3.1 版本发生变更: 增加了可选标记参数。

在 3.7 版本发生变更: 增加了空字符串的样式分隔。

自 3.13 版本弃用: Passing _maxsplit_ and _flags_ as positional arguments is deprecated. In future Python versions they will be [keyword-only parameters](../glossary.md#keyword-only-parameter).

re.findall( _pattern_ , _string_ , _flags =0_)¶

    

~~~
返回 _pattern_ 在 _string_ 中的所有非重叠匹配，以字符串列表或字符串元组列表的形式。对 _string_ 的扫描从左至右，匹配结果按照找到的顺序返回。 空匹配也包括在结果中。

返回结果取决于模式中捕获组的数量。如果没有组，返回与整个模式匹配的字符串列表。如果有且仅有一个组，返回与该组匹配的字符串列表。如果有多个组，返回与这些组匹配的字符串元组列表。非捕获组不影响结果。
~~~
    
    
~~~shell
>>> re.findall(r'\bf[a-z]*', 'which foot or hand fell fastest')
['foot', 'fell', 'fastest']
>>> re.findall(r'(\w+)=(\d+)', 'set width=20 and height=10')
[('width', '20'), ('height', '10')]
~~~

在 3.7 版本发生变更: 非空匹配现在可以在前一个空匹配之后出现了。

re.finditer( _pattern_ , _string_ , _flags =0_)¶

    

~~~
针对正则表达式 _pattern_ 在 _string_ 里的所有非重叠匹配返回一个产生 `Match` 对象的 [iterator](../glossary.md#term-iterator)。 _string_ 将被从左至右地扫描，并且匹配也将按被找到的顺序返回。 空匹配也会被包括在结果中。

在 3.7 版本发生变更: 非空匹配现在可以在前一个空匹配之后出现了。

re.sub( _pattern_ , _repl_ , _string_ , _count =0_, _flags =0_)¶
~~~
    

~~~
返回通过使用 _repl_ 替换在 _string_ 最左边非重叠出现的 _pattern_ 而获得的字符串。 如果样式没有找到，则不加改变地返回 _string_ 。 _repl_ 可以是字符串或函数；如为字符串，则其中任何反斜杠转义序列都会被处理。 也就是说，`\n` 会被转换为一个换行符，`\r` 会被转换为一个回车符，依此类推。 未知的 ASCII 字符转义序列保留在未来使用，会被当作错误来处理。 其他未知转义序列例如 `\&` 会保持原样。 向后引用像是 `\6` 会用样式中第 6 组所匹配到的子字符串来替换。 例如:
~~~
    
    
~~~shell
>>> re.sub(r'def\s+([a-zA-Z_][a-zA-Z_0-9]*)\s*\(\s*\):',
...        r'static PyObject*\npy_\1(void)\n{',
...        'def myfunc():')
'static PyObject*\npy_myfunc(void)\n{'
~~~

如果 _repl_ 是一个函数，则它会针对每次 _pattern_ 的非重叠出现的情况被调用。 该函数接受单个 `Match` 参数，并返回替换字符串。 例如:

    
    
~~~shell
>>> def dashrepl(matchobj):
...     if matchobj.group(0) == '-': return ' '
...     else: return '-'
...
>>> re.sub('-{1,2}', dashrepl, 'pro----gram-files')
'pro--gram files'
>>> re.sub(r'\sAND\s', ' & ', 'Baked Beans And Spam', flags=re.IGNORECASE)
'Baked Beans & Spam'
~~~

模式可以是一个字符串或者 `Pattern`。

可选参数 _count_ 是要替换的最大次数； _count_ 必须是非负整数。如果省略这个参数或设为 0，所有的匹配都会被替换。 样式的空匹配仅在与前一个空匹配不相邻时才会被替换，所以 `sub('x*', '-', 'abxd')` 返回 `'-a-b--d-'` 。

在字符串类型的 _repl_ 参数里，如上所述的转义和向后引用中，`\g<name>` 会使用命名组合 `name`，（在 `(?P<name>…)` 语法中定义） `\g<number>` 会使用数字组；`\g<2>` 就是 `\2`，但它避免了二义性，如 `\g<2>0`。 `\20` 就会被解释为组20，而不是组2后面跟随一个字符 `'0'`。向后引用 `\g<0>` 把 _pattern_ 作为一整个组进行引用。

在 3.1 版本发生变更: 增加了可选标记参数。

在 3.5 版本发生变更: 不匹配的组合替换为空字符串。

在 3.6 版本发生变更: _pattern_ 中的未知转义（由 `'\'` 和一个 ASCII 字符组成）被视为错误。

在 3.7 版本发生变更: Unknown escapes in _repl_ consisting of `'\'` and an ASCII letter now are errors. Empty matches for the pattern are replaced when adjacent to a previous non-empty match.

在 3.12 版本发生变更: 分组 _id_ 只能包含 ASCII 数码。 在 [`bytes`](stdtypes.md#bytes "bytes") 替换字符串中，分组 _name_ 只能包含 ASCII 范围内的字节值 (`b'\x00'`-`b'\x7f'`)。

自 3.13 版本弃用: Passing _count_ and _flags_ as positional arguments is deprecated. In future Python versions they will be [keyword-only parameters](../glossary.md#keyword-only-parameter).

re.subn( _pattern_ , _repl_ , _string_ , _count =0_, _flags =0_)¶

    

~~~
行为与 `sub()` 相同，但是返回一个元组 `(字符串, 替换次数)`.

re.escape( _pattern_ )¶
~~~
    

~~~
转义 _pattern_ 中的特殊字符。如果你想对任意可能包含正则表达式元字符的文本字符串进行匹配，它就是有用的。比如
~~~
    
    
~~~shell
>>> print(re.escape('https://www.python.org'))
https://www\.python\.org

>>> legal_chars = string.ascii_lowercase + string.digits + "!#$%&'*+-.^_`|~:"
>>> print('[%s]+' % re.escape(legal_chars))
[abcdefghijklmnopqrstuvwxyz0123456789!\#\$%\&'\*\+\-\.\^_`\|\~:]+

>>> operators = ['+', '-', '*', '/', '**']
>>> print('|'.join(map(re.escape, sorted(operators, reverse=True))))
/|\-|\+|\*\*|\*
~~~

这个函数不能被用于 `sub()` 和 `subn()` 的替换字符串，只有反斜杠应该被转义。 例如:

    
    
~~~shell
>>> digits_re = r'\d+'
>>> sample = '/usr/sbin/sendmail - 0 errors, 12 warnings'
>>> print(re.sub(digits_re, digits_re.replace('\\', r'\\'), sample))
/usr/sbin/sendmail - \d+ errors, \d+ warnings
~~~

在 3.3 版本发生变更: `'_'` 不再被转义。

在 3.7 版本发生变更: 只有在正则表达式中具有特殊含义的字符才会被转义。 因此， `'!'`, `'"'`, `'%'`, `"'"`, `','`, `'/'`, `':'`, `';'`, `'<'`, `'='`, `'>'`, `'@'` 和 `"`"` 将不再会被转义。

re.purge()¶

    

~~~
清除正则表达式的缓存。

### 异常¶

_exception _re.error( _msg_ , _pattern =None_, _pos =None_)¶
~~~
    

~~~
当传递给函数的正则表达式不合法（比如括号不匹配），或者在编译或匹配过程中出现其他错误时，会引发异常。所给字符串不匹配所给模式不会引发异常。异常实例有以下附加属性：

msg¶
~~~
    

~~~
未格式化的错误消息。

pattern¶
~~~
    

~~~
正则表达式的模式串。

pos¶
~~~
    

~~~
编译失败的 _pattern_ 的位置索引（可以是 `None` ）。

lineno¶
~~~
    

~~~
对应 _pos_ (可以是 `None`) 的行号。

colno¶
~~~
    

~~~
对应 _pos_ (可以是 `None`) 的列号。

在 3.5 版本发生变更: 增加了额外的属性。

## 正则表达式对象 （正则对象）¶

_class _re.Pattern¶
~~~
    

~~~
由 `re.compile()` 返回的已编译正则表达式对象。

在 3.9 版本发生变更: `re.Pattern` 支持用 `[]` 表示 Unicode (str) 或字节串类型的模式。 参见 [GenericAlias 类型](stdtypes.md#types-genericalias)。

Pattern.search( _string_ [, _pos_ [, _endpos_ ]])¶
~~~
    

~~~
扫描整个 _string_ 查找该正则表达式产生匹配的第一个位置，并返回相应的 `Match`。 如果字符串中没有与模式匹配的位置则返回 `None`；请注意这不同于在字符串的某个位置上找到零长度匹配。

可选的第二个参数 _pos_ 给出了字符串中开始搜索的位置索引；默认为 `0`，它不完全等价于字符串切片； `'^'` 样式字符匹配字符串真正的开头，和换行符后面的第一个字符，但不会匹配索引规定开始的位置。

可选参数 _endpos_ 限定了字符串搜索的结束；它假定字符串长度到 _endpos_ ， 所以只有从 `pos` 到 `endpos - 1` 的字符会被匹配。如果 _endpos_ 小于 _pos_ ，就不会有匹配产生；另外，如果 _rx_ 是一个编译后的正则对象， `rx.search(string, 0, 50)` 等价于 `rx.search(string[:50], 0)`。
~~~
    
    
~~~shell
>>> pattern = re.compile("d")
>>> pattern.search("dog")     # Match at index 0
<re.Match object; span=(0, 1), match='d'>
>>> pattern.search("dog", 1)  # No match; search doesn't include the "d"
~~~

Pattern.match( _string_ [, _pos_ [, _endpos_ ]])¶

    

~~~
如果字符串 _开头_ 的零个或多个字符与此正则表达式匹配，则返回相应的 `Match`。 如果字符串与模式不匹配则返回 `None`；请注意这与零长度匹配是不同的。

可选参数 _pos_ 和 _endpos_ 与 `search()` 含义相同。
~~~
    
    
~~~shell
>>> pattern = re.compile("o")
>>> pattern.match("dog")      # No match as "o" is not at the start of "dog".
>>> pattern.match("dog", 1)   # Match as "o" is the 2nd character of "dog".
<re.Match object; span=(1, 2), match='o'>
~~~

如果你想定位匹配在 _string_ 中的位置，使用 `search()` 来替代（另参考 search() vs. match()）。

Pattern.fullmatch( _string_ [, _pos_ [, _endpos_ ]])¶

    

~~~
如果整个 _string_ 与此正则表达式匹配，则返回相应的 `Match`。 如果字符串与模式不匹配则返回 `None`；请注意这与零长度匹配是不同的。

可选参数 _pos_ 和 _endpos_ 与 `search()` 含义相同。
~~~
    
    
~~~shell
>>> pattern = re.compile("o[gh]")
>>> pattern.fullmatch("dog")      # No match as "o" is not at the start of "dog".
>>> pattern.fullmatch("ogre")     # No match as not the full string matches.
>>> pattern.fullmatch("doggie", 1, 3)   # Matches within given limits.
<re.Match object; span=(1, 3), match='og'>
~~~

在 3.4 版本加入.

Pattern.split( _string_ , _maxsplit =0_)¶

    

~~~
等价于 `split()` 函数，使用了编译后的样式。

Pattern.findall( _string_ [, _pos_ [, _endpos_ ]])¶
~~~
    

~~~
类似函数 `findall()` ， 使用了编译后样式，但也可以接收可选参数 _pos_ 和 _endpos_ ，限制搜索范围，就像 `search()`。

Pattern.finditer( _string_ [, _pos_ [, _endpos_ ]])¶
~~~
    

~~~
类似函数 `finditer()` ， 使用了编译后样式，但也可以接收可选参数 _pos_ 和 _endpos_ ，限制搜索范围，就像 `search()`。

Pattern.sub( _repl_ , _string_ , _count =0_)¶
~~~
    

~~~
等价于 `sub()` 函数，使用了编译后的样式。

Pattern.subn( _repl_ , _string_ , _count =0_)¶
~~~
    

~~~
等价于 `subn()` 函数，使用了编译后的样式。

Pattern.flags¶
~~~
    

~~~
正则匹配标记。这是可以传递给 `compile()` 的参数，任何 `(?…)` 内联标记，隐性标记比如 `UNICODE` 的结合。

Pattern.groups¶
~~~
    

~~~
捕获到的模式串中组的数量。

Pattern.groupindex¶
~~~
    

~~~
映射由 `(?P<id>)` 定义的命名符号组合和数字组合的字典。如果没有符号组，那字典就是空的。

Pattern.pattern¶
~~~
    

~~~
编译对象的原始样式字符串。

在 3.7 版本发生变更: 添加 [`copy.copy()`](copy.md#copy.copy "copy.copy") 和 [`copy.deepcopy()`](copy.md#copy.deepcopy "copy.deepcopy") 函数的支持。编译后的正则表达式对象被认为是原子性的。

## 匹配对象¶

匹配对象总是有一个布尔值 `True`。如果没有匹配的话 `match()` 和 `search()` 返回 `None` 所以你可以简单的用 `if` 语句来判断是否匹配
~~~
    
    
~~~
match = re.search(pattern, string)
if match:
    process(match)
~~~

_class _re.Match¶

    

~~~
由成功的 `match` 和 `search` 所返回的匹配对象。

在 3.9 版本发生变更: `re.Match` 支持用 `[]` 表示 Unicode (str) 或字节串类型的匹配。 参见 [GenericAlias 类型](stdtypes.md#types-genericalias)。

Match.expand( _template_ )¶
~~~
    

~~~
对 _template_ 进行反斜杠转义替换并且返回，就像 `sub()` 方法中一样。转义如同 `\n` 被转换成合适的字符，数字引用(`\1`, `\2`)和命名组合(`\g<1>`, `\g<name>`) 替换为相应组合的内容。

在 3.5 版本发生变更: 不匹配的组合替换为空字符串。

Match.group([ _group1_ , _..._ ])¶
~~~
    

~~~
返回一个或者多个匹配的子组。如果只有一个参数，结果就是一个字符串，如果有多个参数，结果就是一个元组（每个参数对应一个项），如果没有参数，组1默认到0（整个匹配都被返回）。 如果一个组N 参数值为 0，相应的返回值就是整个匹配字符串；如果它是一个范围 [1..99]，结果就是相应的括号组字符串。如果一个组号是负数，或者大于样式中定义的组数，就引发一个 [`IndexError`](exceptions.md#IndexError "IndexError") 异常。如果一个组包含在样式的一部分，并被匹配多次，就返回最后一个匹配。:
~~~
    
    
~~~shell
>>> m = re.match(r"(\w+) (\w+)", "Isaac Newton, physicist")
>>> m.group(0)       # The entire match
'Isaac Newton'
>>> m.group(1)       # The first parenthesized subgroup.
'Isaac'
>>> m.group(2)       # The second parenthesized subgroup.
'Newton'
>>> m.group(1, 2)    # Multiple arguments give us a tuple.
('Isaac', 'Newton')
~~~

如果正则表达式使用了 `(?P<name>...)` 语法， _groupN_ 参数就也可能是命名组合的名字。如果一个字符串参数在样式中未定义为组合名，就引发一个 [`IndexError`](exceptions.md#IndexError "IndexError") 异常。

一个相对复杂的例子

    
    
~~~shell
>>> m = re.match(r"(?P<first_name>\w+) (?P<last_name>\w+)", "Malcolm Reynolds")
>>> m.group('first_name')
'Malcolm'
>>> m.group('last_name')
'Reynolds'
~~~

命名组合同样可以通过索引值引用

    
    
~~~shell
>>> m.group(1)
'Malcolm'
>>> m.group(2)
'Reynolds'
~~~

如果一个组匹配成功多次，就只返回最后一个匹配

    
    
~~~shell
>>> m = re.match(r"(..)+", "a1b2c3")  # Matches 3 times.
>>> m.group(1)                        # Returns only the last match.
'c3'
~~~

Match.__getitem__( _g_ )¶

    

~~~
这个等价于 `m.group(g)`。这允许更方便的引用一个匹配
~~~
    
    
~~~shell
>>> m = re.match(r"(\w+) (\w+)", "Isaac Newton, physicist")
>>> m[0]       # The entire match
'Isaac Newton'
>>> m[1]       # The first parenthesized subgroup.
'Isaac'
>>> m[2]       # The second parenthesized subgroup.
'Newton'
~~~

命名分组也是受支持的:

    
    
~~~shell
>>> m = re.match(r"(?P<first_name>\w+) (?P<last_name>\w+)", "Isaac Newton")
>>> m['first_name']
'Isaac'
>>> m['last_name']
'Newton'
~~~

在 3.6 版本加入.

Match.groups( _default =None_)¶

    

~~~
返回一个元组，包含所有匹配的子组，在样式中出现的从1到任意多的组合。 _default_ 参数用于不参与匹配的情况，默认为 `None`。

例如：
~~~
    
    
~~~shell
>>> m = re.match(r"(\d+)\.(\d+)", "24.1632")
>>> m.groups()
('24', '1632')
~~~

如果我们使小数点可选，那么不是所有的组都会参与到匹配当中。这些组合默认会返回一个 `None` ，除非指定了 _default_ 参数。

    
    
~~~shell
>>> m = re.match(r"(\d+)\.?(\d+)?", "24")
>>> m.groups()      # Second group defaults to None.
('24', None)
>>> m.groups('0')   # Now, the second group defaults to '0'.
('24', '0')
~~~

Match.groupdict( _default =None_)¶

    

~~~
返回一个字典，包含了所有的 _命名_ 子组。key就是组名。 _default_ 参数用于不参与匹配的组合；默认为 `None`。 例如
~~~
    
    
~~~shell
>>> m = re.match(r"(?P<first_name>\w+) (?P<last_name>\w+)", "Malcolm Reynolds")
>>> m.groupdict()
{'first_name': 'Malcolm', 'last_name': 'Reynolds'}
~~~

Match.start([ _group_ ])¶

Match.end([ _group_ ])¶

    

~~~
返回 _group_ 匹配到的字串的开始和结束标号。 _group_ 默认为0（意思是整个匹配的子串）。如果 _group_ 存在，但未产生匹配，就返回 `-1` 。对于一个匹配对象 _m_ ， 和一个未参与匹配的组 _g_ ，组 _g_ (等价于 `m.group(g)`)产生的匹配是
~~~
    
    
~~~
m.string[m.start(g):m.end(g)]
~~~

注意 `m.start(group)` 将会等于 `m.end(group)` ，如果 _group_ 匹配一个空字符串的话。比如，在 `m = re.search('b(c?)', 'cba')` 之后，`m.start(0)` 为 1, `m.end(0)` 为 2, `m.start(1)` 和 `m.end(1)` 都是 2, `m.start(2)` 引发一个 [`IndexError`](exceptions.md#IndexError "IndexError") 异常。

这个例子会从email地址中移除掉 _remove_this_

    
    
~~~shell
>>> email = "tony@tiremove_thisger.net"
>>> m = re.search("remove_this", email)
>>> email[:m.start()] + email[m.end():]
'tony@tiger.net'
~~~

Match.span([ _group_ ])¶

    

~~~
对于一个匹配 _m_ ， 返回一个二元组 `(m.start(group), m.end(group))` 。 注意如果 _group_ 没有在这个匹配中，就返回 `(-1, -1)` 。 _group_ 默认为0，就是整个匹配。

Match.pos¶
~~~
    

~~~
_pos_ 的值，会传递给 `search()` 或 `match()` 的方法 a 正则对象 。这个是正则引擎开始在字符串搜索一个匹配的索引位置。

Match.endpos¶
~~~
    

~~~
_endpos_ 的值，会传递给 `search()` 或 `match()` 的方法 a 正则对象 。这个是正则引擎停止在字符串搜索一个匹配的索引位置。

Match.lastindex¶
~~~
    

~~~
捕获组的最后一个匹配的整数索引值，或者 `None` 如果没有匹配产生的话。比如，对于字符串 `'ab'`，表达式 `(a)b`, `((a)(b))`, 和 `((ab))` 将得到 `lastindex == 1` ， 而 `(a)(b)` 会得到 `lastindex == 2` 。

Match.lastgroup¶
~~~
    

~~~
最后一个匹配的命名组名字，或者 `None` 如果没有产生匹配的话。

Match.re¶
~~~
    

~~~
返回产生这个实例的 正则对象 ， 这个实例是由 正则对象的 `match()` 或 `search()` 方法产生的。

Match.string¶
~~~
    

~~~
传递到 `match()` 或 `search()` 的字符串。

在 3.7 版本发生变更: 添加了对 [`copy.copy()`](copy.md#copy.copy "copy.copy") 和 [`copy.deepcopy()`](copy.md#copy.deepcopy "copy.deepcopy") 的支持。匹配对象被看作是原子性的。

## 正则表达式例子¶

### 检查对子¶

在这个例子里，我们使用以下辅助函数来更好地显示匹配对象:
~~~
    
    
~~~python
def displaymatch(match):
    if match is None:
        return None
    return '<Match: %r, groups=%r>' % (match.group(), match.groups())
~~~

假设你在写一个扑克程序，一个玩家的一手牌为五个字符的串，每个字符表示一张牌，"a" 就是 A, "k" K， "q" Q, "j" J, "t" 为 10, "2" 到 "9" 表示2 到 9。

要看给定的字符串是否有效，我们可以按照以下步骤

    
    
~~~shell
>>> valid = re.compile(r"^[a2-9tjqk]{5}$")
>>> displaymatch(valid.match("akt5q"))  # Valid.
"<Match: 'akt5q', groups=()>"
>>> displaymatch(valid.match("akt5e"))  # Invalid.
>>> displaymatch(valid.match("akt"))    # Invalid.
>>> displaymatch(valid.match("727ak"))  # Valid.
"<Match: '727ak', groups=()>"
~~~

最后一手牌，`"727ak"` ，包含了一个对子，或者两张同样数值的牌。要用正则表达式匹配它，应该使用向后引用如下

    
    
~~~shell
>>> pair = re.compile(r".*(.).*\1")
>>> displaymatch(pair.match("717ak"))     # Pair of 7s.
"<Match: '717', groups=('7',)>"
>>> displaymatch(pair.match("718ak"))     # No pairs.
>>> displaymatch(pair.match("354aa"))     # Pair of aces.
"<Match: '354aa', groups=('a',)>"
~~~

要找出对子由什么牌组成，开发者可以按照下面的方式来使用匹配对象的 `group()` 方法:

    
    
~~~shell
>>> pair = re.compile(r".*(.).*\1")
>>> pair.match("717ak").group(1)
'7'

# Error because re.match() returns None, which doesn't have a group() method:
>>> pair.match("718ak").group(1)
Traceback (most recent call last):
  File "<pyshell#23>", line 1, in <module>
    re.match(r".*(.).*\1", "718ak").group(1)
AttributeError: 'NoneType' object has no attribute 'group'

>>> pair.match("354aa").group(1)
'a'
~~~

### 模拟 scanf()¶

目前 Python 没有 `scanf()` 的等价物。 正则表达式通常比 `scanf()` 格式字符串更强大，但也更冗长。 下表提供了 `scanf()` 格式符和正则表达式之间一些大致等价的映射。

`scanf()` 形符

|

正则表达式  
  
---|---  
  
`%c`

|

`.`  
  
`%5c`

|

`.{5}`  
  
`%d`

|

`[-+]?\d+`  
  
`%e`, `%E`, `%f`, `%g`

|

`[-+]?(\d+(\.\d*)?|\.\d+)([eE][-+]?\d+)?`  
  
`%i`

|

`[-+]?(0[xX][\dA-Fa-f]+|0[0-7]*|\d+)`  
  
`%o`

|

`[-+]?[0-7]+`  
  
`%s`

|

`\S+`  
  
`%u`

|

`\d+`  
  
`%x`, `%X`

|

`[-+]?(0[xX])?[\dA-Fa-f]+`  
  
从文件名和数字提取字符串

    
    
~~~
/usr/sbin/sendmail - 0 errors, 4 warnings
~~~

你应当这样使用 `scanf()` 格式

    
    
~~~
%s - %d errors, %d warnings
~~~

等价的正则表达式是：

    
    
~~~
(\S+) - (\d+) errors, (\d+) warnings
~~~

### search() vs. match()¶

Python 基于正则表达式提供了不同的原始操作:

  * `re.match()` 只在字符串的开头位置检测匹配。

  * `re.search()` 在字符串中的任何位置检测匹配（这也是 Perl 在默认情况下所做的）

  * `re.fullmatch()` 检测整个字符串是否匹配

例如：

    
    
~~~shell
>>> re.match("c", "abcdef")    # No match
>>> re.search("c", "abcdef")   # Match
<re.Match object; span=(2, 3), match='c'>
>>> re.fullmatch("p.*n", "python") # Match
<re.Match object; span=(0, 6), match='python'>
>>> re.fullmatch("r.*n", "python") # No match
~~~

在 `search()` 中，可以用 `'^'` 作为开始来限制匹配到字符串的首位

    
    
~~~shell
>>> re.match("c", "abcdef")    # No match
>>> re.search("^c", "abcdef")  # No match
>>> re.search("^a", "abcdef")  # Match
<re.Match object; span=(0, 1), match='a'>
~~~

注意 `MULTILINE` 多行模式中函数 `match()` 只匹配字符串的开始，但使用 `search()` 和以 `'^'` 开始的正则表达式会匹配每行的开始

    
    
~~~shell
>>> re.match("X", "A\nB\nX", re.MULTILINE)  # No match
>>> re.search("^X", "A\nB\nX", re.MULTILINE)  # Match
<re.Match object; span=(4, 5), match='X'>
~~~

### 制作一个电话本¶

`split()` 将字符串用参数传递的样式分隔开。这个方法对于转换文本数据到易读而且容易修改的数据结构，是很有用的，如下面的例子证明。

首先，这里是输入。 它通常来自一个文件，这里我们使用三重引号字符串语法

    
    
~~~shell
>>> text = """Ross McFluff: 834.345.1254 155 Elm Street
...
... Ronald Heathmore: 892.345.3428 436 Finley Avenue
... Frank Burger: 925.541.7625 662 South Dogwood Way
...
...
... Heather Albrecht: 548.326.4584 919 Park Place"""
~~~

条目用一个或者多个换行符分开。现在我们将字符串转换为一个列表，每个非空行都有一个条目:

    
    
~~~shell
>>> entries = re.split("\n+", text)
>>> entries
['Ross McFluff: 834.345.1254 155 Elm Street',
'Ronald Heathmore: 892.345.3428 436 Finley Avenue',
'Frank Burger: 925.541.7625 662 South Dogwood Way',
'Heather Albrecht: 548.326.4584 919 Park Place']
~~~

最终，将每个条目分割为一个由名字、姓氏、电话号码和地址组成的列表。我们为 `split()` 使用了 `maxsplit` 形参，因为地址中包含有被我们作为分割模式的空格符:

    
    
~~~shell
>>> [re.split(":? ", entry, maxsplit=3) for entry in entries]
[['Ross', 'McFluff', '834.345.1254', '155 Elm Street'],
['Ronald', 'Heathmore', '892.345.3428', '436 Finley Avenue'],
['Frank', 'Burger', '925.541.7625', '662 South Dogwood Way'],
['Heather', 'Albrecht', '548.326.4584', '919 Park Place']]
~~~

`:?` 样式匹配姓后面的冒号，因此它不出现在结果列表中。如果 `maxsplit` 设置为 `4` ，我们还可以从地址中获取到房间号:

    
    
~~~shell
>>> [re.split(":? ", entry, maxsplit=4) for entry in entries]
[['Ross', 'McFluff', '834.345.1254', '155', 'Elm Street'],
['Ronald', 'Heathmore', '892.345.3428', '436', 'Finley Avenue'],
['Frank', 'Burger', '925.541.7625', '662', 'South Dogwood Way'],
['Heather', 'Albrecht', '548.326.4584', '919', 'Park Place']]
~~~

### 文字整理¶

`sub()` 替换字符串中出现的样式的每一个实例。这个例子证明了使用 `sub()` 来整理文字，或者随机化每个字符的位置，除了首位和末尾字符

    
    
~~~shell
>>> def repl(m):
...     inner_word = list(m.group(2))
...     random.shuffle(inner_word)
...     return m.group(1) + "".join(inner_word) + m.group(3)
...
>>> text = "Professor Abdolmalek, please report your absences promptly."
>>> re.sub(r"(\w)(\w+)(\w)", repl, text)
'Poefsrosr Aealmlobdk, pslaee reorpt your abnseces plmrptoy.'
>>> re.sub(r"(\w)(\w+)(\w)", repl, text)
'Pofsroser Aodlambelk, plasee reoprt yuor asnebces potlmrpy.'
~~~

### 查找所有副词¶

`findall()` 匹配样式 _所有_ 的出现，不仅是像 `search()` 中的第一个匹配。比如，如果一个作者希望找到文字中的所有副词，他可能会按照以下方法用 `findall()`

    
    
~~~shell
>>> text = "He was carefully disguised but captured quickly by police."
>>> re.findall(r"\w+ly\b", text)
['carefully', 'quickly']
~~~

### 查找所有的副词及其位置¶

如果想要获得比匹配文本更多的关于模式的所有匹配信息，则 `finditer()` 会很有用处因为它提供了 `Match` 对象而不是字符串。 继续前面的例子，如果某位作者想要查找某段文本中的所有副词 _以及它们的位置_ ，可以按以下方式使用 `finditer()`:

    
    
~~~shell
>>> text = "He was carefully disguised but captured quickly by police."
>>> for m in re.finditer(r"\w+ly\b", text):
...     print('%02d-%02d: %s' % (m.start(), m.end(), m.group(0)))
07-16: carefully
40-47: quickly
~~~

### 原始字符串标记¶

原始字符串记法 (`r"text"`) 保持正则表达式正常。否则，每个正则式里的反斜杠(`'\'`) 都必须前缀一个反斜杠来转义。比如，下面两行代码功能就是完全一致的

    
    
~~~shell
>>> re.match(r"\W(.)\1\W", " ff ")
<re.Match object; span=(0, 4), match=' ff '>
>>> re.match("\\W(.)\\1\\W", " ff ")
<re.Match object; span=(0, 4), match=' ff '>
~~~

当需要匹配一个字符反斜杠，它必须在正则表达式中转义。在原始字符串记法，就是 `r"\\"`。否则就必须用 `"\\\\"`，来表示同样的意思

    
    
~~~shell
>>> re.match(r"\\", r"\\")
<re.Match object; span=(0, 1), match='\\'>
>>> re.match("\\\\", r"\\")
<re.Match object; span=(0, 1), match='\\'>
~~~

### 写一个词法分析器¶

一个 [词法器或词法分析器](https://en.wikipedia.org/wiki/Lexical_analysis) 分析字符串，并分类成目录组。 这是写一个编译器或解释器的第一步。

文字目录是由正则表达式指定的。这个技术是通过将这些样式合并为一个主正则式，并且循环匹配来实现的

    
    
~~~
from typing import NamedTuple
import re

class Token(NamedTuple):
    type: str
    value: str
    line: int
    column: int

def tokenize(code):
    keywords = {'IF', 'THEN', 'ENDIF', 'FOR', 'NEXT', 'GOSUB', 'RETURN'}
    token_specification = [
        ('NUMBER',   r'\d+(\.\d*)?'),  # Integer or decimal number
        ('ASSIGN',   r':='),           # Assignment operator
        ('END',      r';'),            # Statement terminator
        ('ID',       r'[A-Za-z]+'),    # Identifiers
        ('OP',       r'[+\-*/]'),      # Arithmetic operators
        ('NEWLINE',  r'\n'),           # Line endings
        ('SKIP',     r'[ \t]+'),       # Skip over spaces and tabs
        ('MISMATCH', r'.'),            # Any other character
    ]
    tok_regex = '|'.join('(?P<%s>%s)' % pair for pair in token_specification)
    line_num = 1
    line_start = 0
    for mo in re.finditer(tok_regex, code):
        kind = mo.lastgroup
        value = mo.group()
        column = mo.start() - line_start
        if kind == 'NUMBER':
            value = float(value) if '.' in value else int(value)
        elif kind == 'ID' and value in keywords:
            kind = value
        elif kind == 'NEWLINE':
            line_start = mo.end()
            line_num += 1
            continue
        elif kind == 'SKIP':
            continue
        elif kind == 'MISMATCH':
            raise RuntimeError(f'{value!r} unexpected on line {line_num}')
        yield Token(kind, value, line_num, column)

statements = '''
    IF quantity THEN
        total := total + price * quantity;
        tax := price * 0.05;
    ENDIF;
'''

for token in tokenize(statements):
    print(token)
~~~

该词法器产生以下的输出

    
    
~~~
Token(type='IF', value='IF', line=2, column=4)
Token(type='ID', value='quantity', line=2, column=7)
Token(type='THEN', value='THEN', line=2, column=16)
Token(type='ID', value='total', line=3, column=8)
Token(type='ASSIGN', value=':=', line=3, column=14)
Token(type='ID', value='total', line=3, column=17)
Token(type='OP', value='+', line=3, column=23)
Token(type='ID', value='price', line=3, column=25)
Token(type='OP', value='*', line=3, column=31)
Token(type='ID', value='quantity', line=3, column=33)
Token(type='END', value=';', line=3, column=41)
Token(type='ID', value='tax', line=4, column=8)
Token(type='ASSIGN', value=':=', line=4, column=12)
Token(type='ID', value='price', line=4, column=15)
Token(type='OP', value='*', line=4, column=21)
Token(type='NUMBER', value=0.05, line=4, column=23)
Token(type='END', value=';', line=4, column=27)
Token(type='ENDIF', value='ENDIF', line=5, column=4)
Token(type='END', value=';', line=5, column=9)
~~~

[Frie09]

Friedl, Jeffrey. Mastering Regular Expressions. 3rd ed., O'Reilly Media, 2009. 该书的第三版不再包含 Python，但第一版极详细地覆盖了正则表达式模式串的编写。

