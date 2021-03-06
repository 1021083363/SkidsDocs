:mod:`sys` -- 系统相关函数
=======================================

.. module:: sys
   :synopsis: 系统相关函数

该模块实现相应CPython模块的子集，如下所示。更多信息，请参见
|see_cpython_module| :mod:`python:sys`.

函数
---------

.. function:: exit(retval=0)

   使用给定的退出代码终止当前程序。该函数出现 `SystemExit` 异常。若给定一个参数，该值作为 `SystemExit` 的参数。

.. function:: print_exception(exc, file=sys.stdout)

   打印异常，并返回到一个类似 *file* 的对象文件（或默认的 `sys.stdout` ）。

   .. admonition:: Difference to CPython
      :class: attention

      该函数出现在CPython的 ``traceback`` 模块中。 与 ``traceback.print_exception()`` 不同，
      该函数只接受异常值，而不接受异常类型、异常值和回溯对象；文件参数应为位置参数；
      不支持更多参数。与CPython兼容的 ``traceback`` 模块可在 `micropython-lib` 中找到。

常量
---------

.. data:: argv

   当前程序由一个可变的参数列表开始。

.. data:: byteorder

   系统的字节顺序（ ``“小端”`` 或 ``“大端”`` ）。

.. data:: implementation

   具有有关当前Python实现的信息的对象。对于MicroPython而言，具有以下属性;

   * *name* - string "micropython"
   * *version* - tuple (major, minor, micro), e.g. (1, 7, 0)

   该对象是将MicroPython与其他Python实现区分开来的推荐方法(注意：该对象仍可能不存在于极小的端口中)。 

   .. admonition:: Difference to CPython
      :class: attention

      CPython为此对象提供更多属性，但是实际有用的最小值是在MicroPython中实现的。

.. data:: maxsize

   一个本地整数类型可在当前平台上保留的最大值，或MicroPython 整数类型所能表示的最大值，若其小于平台最大值（此即Micropython端口在没有长整数支持下的情况）。

   这个属性对于探测平台的位度非常有用（32位vs 64位）。建议不要直接将该属性与某个值进行比较，而是计算其中的比特数;


    bits = 0
    v = sys.maxsize
    while v:
        bits += 1
        v >>= 1
    if bits > 32:
        # 64-bit (or more) platform
        ...
    else:
        # 32-bit (or less) platform
        # Note that on 32-bit platform, value of bits may be less than 32
        # (e.g. 31) due to peculiarities described above, so use "> 16",
        # "> 32", "> 64" style of comparisons.

.. data:: modules

   加载模块库。在某些端口上，可能不包括内置模块。

.. data:: path

   用于搜索导入模块的目录的可变列表。

.. data:: platform

   MicroPython运行的平台。对于OS/RTOS端口而言，通常为OS的标识符，例如： ``"linux"`` 。
   对于裸金属板而言，通常是插件的标识符，例如：原始参考板即对应“pyboard”。可用于将板与板区分开来。
   若您需要检查您的程序是否在MicroPython（vs其他Python实现）上运行，请使用 `sys.implementation` 。

.. data:: stderr

   标准错误流。

.. data:: stdin

   标准输入流。

.. data:: stdout

   标准输出流。

.. data:: version

   该实现所遵照的Python语言版本，字符串形式。

.. data:: version_info

   该实现所遵照的Python语言版本，整数元组形式。
