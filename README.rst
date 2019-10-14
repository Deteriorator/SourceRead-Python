---------------
Python 代码阅读
---------------

**版本** ： Python 3.7.4

Python 内置函数
---------------

内置函数的源代码无法直接查看，只能从 Python 源代码中阅读。

abs_

.. _abs:

abs()

abs 函数在 Python 源代码中的位置是：``/Python/bltinmodule.c`` 第 343 行，名称\ 
是 builtin_abs()，代码如下：

.. code-block:: c

    /*[clinic input]
    abs as builtin_abs
    x: object
    /
    Return the absolute value of the argument.
    [clinic start generated code]*/

    static PyObject *
    builtin_abs(PyObject *module, PyObject *x)
    /*[clinic end generated code: output=b1b433b9e51356f5 input=bed4ca14e29c20d1]*/
    {
        return PyNumber_Absolute(x);
    }

在该函数中调用了 ``PyNumber_Absolute`` 函数，位于 ``/Objects/abstract.c`` 文件的\
第 1234 行，代码如下：

.. code-block:: c

    PyObject *
    PyNumber_Absolute(PyObject *o)
    {
        PyNumberMethods *m;

        if (o == NULL) {
            return null_error();
        }

        m = o->ob_type->tp_as_number;
        if (m && m->nb_absolute)
            return m->nb_absolute(o);

        return type_error("bad operand type for abs(): '%.200s'", o);
    }

在阅读该代码的时候，需要先看一下 PyObject 和 PyNumberMethods 结构体，\
PyObject 结构体如下：

.. code-block:: c

    typedef struct _object {
        _PyObject_HEAD_EXTRA
        Py_ssize_t ob_refcnt;
        struct _typeobject *ob_type;
    } PyObject;

成员 ``_PyObject_HEAD_EXTRA`` 的宏定义如下：

.. code-block:: c

    #ifdef Py_TRACE_REFS
    /* Define pointers to support a doubly-linked list of all live heap objects. */
    #define _PyObject_HEAD_EXTRA            \
        struct _object *_ob_next;           \
        struct _object *_ob_prev;

    #define _PyObject_EXTRA_INIT 0, 0,

    #else
    #define _PyObject_HEAD_EXTRA
    #define _PyObject_EXTRA_INIT
    #endif

宏定义显示，如果 ``Py_TRACE_REFS`` 定义了，``_PyObject_HEAD_EXTRA`` 才会有值，否\
则就是空值，见上述 ``#define _PyObject_HEAD_EXTRA``。

接下来 ``Py_TRACE_REFS`` 的宏定义如下：

.. code-block:: c

    #if defined(Py_DEBUG) && !defined(Py_TRACE_REFS)
    #define Py_TRACE_REFS
    #endif

代码显示，只有开启 DEBUG 才会定义 ``Py_TRACE_REFS``，所以当前不需要考虑 \
_PyObject_HEAD_EXTRA 。

顺便看一下结构体成员 ``ob_refcnt`` 的定义， ``ob_refcnt`` 是 Py_ssize_t 类型的\
值，Py_ssize_t 的宏定义如下：

.. code-block:: c

    #ifdef HAVE_SSIZE_T
    typedef ssize_t         Py_ssize_t;
    #elif SIZEOF_VOID_P == SIZEOF_SIZE_T
    typedef Py_intptr_t     Py_ssize_t;
    #else
    #   error "Python needs a typedef for Py_ssize_t in pyport.h."
    #endif


    #ifdef MS_WIN64
    typedef __int64 ssize_t;
    #else
    typedef _W64 int ssize_t;
    #endif


    #ifdef _WIN64
    #define MS_WIN64
    #endif


``#define _WIN64`` 这条宏定义在系统中存在，所以在源代码中无法找到。可以从\
代码中看到 Py_ssize_t 是一个64位整型。

``PyNumber_Absolute`` 函数的参数是一个 ``PyObject`` 指针，局部变量 m 为\
参数的 ``ob_type`` 成员的 ``tp_as_number`` 成员。

``ob_type`` 在 ``PyObject`` 结构体中是一个 ``_typeobject`` 的结构体指针，\
其结构在 ``/Include/object.h`` 的 346 行，结构很长，就不粘贴了。他有一个\
成员是 ``PyNumberMethods *tp_as_number;``, 也就是说 tp_as_number 是一个 \
PyNumberMethods 类型，其结构如下：

.. code-block:: c

    typedef struct {
    /* Number implementations must check *both*
       arguments for proper type and implement the necessary conversions
       in the slot functions themselves. */

        binaryfunc nb_add;
        binaryfunc nb_subtract;
        binaryfunc nb_multiply;
        binaryfunc nb_remainder;
        binaryfunc nb_divmod;
        ternaryfunc nb_power;
        unaryfunc nb_negative;
        unaryfunc nb_positive;
        unaryfunc nb_absolute;
        inquiry nb_bool;
        unaryfunc nb_invert;
        binaryfunc nb_lshift;
        binaryfunc nb_rshift;
        binaryfunc nb_and;
        binaryfunc nb_xor;
        binaryfunc nb_or;
        unaryfunc nb_int;
        void *nb_reserved;  /* the slot formerly known as nb_long */
        unaryfunc nb_float;

        binaryfunc nb_inplace_add;
        binaryfunc nb_inplace_subtract;
        binaryfunc nb_inplace_multiply;
        binaryfunc nb_inplace_remainder;
        ternaryfunc nb_inplace_power;
        binaryfunc nb_inplace_lshift;
        binaryfunc nb_inplace_rshift;
        binaryfunc nb_inplace_and;
        binaryfunc nb_inplace_xor;
        binaryfunc nb_inplace_or;

        binaryfunc nb_floor_divide;
        binaryfunc nb_true_divide;
        binaryfunc nb_inplace_floor_divide;
        binaryfunc nb_inplace_true_divide;

        unaryfunc nb_index;

        binaryfunc nb_matrix_multiply;
        binaryfunc nb_inplace_matrix_multiply;
    } PyNumberMethods;


最后返回了 m 的成员 ``nb_absolute(o)``， 而在代码中的定义为 \
``unaryfunc nb_absolute;``，需要先看一下 unaryfunc 类型的定义：
    
``typedef PyObject * (*unaryfunc)(PyObject *);``

unaryfunc 是一个函数指针，指向的函数的参数是一个 ``PyObject *``，返回的是一\
个 ``PyObject *``

举个栗子：

.. code-block:: c

    #include <stdio.h>
    typedef int (*FUNC)(int a, int b);
    int Add(int a, int b);
    void main()
    {
        int x;
        FUNC p;
        p = Add;
        x = (*p)(2,3);
        printf("%d", x);
    }

    int Add(int a, int b)
    {
        return a + b;
    }
