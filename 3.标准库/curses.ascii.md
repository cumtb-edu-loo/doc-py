# `curses.ascii` \--- 用于 ASCII 字符的工具¶

**源代码:** [Lib/curses/ascii.py](https://github.com/python/cpython/tree/3.12/Lib/curses/ascii.py)

* * *

`curses.ascii` 模块提供了一些 ASCII 字符的名称常量以及在各种 ASCII 字符类中执行成员检测的函数。 所提供的控制字符常量如下:

名称

|

含意  
  
---|---  
  
curses.ascii.NUL¶

    
|  
  
curses.ascii.SOH¶

    
|

标题开始，控制台中断  
  
curses.ascii.STX¶

    
|

文本开始  
  
curses.ascii.ETX¶

    
|

文本结束  
  
curses.ascii.EOT¶

    
|

传输结束  
  
curses.ascii.ENQ¶

    
|

查询，附带 `ACK` 流量控制  
  
curses.ascii.ACK¶

    
|

确认  
  
curses.ascii.BEL¶

    
|

蜂鸣器  
  
curses.ascii.BS¶

    
|

退格  
  
curses.ascii.TAB¶

    
|

制表符  
  
curses.ascii.HT¶

    
|

`TAB` 的别名： "水平制表符”  
  
curses.ascii.LF¶

    
|

换行  
  
curses.ascii.NL¶

    
|

`LF` 的别名： "新行"  
  
curses.ascii.VT¶

    
|

垂直制表符  
  
curses.ascii.FF¶

    
|

换页  
  
curses.ascii.CR¶

    
|

回车  
  
curses.ascii.SO¶

    
|

Shift-out，开始替换字符集  
  
curses.ascii.SI¶

    
|

Shift-in，恢复默认字符集  
  
curses.ascii.DLE¶

    
|

Data-link escape，数据链接转义  
  
curses.ascii.DC1¶

    
|

XON，用于流程控制  
  
curses.ascii.DC2¶

    
|

Device control 2，块模式流程控制  
  
curses.ascii.DC3¶

    
|

XOFF，用于流程控制  
  
curses.ascii.DC4¶

    
|

设备控制4  
  
curses.ascii.NAK¶

    
|

否定确认  
  
curses.ascii.SYN¶

    
|

同步空闲  
  
curses.ascii.ETB¶

    
|

末端传输块  
  
curses.ascii.CAN¶

    
|

取消  
  
curses.ascii.EM¶

    
|

媒体结束  
  
curses.ascii.SUB¶

    
|

替换  
  
curses.ascii.ESC¶

    
|

退出  
  
curses.ascii.FS¶

    
|

文件分隔符  
  
curses.ascii.GS¶

    
|

组分隔符  
  
curses.ascii.RS¶

    
|

Record separator，块模式终止符  
  
curses.ascii.US¶

    
|

单位分隔符  
  
curses.ascii.SP¶

    
|

空格  
  
curses.ascii.DEL¶

    
|

删除  
  
请注意其中有许多在现今已经没有实际作用。 这些助记符是来源于数字计算机之前的电传打印机规范。

此模块提供了下列函数，对应于标准 C 库中的函数:

curses.ascii.isalnum( _c_ )¶

    

~~~
检测 ASCII 字母数字类字符；它等价于 `isalpha(c) 或 isdigit(c)`。

curses.ascii.isalpha( _c_ )¶
~~~
    

~~~
检测 ASCII 字母类字符；它等价于 `isupper(c) or islower(c)`。

curses.ascii.isascii( _c_ )¶
~~~
    

~~~
检测字符值是否在 7 位 ASCII 集范围内。

curses.ascii.isblank( _c_ )¶
~~~
    

~~~
检测 ASCII 空白字符；包括空格或水平制表符。

curses.ascii.iscntrl( _c_ )¶
~~~
    

~~~
检测 ASCII 控制字符（在 0x00 到 0x1f 或 0x7f 范围内）。

curses.ascii.isdigit( _c_ )¶
~~~
    

~~~
检测 ASCII 十进制数码，即 `'0'` 至 `'9'`。 它等价于 `c in string.digits`。

curses.ascii.isgraph( _c_ )¶
~~~
    

~~~
检测任意 ASCII 可打印字符，不包括空白符。

curses.ascii.islower( _c_ )¶
~~~
    

~~~
检测 ASCII 小写字母字符。

curses.ascii.isprint( _c_ )¶
~~~
    

~~~
检测任意 ASCII 可打印字符，包括空白符。

curses.ascii.ispunct( _c_ )¶
~~~
    

~~~
检测任意 ASCII 可打印字符，不包括空白符或字母数字类字符。

curses.ascii.isspace( _c_ )¶
~~~
    

~~~
检测 ASCII 空白字符；包括空格，换行，回车，进纸，水平制表和垂直制表。

curses.ascii.isupper( _c_ )¶
~~~
    

~~~
检测 ASCII 大写字母字符。

curses.ascii.isxdigit( _c_ )¶
~~~
    

~~~
检测 ASCII 十六进制数码。 这等价于 `c in string.hexdigits`。

curses.ascii.isctrl( _c_ )¶
~~~
    

~~~
检测 ASCII 控制字符（码位值 0 至 31）。

curses.ascii.ismeta( _c_ )¶
~~~
    

~~~
检测非 ASCII 字符（码位值 0x80 及以上）。

这些函数接受整数或单字符字符串；当参数为字符串时，会先使用内置函数 [`ord()`](functions.md#ord "ord") 进行转换。

请注意所有这些函数都是检测根据你传入的字符串的字符所生成的码位值；它们实际上完全不会知晓本机的字符编码格式。

以下两个函数接受单字符字符串或整数形式的字节值；它们会返回相同类型的值。

curses.ascii.ascii( _c_ )¶
~~~
    

~~~
返回对应于 _c_ 的下个 7 比特位的 ASCII 值。

curses.ascii.ctrl( _c_ )¶
~~~
    

~~~
返回对应于给定字符的控制字符（字符比特值会与 0x1f 进行按位与运算）。

curses.ascii.alt( _c_ )¶
~~~
    

~~~
返回对应于给定 ASCII 字符的 8 比特位字符（字符比特值会与 0x80 进行按位或运算）。

以下函数接受单字符字符串或整数值；它会返回一个字符串。

curses.ascii.unctrl( _c_ )¶
~~~
    

~~~
返回 ASCII 字符 _c_ 的字符串表示形式。 如果 _c_ 是可打印字符，则字符串为字符本身。 如果该字符是控制字符 (0x00--0x1f) 则字符串由一个插入符 (`'^'`) 加相应的大写字母组成。 如果该字符是 ASCII 删除符 (0x7f) 则字符串为 `'^?'`。 如果该字符设置了元比特位 (0x80)，元比特位会被去除，应用以上规则后将在结果之前添加 `'!'`。

curses.ascii.controlnames¶
~~~
    

~~~
