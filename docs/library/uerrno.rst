:mod:`uerrno` -- 系统错误代码
===================================

.. module:: uerrno
   :synopsis: 系统错误代码

该模块实现相应CPython模块的子集，如下所示。更多信息，请参见
|see_cpython_module| :mod:`python:errno`.

该模块提供对 `OSError` 异常的符号错误代码的访问。特定代码清单依赖于 `MicroPython port` 。

常量
---------

.. data:: EEXIST, EAGAIN, etc.

    错误代码，基于ANSI C/POSIX标准。所有错误代码以“E”开头。如上所述，
    代码清单依赖于 `MicroPython port` 。错误通常可作为 ``exc.args[0]`` 访问，
    其中 ``exc`` 是 `OSError` 的实例。用法示例::

        try:
            uos.mkdir("my_dir")
        except OSError as exc:
            if exc.args[0] == uerrno.EEXIST:
                print("Directory already exists")

.. data:: errorcode

    字典将数字错误代码映射到带有符号错误代码的字符串（见上）::

        >>> print(uerrno.errorcode[uerrno.EEXIST])
        EEXIST