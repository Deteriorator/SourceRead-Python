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

PyNumberMethods 结构体如下：

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

PyObject结构体如下：

.. code-block:: c

    typedef struct _object {
        _PyObject_HEAD_EXTRA
        Py_ssize_t ob_refcnt;
        struct _typeobject *ob_type;
    } PyObject;

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


    #if defined(Py_DEBUG) && !defined(Py_TRACE_REFS)
    #define Py_TRACE_REFS
    #endif


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


``#define _WIN64`` 这条宏定义在系统中存在，所以在源代码中无法找到。
