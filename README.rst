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

abs 函数在 Python 源代码中的位置是：``/Python/bltinmodule.c`` 第 309 行，名称\ 
是 builtin_abs()

.. code-block:: c
   :linenos:

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

