# 处理错误¶

Python 是一门成熟的编程语言，以稳定著称。为维持其美誉，开发者希望获悉所有您在 Python 中发现的缺陷。

有时候自己修复漏洞并将补丁提交给 Python 能更快地解决问题，因为它简化了处理过程并且减少了参与的人力。 请参阅如何 提交补丁。

## 文档 Bug¶

如果您在本文档中发现了错误并打算帮助我们改进，请将错误提交到 追踪系统。 如果您有相应的修正建议，请一并提交。 （译者注：如果是中文文档翻译相关的错误，请务必提交到 <https://github.com/python/python-docs-zh-cn/issues>。如果将翻译问题反馈到了其他地方，可能会因为语言不通而不能及时处理。）

你还可以在我们的 [文档编撰论坛](https://discuss.python.org/c/documentation/26) 上开启一个讨论条目。

如果您时间有限，还可以通过电子邮件将文档的错误报告发送至 [docs@python](mailto:docs%40python).org（代码运行错误请发送至 [python-list@python.org](mailto:python-list%40python.org)）。“docs@”是一个由志愿者运作的邮件列表；您的请求会得到关注，但可能需要一些时间才会被处理。

参见

[Documentation bugs](https://github.com/python/cpython/issues?q=is%3Aissue+is%3Aopen+label%3Adocs)

    

已提交给 Python 问题追踪系统的文档错误列表。

[问题跟踪](https://devguide.python.org/tracker/)


在追踪系统上参与问题改进的过程概述。

[改进文档](https://devguide.python.org/docquality/#helping-with-documentation)

给有兴趣为 Python 文档做出贡献的人。

[文档翻译](https://devguide.python.org/documenting/#translating)

文档翻译的 GitHub 页面及其主要联系人的列表

## 使用 Python 的问题追踪系统¶

针对 Python 本身的问题报告应当通过 GitHub 问题追踪系统 (<https://github.com/python/cpython/issues>) 来提交。 GitHub 问题追踪系统提供了一个网页表单用来输入并提交相关信息给开发者。

第一步是确定问题是否已经被报告了。 这样做的好处除了可以节省开发者的时间，还可以让你了解该问题是如何解决的；有可能该问题已在下一发布版中被修复，或者还需要额外的信息（在此情况下非常欢迎你来提供信息！）。 要确定这一点，请使用页面顶部的搜索框来搜索问题追踪系统。

如果你要报告的问题未出现在列表中，请登录 GitHub 报告。 如果你还没有 GitHub 账号，请使用 "Sign up" 链接新建一个账号。 错误报告是不可能自动被提交的。

当你登录之后，就可以提交问题了。 请点击顶端栏的 "New issue" 按钮来报告新问题。

提交表单有两个字段，"Title" 和 "Comment"。

对于 "Title" 字段，请输入对问题的 _非常_ 简短的描述；最好是少于十个单词。

在“说明（Comment）”栏，请详细描述问题，包括您预期的情况和实际的情况。请确保包含任何涉及的拓展模块，以及您当时所使用的硬件和软件平台（如果可能，请附上版本信息）。

每个问题报告将由一位开发者进行审查并决定要以何种方式来修正该问题。 每当对该问题有新的处理措施时你都会收到更新的消息。

参见

[如何有效地报告错误](https://www.chiark.greenend.org.uk/~sgtatham/bugs.md)
该文章详细介绍了如何创建一份有用的错误报告。它描述了什么样的信息是有用的，以及为什么是有用的。

[Bug Writing Guidelines](https://bugzilla.mozilla.org/page.cgi?id=bug-writing.md)

