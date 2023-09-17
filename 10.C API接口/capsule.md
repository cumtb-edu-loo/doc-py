# Capsule 对象¶

有关使用这些对象的更多信息请参阅 [给扩展模块提供C API](9.拓展和嵌入/extending.md#using-capsules)。

在 3.1 版本加入.

type PyCapsule¶  

    

~~~
这个 [`PyObject`](structures.md#c.PyObject "PyObject") 的子类型代表一个隐藏的值，适用于需要将隐藏值（作为 void* 指针）通过 Python 代码传递到其他 C 代码的 C 扩展模块。 它常常被用来让在一个模块中定义的 C 函数指针在其他模块中可用，这样就可以使用常规导入机制来访问在动态加载的模块中定义的 C API。

type PyCapsule_Destructor¶  

    _Part of the[ Stable ABI](stable.md#stable)._

Capsule 的析构器回调的类型。 定义如下：
~~~
    
    
~~~
typedef void (*PyCapsule_Destructor)(PyObject *);
~~~

参阅 `PyCapsule_New()` 来获取 PyCapsule_Destructor 返回值的语义。

int PyCapsule_CheckExact([PyObject](structures.md#c.PyObject "PyObject") *p)¶  

    

~~~
