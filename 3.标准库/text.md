# 文本处理服务¶

本章介绍的模块提供了广泛的字符串操作和其他文本处理服务。

在 [二进制数据服务](binary.md#binaryservices) 之下描述的 [`codecs`](codecs.md#module-codecs "codecs: Encode and decode data and streams.") 模块也与文本处理高度相关。 此外也请参阅 Python 内置字符串类型的文档 [文本序列类型 --- str](stdtypes.md#textseq)。

  * [`string` \--- 常见的字符串操作](string.md)
    * [字符串常量](string.md#string-constants)
      * [`ascii_letters`](string.md#string.ascii_letters)
      * [`ascii_lowercase`](string.md#string.ascii_lowercase)
      * [`ascii_uppercase`](string.md#string.ascii_uppercase)
      * [`digits`](string.md#string.digits)
      * [`hexdigits`](string.md#string.hexdigits)
      * [`octdigits`](string.md#string.octdigits)
      * [`punctuation`](string.md#string.punctuation)
      * [`printable`](string.md#string.printable)
      * [`whitespace`](string.md#string.whitespace)
    * [自定义字符串格式化](string.md#custom-string-formatting)
      * [`Formatter`](string.md#string.Formatter)
        * [`Formatter.format()`](string.md#string.Formatter.format)
        * [`Formatter.vformat()`](string.md#string.Formatter.vformat)
        * [`Formatter.parse()`](string.md#string.Formatter.parse)
        * [`Formatter.get_field()`](string.md#string.Formatter.get_field)
        * [`Formatter.get_value()`](string.md#string.Formatter.get_value)
        * [`Formatter.check_unused_args()`](string.md#string.Formatter.check_unused_args)
        * [`Formatter.format_field()`](string.md#string.Formatter.format_field)
        * [`Formatter.convert_field()`](string.md#string.Formatter.convert_field)
    * [格式字符串语法](string.md#format-string-syntax)
      * [格式规格迷你语言](string.md#format-specification-mini-language)
      * [格式示例](string.md#format-examples)
    * [模板字符串](string.md#template-strings)
      * [`Template`](string.md#string.Template)
        * [`Template.substitute()`](string.md#string.Template.substitute)
        * [`Template.safe_substitute()`](string.md#string.Template.safe_substitute)
        * [`Template.is_valid()`](string.md#string.Template.is_valid)
        * [`Template.get_identifiers()`](string.md#string.Template.get_identifiers)
        * [`Template.template`](string.md#string.Template.template)
    * [辅助函数](string.md#helper-functions)
      * [`capwords()`](string.md#string.capwords)
  * [`re` \--- 正则表达式操作](re.md)
    * [正则表达式语法](re.md#regular-expression-syntax)
    * [模块内容](re.md#module-contents)
      * [标志](re.md#flags)
        * [`RegexFlag`](re.md#re.RegexFlag)
        * [`A`](re.md#re.A)
        * [`ASCII`](re.md#re.ASCII)
        * [`DEBUG`](re.md#re.DEBUG)
        * [`I`](re.md#re.I)
        * [`IGNORECASE`](re.md#re.IGNORECASE)
        * [`L`](re.md#re.L)
        * [`LOCALE`](re.md#re.LOCALE)
        * [`M`](re.md#re.M)
        * [`MULTILINE`](re.md#re.MULTILINE)
        * [`NOFLAG`](re.md#re.NOFLAG)
        * [`S`](re.md#re.S)
        * [`DOTALL`](re.md#re.DOTALL)
        * [`U`](re.md#re.U)
        * [`UNICODE`](re.md#re.UNICODE)
        * [`X`](re.md#re.X)
        * [`VERBOSE`](re.md#re.VERBOSE)
      * [函数](re.md#functions)
        * [`compile()`](re.md#re.compile)
        * [`search()`](re.md#re.search)
        * [`match()`](re.md#re.match)
        * [`fullmatch()`](re.md#re.fullmatch)
        * [`split()`](re.md#re.split)
        * [`findall()`](re.md#re.findall)
        * [`finditer()`](re.md#re.finditer)
        * [`sub()`](re.md#re.sub)
        * [`subn()`](re.md#re.subn)
        * [`escape()`](re.md#re.escape)
        * [`purge()`](re.md#re.purge)
      * [异常](re.md#exceptions)
        * [`error`](re.md#re.error)
          * [`error.msg`](re.md#re.error.msg)
          * [`error.pattern`](re.md#re.error.pattern)
          * [`error.pos`](re.md#re.error.pos)
          * [`error.lineno`](re.md#re.error.lineno)
          * [`error.colno`](re.md#re.error.colno)
    * [正则表达式对象 （正则对象）](re.md#regular-expression-objects)
      * [`Pattern`](re.md#re.Pattern)
        * [`Pattern.search()`](re.md#re.Pattern.search)
        * [`Pattern.match()`](re.md#re.Pattern.match)
        * [`Pattern.fullmatch()`](re.md#re.Pattern.fullmatch)
        * [`Pattern.split()`](re.md#re.Pattern.split)
        * [`Pattern.findall()`](re.md#re.Pattern.findall)
        * [`Pattern.finditer()`](re.md#re.Pattern.finditer)
        * [`Pattern.sub()`](re.md#re.Pattern.sub)
        * [`Pattern.subn()`](re.md#re.Pattern.subn)
        * [`Pattern.flags`](re.md#re.Pattern.flags)
        * [`Pattern.groups`](re.md#re.Pattern.groups)
        * [`Pattern.groupindex`](re.md#re.Pattern.groupindex)
        * [`Pattern.pattern`](re.md#re.Pattern.pattern)
    * [匹配对象](re.md#match-objects)
      * [`Match`](re.md#re.Match)
        * [`Match.expand()`](re.md#re.Match.expand)
        * [`Match.group()`](re.md#re.Match.group)
        * [`Match.__getitem__()`](re.md#re.Match.__getitem__)
        * [`Match.groups()`](re.md#re.Match.groups)
        * [`Match.groupdict()`](re.md#re.Match.groupdict)
        * [`Match.start()`](re.md#re.Match.start)
        * [`Match.end()`](re.md#re.Match.end)
        * [`Match.span()`](re.md#re.Match.span)
        * [`Match.pos`](re.md#re.Match.pos)
        * [`Match.endpos`](re.md#re.Match.endpos)
        * [`Match.lastindex`](re.md#re.Match.lastindex)
        * [`Match.lastgroup`](re.md#re.Match.lastgroup)
        * [`Match.re`](re.md#re.Match.re)
        * [`Match.string`](re.md#re.Match.string)
    * [正则表达式例子](re.md#regular-expression-examples)
      * [检查对子](re.md#checking-for-a-pair)
      * [模拟 scanf()](re.md#simulating-scanf)
      * [search() vs. match()](re.md#search-vs-match)
      * [制作一个电话本](re.md#making-a-phonebook)
      * [文字整理](re.md#text-munging)
      * [查找所有副词](re.md#finding-all-adverbs)
      * [查找所有的副词及其位置](re.md#finding-all-adverbs-and-their-positions)
      * [原始字符串标记](re.md#raw-string-notation)
      * [写一个词法分析器](re.md#writing-a-tokenizer)
  * [`difflib` \--- 计算差异的辅助工具](difflib.md)
    * [`Differ`](difflib.md#difflib.Differ)
    * [`HtmlDiff`](difflib.md#difflib.HtmlDiff)
      * [`HtmlDiff.__init__()`](difflib.md#difflib.HtmlDiff.__init__)
      * [`HtmlDiff.make_file()`](difflib.md#difflib.HtmlDiff.make_file)
      * [`HtmlDiff.make_table()`](difflib.md#difflib.HtmlDiff.make_table)
    * [`context_diff()`](difflib.md#difflib.context_diff)
    * [`get_close_matches()`](difflib.md#difflib.get_close_matches)
    * [`ndiff()`](difflib.md#difflib.ndiff)
    * [`restore()`](difflib.md#difflib.restore)
    * [`unified_diff()`](difflib.md#difflib.unified_diff)
    * [`diff_bytes()`](difflib.md#difflib.diff_bytes)
    * [`IS_LINE_JUNK()`](difflib.md#difflib.IS_LINE_JUNK)
    * [`IS_CHARACTER_JUNK()`](difflib.md#difflib.IS_CHARACTER_JUNK)
    * [SequenceMatcher 对象](difflib.md#sequencematcher-objects)
      * [`SequenceMatcher`](difflib.md#difflib.SequenceMatcher)
        * [`SequenceMatcher.set_seqs()`](difflib.md#difflib.SequenceMatcher.set_seqs)
        * [`SequenceMatcher.set_seq1()`](difflib.md#difflib.SequenceMatcher.set_seq1)
        * [`SequenceMatcher.set_seq2()`](difflib.md#difflib.SequenceMatcher.set_seq2)
        * [`SequenceMatcher.find_longest_match()`](difflib.md#difflib.SequenceMatcher.find_longest_match)
        * [`SequenceMatcher.get_matching_blocks()`](difflib.md#difflib.SequenceMatcher.get_matching_blocks)
        * [`SequenceMatcher.get_opcodes()`](difflib.md#difflib.SequenceMatcher.get_opcodes)
        * [`SequenceMatcher.get_grouped_opcodes()`](difflib.md#difflib.SequenceMatcher.get_grouped_opcodes)
        * [`SequenceMatcher.ratio()`](difflib.md#difflib.SequenceMatcher.ratio)
        * [`SequenceMatcher.quick_ratio()`](difflib.md#difflib.SequenceMatcher.quick_ratio)
        * [`SequenceMatcher.real_quick_ratio()`](difflib.md#difflib.SequenceMatcher.real_quick_ratio)
    * [SequenceMatcher 的示例](difflib.md#sequencematcher-examples)
    * [Differ 对象](difflib.md#differ-objects)
      * [`Differ.compare()`](difflib.md#difflib.Differ.compare)
    * [Differ 示例](difflib.md#differ-example)
    * [difflib 的命令行接口](difflib.md#a-command-line-interface-to-difflib)
    * [ndiff 示例](difflib.md#ndiff-example)
  * [`textwrap` \--- 文本自动换行与填充](textwrap.md)
    * [`wrap()`](textwrap.md#textwrap.wrap)
    * [`fill()`](textwrap.md#textwrap.fill)
    * [`shorten()`](textwrap.md#textwrap.shorten)
    * [`dedent()`](textwrap.md#textwrap.dedent)
    * [`indent()`](textwrap.md#textwrap.indent)
    * [`TextWrapper`](textwrap.md#textwrap.TextWrapper)
      * [`TextWrapper.width`](textwrap.md#textwrap.TextWrapper.width)
      * [`TextWrapper.expand_tabs`](textwrap.md#textwrap.TextWrapper.expand_tabs)
      * [`TextWrapper.tabsize`](textwrap.md#textwrap.TextWrapper.tabsize)
      * [`TextWrapper.replace_whitespace`](textwrap.md#textwrap.TextWrapper.replace_whitespace)
      * [`TextWrapper.drop_whitespace`](textwrap.md#textwrap.TextWrapper.drop_whitespace)
      * [`TextWrapper.initial_indent`](textwrap.md#textwrap.TextWrapper.initial_indent)
      * [`TextWrapper.subsequent_indent`](textwrap.md#textwrap.TextWrapper.subsequent_indent)
      * [`TextWrapper.fix_sentence_endings`](textwrap.md#textwrap.TextWrapper.fix_sentence_endings)
      * [`TextWrapper.break_long_words`](textwrap.md#textwrap.TextWrapper.break_long_words)
      * [`TextWrapper.break_on_hyphens`](textwrap.md#textwrap.TextWrapper.break_on_hyphens)
      * [`TextWrapper.max_lines`](textwrap.md#textwrap.TextWrapper.max_lines)
      * [`TextWrapper.placeholder`](textwrap.md#textwrap.TextWrapper.placeholder)
      * [`TextWrapper.wrap()`](textwrap.md#textwrap.TextWrapper.wrap)
      * [`TextWrapper.fill()`](textwrap.md#textwrap.TextWrapper.fill)
  * [`unicodedata` \--- Unicode 数据库](unicodedata.md)
    * [`lookup()`](unicodedata.md#unicodedata.lookup)
    * [`name()`](unicodedata.md#unicodedata.name)
    * [`decimal()`](unicodedata.md#unicodedata.decimal)
    * [`digit()`](unicodedata.md#unicodedata.digit)
    * [`numeric()`](unicodedata.md#unicodedata.numeric)
    * [`category()`](unicodedata.md#unicodedata.category)
    * [`bidirectional()`](unicodedata.md#unicodedata.bidirectional)
    * [`combining()`](unicodedata.md#unicodedata.combining)
    * [`east_asian_width()`](unicodedata.md#unicodedata.east_asian_width)
    * [`mirrored()`](unicodedata.md#unicodedata.mirrored)
    * [`decomposition()`](unicodedata.md#unicodedata.decomposition)
    * [`normalize()`](unicodedata.md#unicodedata.normalize)
    * [`is_normalized()`](unicodedata.md#unicodedata.is_normalized)
    * [`unidata_version`](unicodedata.md#unicodedata.unidata_version)
    * [`ucd_3_2_0`](unicodedata.md#unicodedata.ucd_3_2_0)
  * [`stringprep` \--- 因特网字符串预备](stringprep.md)
    * [`in_table_a1()`](stringprep.md#stringprep.in_table_a1)
    * [`in_table_b1()`](stringprep.md#stringprep.in_table_b1)
    * [`map_table_b2()`](stringprep.md#stringprep.map_table_b2)
    * [`map_table_b3()`](stringprep.md#stringprep.map_table_b3)
    * [`in_table_c11()`](stringprep.md#stringprep.in_table_c11)
    * [`in_table_c12()`](stringprep.md#stringprep.in_table_c12)
    * [`in_table_c11_c12()`](stringprep.md#stringprep.in_table_c11_c12)
    * [`in_table_c21()`](stringprep.md#stringprep.in_table_c21)
    * [`in_table_c22()`](stringprep.md#stringprep.in_table_c22)
    * [`in_table_c21_c22()`](stringprep.md#stringprep.in_table_c21_c22)
    * [`in_table_c3()`](stringprep.md#stringprep.in_table_c3)
    * [`in_table_c4()`](stringprep.md#stringprep.in_table_c4)
    * [`in_table_c5()`](stringprep.md#stringprep.in_table_c5)
    * [`in_table_c6()`](stringprep.md#stringprep.in_table_c6)
    * [`in_table_c7()`](stringprep.md#stringprep.in_table_c7)
    * [`in_table_c8()`](stringprep.md#stringprep.in_table_c8)
    * [`in_table_c9()`](stringprep.md#stringprep.in_table_c9)
    * [`in_table_d1()`](stringprep.md#stringprep.in_table_d1)
    * [`in_table_d2()`](stringprep.md#stringprep.in_table_d2)
  * [`readline` \--- GNU readline 接口](readline.md)
    * [初始化文件](readline.md#init-file)
      * [`parse_and_bind()`](readline.md#readline.parse_and_bind)
      * [`read_init_file()`](readline.md#readline.read_init_file)
    * [行缓冲区](readline.md#line-buffer)
      * [`get_line_buffer()`](readline.md#readline.get_line_buffer)
      * [`insert_text()`](readline.md#readline.insert_text)
      * [`redisplay()`](readline.md#readline.redisplay)
    * [历史文件](readline.md#history-file)
      * [`read_history_file()`](readline.md#readline.read_history_file)
      * [`write_history_file()`](readline.md#readline.write_history_file)
      * [`append_history_file()`](readline.md#readline.append_history_file)
      * [`get_history_length()`](readline.md#readline.get_history_length)
      * [`set_history_length()`](readline.md#readline.set_history_length)
    * [历史列表](readline.md#history-list)
      * [`clear_history()`](readline.md#readline.clear_history)
      * [`get_current_history_length()`](readline.md#readline.get_current_history_length)
      * [`get_history_item()`](readline.md#readline.get_history_item)
      * [`remove_history_item()`](readline.md#readline.remove_history_item)
      * [`replace_history_item()`](readline.md#readline.replace_history_item)
      * [`add_history()`](readline.md#readline.add_history)
      * [`set_auto_history()`](readline.md#readline.set_auto_history)
    * [启动钩子](readline.md#startup-hooks)
      * [`set_startup_hook()`](readline.md#readline.set_startup_hook)
      * [`set_pre_input_hook()`](readline.md#readline.set_pre_input_hook)
    * [Completion](readline.md#completion)
      * [`set_completer()`](readline.md#readline.set_completer)
      * [`get_completer()`](readline.md#readline.get_completer)
      * [`get_completion_type()`](readline.md#readline.get_completion_type)
      * [`get_begidx()`](readline.md#readline.get_begidx)
      * [`get_endidx()`](readline.md#readline.get_endidx)
      * [`set_completer_delims()`](readline.md#readline.set_completer_delims)
      * [`get_completer_delims()`](readline.md#readline.get_completer_delims)
      * [`set_completion_display_matches_hook()`](readline.md#readline.set_completion_display_matches_hook)
    * [示例](readline.md#example)
  * [`rlcompleter` \--- GNU readline 的补全函数](rlcompleter.md)
    * [Completer 对象](rlcompleter.md#completer-objects)
      * [`Completer.complete()`](rlcompleter.md#rlcompleter.Completer.complete)

