# 1\. 在其它应用程序嵌入 Python¶

前几章讨论了如何对 Python 进行扩展，也就是如何用 C 函数库 扩展 Python 的功能。反过来也是可以的：将 Python 嵌入到 C/C++ 应用程序中丰富其功能。这种嵌入可以让应用程序用 Python 来实现某些功能，而不是用 C 或 C++ 。用途会有很多；比如允许用户用 Python 编写一些脚本，以便定制应用程序满足需求。如果某些功能用 Python 编写起来更为容易，那么开发人员自己也能这么干。

Python 的嵌入类似于扩展，但不完全相同。不同之处在于，扩展 Python 时应用程序的主程序仍然是 Python 解释器，而嵌入 Python 时的主程序可能与 Python 完全无关——而是应用程序的某些部分偶尔会调用 Python 解释器来运行一些 Python 代码。

因此，若要嵌入 Python，就要提供自己的主程序。此主程序要做的事情之一就是初始化 Python 解释器。至少得调用函数 [`Py_Initialize()`](../c-api/init.md#c.Py_Initialize "Py_Initialize")。还有些可选的调用可向 Python 传递命令行参数。之后即可从应用程序的任何地方调用解释器了。

调用解释器的方式有好几种：可向 [`PyRun_SimpleString()`](../c-api/veryhigh.md#c.PyRun_SimpleString "PyRun_SimpleString") 传入一个包含 Python 语句的字符串，也可向 [`PyRun_SimpleFile()`](../c-api/veryhigh.md#c.PyRun_SimpleFile "PyRun_SimpleFile") 传入一个 stdio 文件指针和一个文件名（仅在错误信息中起到识别作用）。还可以调用前面介绍过的底层操作来构造并使用 Python 对象。

参见

[Python/C API 参考手册](../c-api/index.md#c-api-index)

    

~~~
本文详细介绍了 Python 的 C 接口。这里有大量必要的信息。

## 1.1. 高层次的嵌入¶

最简单的 Python 嵌入形式就是采用非常高层的接口。该接口的目标是只执行一段 Python 脚本，而无需与应用程序直接交互。比如以下代码可以用来对某个文件进行一些操作。
~~~
    
    
~~~cpp
#define PY_SSIZE_T_CLEAN
#include <Python.h>

int
main(int argc, char *argv[])
{
    PyStatus status;
    PyConfig config;
    PyConfig_InitPythonConfig(&config);

    /* optional but recommended */
    status = PyConfig_SetBytesString(&config, &config.program_name, argv[0]);
    if (PyStatus_Exception(status)) {
        goto exception;
    }

    status = Py_InitializeFromConfig(&config);
    if (PyStatus_Exception(status)) {
        goto exception;
    }
    PyConfig_Clear(&config);

    PyRun_SimpleString("from time import time,ctime\n"
                       "print('Today is', ctime(time()))\n");
    if (Py_FinalizeEx() < 0) {
        exit(120);
    }
    return 0;

  exception:
     PyConfig_Clear(&config);
     Py_ExitStatusException(status);
}
~~~

备注

`#define PY_SSIZE_T_CLEAN` was used to indicate that `Py_ssize_t` should be used in some APIs instead of `int`. It is not necessary since Python 3.13, but we keep it here for backward compatibility. See [字符串和缓存区](../c-api/arg.md#arg-parsing-string-and-buffers) for a description of this macro.

Setting [`PyConfig.program_name`](../c-api/init_config.md#c.PyConfig.program_name "PyConfig.program_name") should be called before [`Py_InitializeFromConfig()`](../c-api/init_config.md#c.Py_InitializeFromConfig "Py_InitializeFromConfig") to inform the interpreter about paths to Python run-time libraries. Next, the Python interpreter is initialized with [`Py_Initialize()`](../c-api/init.md#c.Py_Initialize "Py_Initialize"), followed by the execution of a hard-coded Python script that prints the date and time. Afterwards, the [`Py_FinalizeEx()`](../c-api/init.md#c.Py_FinalizeEx "Py_FinalizeEx") call shuts the interpreter down, followed by the end of the program. In a real program, you may want to get the Python script from another source, perhaps a text-editor routine, a file, or a database. Getting the Python code from a file can better be done by using the [`PyRun_SimpleFile()`](../c-api/veryhigh.md#c.PyRun_SimpleFile "PyRun_SimpleFile") function, which saves you the trouble of allocating memory space and loading the file contents.

## 1.2. 突破高层次嵌入的限制：概述¶

高级接口能从应用程序中执行任何 Python 代码，但至少交换数据可说是相当麻烦的。如若需要交换数据，应使用较低级别的调用。几乎可以实现任何功能，代价是得写更多的 C 代码。

应该注意，尽管意图不同，但扩展 Python 和嵌入 Python 的过程相当类似。前几章中讨论的大多数主题依然有效。为了说明这一点，不妨来看一下从 Python 到 C 的扩展代码到底做了什么：

  1. 将 Python 的数据转换为 C 格式，

  2. 用转换后的数据执行 C 程序的函数调用，

  3. 将调用返回的数据从 C 转换为 Python 格式。

嵌入 Python 时，接口代码会这样做：

  1. 将 C 数据转换为 Python 格式，

  2. 用转换后的数据执行对 Python 接口的函数调用，

  3. 将调用返回的数据从 Python 转换为 C 格式。

可见只是数据转换的步骤交换了一下顺序，以顺应跨语言的传输方向。唯一的区别是在两次数据转换之间调用的函数不同。在执行扩展时，调用一个 C 函数，而执行嵌入时调用的是个 Python 函数。

本文不会讨论如何将数据从 Python 转换到 C 去，反之亦然。另外还假定读者能够正确使用引用并处理错误。由于这些地方与解释器的扩展没有区别，请参考前面的章节以获得所需的信息。

## 1.3. 只做嵌入¶

第一个程序的目标是执行 Python 脚本中的某个函数。就像高层次接口那样，Python 解释器并不会直接与应用程序进行交互（但下一节将改变这一点）。

要运行 Python 脚本中定义的函数，代码如下：

    
    
~~~cpp
#define PY_SSIZE_T_CLEAN
#include <Python.h>

int
main(int argc, char *argv[])
{
    PyObject *pName, *pModule, *pFunc;
    PyObject *pArgs, *pValue;
    int i;

    if (argc < 3) {
        fprintf(stderr,"Usage: call pythonfile funcname [args]\n");
        return 1;
    }

    Py_Initialize();
    pName = PyUnicode_DecodeFSDefault(argv[1]);
    /* Error checking of pName left out */

    pModule = PyImport_Import(pName);
    Py_DECREF(pName);

    if (pModule != NULL) {
        pFunc = PyObject_GetAttrString(pModule, argv[2]);
        /* pFunc is a new reference */

        if (pFunc && PyCallable_Check(pFunc)) {
            pArgs = PyTuple_New(argc - 3);
            for (i = 0; i < argc - 3; ++i) {
                pValue = PyLong_FromLong(atoi(argv[i + 3]));
                if (!pValue) {
                    Py_DECREF(pArgs);
                    Py_DECREF(pModule);
                    fprintf(stderr, "Cannot convert argument\n");
                    return 1;
                }
                /* pValue reference stolen here: */
                PyTuple_SetItem(pArgs, i, pValue);
            }
            pValue = PyObject_CallObject(pFunc, pArgs);
            Py_DECREF(pArgs);
            if (pValue != NULL) {
                printf("Result of call: %ld\n", PyLong_AsLong(pValue));
                Py_DECREF(pValue);
            }
            else {
                Py_DECREF(pFunc);
                Py_DECREF(pModule);
                PyErr_Print();
                fprintf(stderr,"Call failed\n");
                return 1;
            }
        }
        else {
            if (PyErr_Occurred())
                PyErr_Print();
            fprintf(stderr, "Cannot find function \"%s\"\n", argv[2]);
        }
        Py_XDECREF(pFunc);
        Py_DECREF(pModule);
    }
    else {
        PyErr_Print();
        fprintf(stderr, "Failed to load \"%s\"\n", argv[1]);
        return 1;
    }
    if (Py_FinalizeEx() < 0) {
        return 120;
    }
    return 0;
}
~~~

上述代码先利用 `argv[1]` 加载 Python 脚本，再调用 `argv[2]` 指定的函数。函数的整数参数是 `argv` 数组中的其余值。如果 编译并链接 该程序（此处将最终的可执行程序称作 **call** ）, 并用它执行一个 Python 脚本，例如：

    
    
~~~python
def multiply(a,b):
    print("Will compute", a, "times", b)
    c = 0
    for i in range(0, a):
        c = c + b
    return c
~~~

然后结果应该是：

    
    
~~~
$ call multiply multiply 3 2
Will compute 3 times 2
Result of call: 6
~~~

尽管相对其功能而言，该程序体积相当庞大，但大部分代码是用于 Python 和 C 之间的数据转换，以及报告错误。嵌入 Python 的有趣部分从此开始：

    
    
~~~
Py_Initialize();
pName = PyUnicode_DecodeFSDefault(argv[1]);
/* Error checking of pName left out */
pModule = PyImport_Import(pName);
~~~

初始化解释器之后，则用 [`PyImport_Import()`](../c-api/import.md#c.PyImport_Import "PyImport_Import") 加载脚本。此函数的参数需是个 Python 字符串，一个用 [`PyUnicode_FromString()`](../c-api/unicode.md#c.PyUnicode_FromString "PyUnicode_FromString") 数据转换函数构建的字符串。

    
    
~~~
pFunc = PyObject_GetAttrString(pModule, argv[2]);
/* pFunc is a new reference */

if (pFunc && PyCallable_Check(pFunc)) {
    ...
}
Py_XDECREF(pFunc);
~~~

脚本一旦加载完毕，就会用 [`PyObject_GetAttrString()`](../c-api/object.md#c.PyObject_GetAttrString "PyObject_GetAttrString") 查找属性名称。如果名称存在，并且返回的是可调用对象，即可安全地视其为函数。然后程序继续执行，照常构建由参数组成的元组。然后用以下方式调用 Python 函数：

    
    
~~~
pValue = PyObject_CallObject(pFunc, pArgs);
~~~

当函数返回时，`pValue` 要么为 `NULL`，要么包含对函数返回值的引用。请确保用完后释放该引用。

## 1.4. 对嵌入 Python 功能进行扩展¶

到目前为止，嵌入的 Python 解释器还不能访问应用程序本身的功能。Python API 通过扩展嵌入解释器实现了这一点。 也就是说，用应用程序提供的函数对嵌入的解释器进行扩展。虽然听起来有些复杂，但也没那么糟糕。只要暂时忘记是应用程序启动了 Python 解释器。而把应用程序看作是一堆子程序，然后写一些胶水代码让 Python 访问这些子程序，就像编写普通的 Python 扩展程序一样。 例如：

    
    
~~~cpp
static int numargs=0;

/* Return the number of arguments of the application command line */
static PyObject*
emb_numargs(PyObject *self, PyObject *args)
{
    if(!PyArg_ParseTuple(args, ":numargs"))
        return NULL;
    return PyLong_FromLong(numargs);
}

static PyMethodDef EmbMethods[] = {
    {"numargs", emb_numargs, METH_VARARGS,
     "Return the number of arguments received by the process."},
    {NULL, NULL, 0, NULL}
};

static PyModuleDef EmbModule = {
    PyModuleDef_HEAD_INIT, "emb", NULL, -1, EmbMethods,
    NULL, NULL, NULL, NULL
};

static PyObject*
PyInit_emb(void)
{
    return PyModule_Create(&EmbModule);
}
~~~

在 `main()` 函数之前插入上述代码。并在调用 [`Py_Initialize()`](../c-api/init.md#c.Py_Initialize "Py_Initialize") 之前插入以下两条语句：

    
    
~~~
numargs = argc;
PyImport_AppendInittab("emb", &PyInit_emb);
~~~

这两行代码初始化了 `numargs` 变量，并使嵌入式 Python 解释器可以访问 `emb.numargs()` 函数。通过这些扩展，Python 脚本可以执行以下操作

    
    
~~~
import emb
print("Number of arguments", emb.numargs())
~~~

在真实的应用程序中，这种方法将把应用的 API 暴露给 Python 使用。

## 1.5. 在 C++ 中嵌入 Python¶

还可以将 Python 嵌入到 C++ 程序中去；确切地说，实现方式将取决于 C++ 系统的实现细节；一般需用 C++ 编写主程序，并用 C++ 编译器来编译和链接程序。不需要用 C++ 重新编译 Python 本身。

## 1.6. 在类 Unix 系统中编译和链接¶

为了将 Python 解释器嵌入应用程序，找到正确的编译参数传给编译器 (和链接器) 并非易事，特别是因为 Python 加载的库模块是以 C 动态扩展（`.so` 文件）的形式实现的。

为了得到所需的编译器和链接器参数，可执行 `python _X.Y_ -config` 脚本，它是在安装 Python 时生成的（也可能存在 `python3-config` 脚本）。该脚本有几个参数，其中以下几个参数会直接有用：

  * `pythonX.Y-config --cflags` 将给出建议的编译参数。
    
        $ /opt/bin/python3.11-config --cflags
    -I/opt/include/python3.11 -I/opt/include/python3.11 -Wsign-compare  -DNDEBUG -g -fwrapv -O3 -Wall
    

  * `pythonX.Y-config --ldflags --embed` 将给出在链接时建议的旗标:
    
        $ /opt/bin/python3.11-config --ldflags --embed
    -L/opt/lib/python3.11/config-3.11-x86_64-linux-gnu -L/opt/lib -lpython3.11 -lpthread -ldl  -lutil -lm
    

备注

为了避免多个 Python 安装版本引发混乱（特别是在系统安装版本和自己编译版本之间），建议用 `python _X.Y_ -config` 指定绝对路径，如上例所述。

如果上述方案不起作用（不能保证对所有 Unix 类平台都生效；欢迎提出 [bug 报告](../bugs.md#reporting-bugs)），就得阅读系统关于动态链接的文档，并检查 Python 的 `Makefile` （用 [`sysconfig.get_makefile_filename()`](../library/sysconfig.md#sysconfig.get_makefile_filename "sysconfig.get_makefile_filename") 找到所在位置）和编译参数。这时 [`sysconfig`](../library/sysconfig.md#module-sysconfig "sysconfig: Python's configuration information") 模块会是个有用的工具，可用编程方式提取需组合在一起的配置值。比如：

    
    
~~~shell
>>> import sysconfig
>>> sysconfig.get_config_var('LIBS')
'-lpthread -ldl  -lutil'
>>> sysconfig.get_config_var('LINKFORSHARED')
'-Xlinker -export-dynamic'
~~~

