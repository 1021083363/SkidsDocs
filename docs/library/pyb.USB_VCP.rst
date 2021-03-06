.. currentmodule:: pyb

USB_VCP 类– USB 虚拟通讯端口
======================================

USB_VCP类允许创建一个表示USB虚拟通信端口的对象。可用来通过USB 向主机读写数据。

构造函数
------------

.. class:: pyb.USB_VCP()

   创建一个新USB_VCP对象。


方法
-------

.. method:: USB_VCP.setinterrupt(chr)

   设置中断运行的Python代码的字符。其默认值为3 (CTRL-C)。当在USB VCP端口上接收CTRL-C字符时，会出现键盘中断异常。 

   设置为-1以禁用此中断功能。这对您在USB VCP端口上发送原始字节大有帮助。 .

.. method:: USB_VCP.isconnected()

   若USB 连接成串行设备，则返回 ``True`` ；否则返回 ``False`` 。

.. method:: USB_VCP.any()

   若有字符在等待中，返回 ``True`` ；否则返回 ``False`` 。

.. method:: USB_VCP.close()

   该方法不执行任何动作。其意义在于使USB_VCP对象可作为文件运行。 

.. method:: USB_VCP.read([nbytes])

   从串行设备中读取尽量多的 ``nbytes`` ，并将其作为一个字节对象返回。若未指定 ``nbytes`` ，则该方法按照 ``readall()`` 运行。
   USB_VCP流在非阻塞模式下隐式地工作，因此若无可用的待处理数据，该方法将立即以 ``None`` 值返回。

.. method:: USB_VCP.readinto(buf, [maxlen])

   从串行设备汇总读取字节，并将之存入 ``buf`` ，即一个类似缓冲区的对象。读取尽量多的 ``len(buf)`` 字节。若给定 ``maxlen`` ，
   读取尽量多的 ``min(maxlen, len(buf))`` 字节。
   
   返回读取的字节数量，若无可用的待处理数据，则将之存入 ``buf`` 或 ``None`` 。

.. method:: USB_VCP.readline()

   从串行设备中读取完整的一行。

   返回一个包含数据和后面换行符的字节对象；若无可用的待处理数据，则返回 ``None`` 。

.. method:: USB_VCP.readlines()

   从串行设备中读取尽量多的数据，并将之分解成行。

   返回一个字节对象的列表，每个对象须为其中一行。每行都将包含一个换行符。

.. method:: USB_VCP.write(buf)

   将字节从 ``buf`` 读取到串行设备中。

   返回写入的字节数量。

.. method:: USB_VCP.recv(data, \*, timeout=5000)

   在总线上接收数据：
   
     - ``data`` 可为一个整数，即要接收的字节的数量，或一个将用接收到的字节填充的可变缓冲区。
     - ``timeout`` 是以毫秒计的等待接收的超时时长。
   
   返回值：若 ``data`` 是一个整数，则新的字节缓冲区接收；否则返回读取到 ``data`` 中的字节数量。

.. method:: USB_VCP.send(data, \*, timeout=5000)

   通过USB VCP发送数据：
   
     - ``data`` 是要发送的数据（一个整数或一个缓冲区对象）。
     - ``timeout`` 是以毫秒计的等待发送的超时时长。
   
   返回值：发送字节数量。
