:mod:`uheapq` -- 堆队列算法
=====================================

.. module:: uheapq
   :synopsis: 堆队列算法

该模块实现相应 `CPython` 模块的子集，如下所示。更多信息，请参见
|see_cpython_module| :mod:`python:heapq`.

该模块实现堆队列算法。简而言之，堆队列即为以一定方式储存其所有项的列表。


函数
---------

.. function:: heappush(heap, item)

   将 ``item`` 载入 ``heap`` 中。

.. function:: heappop(heap)

   从 ``heap`` 中提取首个项，并返回。若堆为空，则引发Index错误。

.. function:: heapify(x)

   将列表 ``x`` 转换为一个堆。此为就地操作。
