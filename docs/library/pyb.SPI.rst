.. currentmodule:: pyb
.. _pyb.SPI:

SPI类 – 串行外设接口
============================================

SPI是由主机驱动的串行协议。其物理层包括3道线：SCK、MOSI、MISO。

.. only:: port_pyboard

    请参见I2C的用法示例；SPI十分类似。主要区别在于初始化SPI总线的参数::

        from pyb import SPI
        spi = SPI(1, SPI.MASTER, baudrate=600000, polarity=1, phase=0, crc=0x7)

    仅需参数为模式，SPI.MASTER或SPI.SLAVE。极性可为0或1，且为空闲时钟线所在的水平。
    相位可为0或1，分别在第一或第二时钟沿采样数据。无CRC时Crc可为None或一个多项式说明符。

    SPI的其他方法::

        data = spi.send_recv(b'1234')        # send 4 bytes and receive 4 bytes 发送4字节并接收4字节
        buf = bytearray(4)
        spi.send_recv(b'1234', buf)          # send 4 bytes and receive 4 into buf 发送4字节，接收4字节到缓冲区
        spi.send_recv(buf, buf)              # send/recv 4 bytes from/to buf 发送/接收4字节到/从缓冲区中

.. only:: port_openmvcam or port_moxingstm32f4

    SPI的应用模式与I2C极为相似。其主要区别在于初始化SPI总线的参数::

        from pyb import SPI
        spi = SPI(2, SPI.MASTER, baudrate=600000, polarity=1, phase=0, crc=0x7)

    只需参数为SPI.MASTER或SPI.SLAVE模式。极性可为0或1，即闲置时钟线所在水平。相位可为0或1，各在第一第二个时钟边缘采样数据。CRC可为None或一个多项式。

    SPI的附加方法::

        data = spi.send_recv(b'1234')        # send 4 bytes and receive 4 bytes
        buf = bytearray(4)
        spi.send_recv(b'1234', buf)          # send 4 bytes and receive 4 into buf
        spi.send_recv(buf, buf)              # send/recv 4 bytes from/to buf

构建函数
------------

.. only:: port_pyboard

    .. class:: pyb.SPI(bus, ...)

       在给定总线上创建一个SPI对象。可为1,2。无附加参数的情况下，SPI对象可创建但未进行初始化（若存在，其设置来自总线的最后一次初始化）。初始化参数参见 ``init`` 。

       SPI总线的物理引脚为：

         - ``SPI(1)`` is on the X position: ``(NSS, SCK, MISO, MOSI) = (X5, X6, X7, X8) = (PA4, PA5, PA6, PA7)``
         - ``SPI(2)`` is on the Y position: ``(NSS, SCK, MISO, MOSI) = (Y5, Y6, Y7, Y8) = (PB12, PB13, PB14, PB15)``

       此时，NSS引脚并未被SPI驱动占用，为可用状态。

.. only:: port_moxingstm32f4

    .. class:: pyb.SPI(bus, ...)

       在给定总线上创建一个SPI对象。可为1,2。无附加参数的情况下，SPI对象可创建但未进行初始化（若存在，其设置来自总线的最后一次初始化）。初始化参数参见 ``init`` 。

       SPI总线的物理引脚为：

         - ``SPI(1)``: ``(SCK, MISO, MOSI) = (A1, P18, P17) = (PA5, PA6, PA7)``
         - ``SPI(2)``: ``(SCK, MISO, MOSI) = (P20, P22, P21) = (PB12, PB13, PB14, PB15)``

       此时，NSS引脚并未被SPI驱动占用，为可用状态。

.. only:: port_openmvcam

    .. class:: pyb.SPI(bus, ...)

       在给定总线上创建一个SPI对象。可为2。无附加参数的情况下，SPI对象可创建但未进行初始化（若存在，其设置来自总线的最后一次初始化）。初始化参数参见 ``init`` 。

       SPI总线的物理引脚为：

         - ``SPI(2)``: ``(NSS, SCK, MISO, MOSI) = (P3, P2, P1, P0) = (PB12, PB13, PB14, PB15)``

       此时，NSS引脚并未被SPI驱动占用，为可用状态。

方法
-------

.. method:: SPI.deinit()

   关闭SPI总线。

.. only:: port_pyboard or port_moxingstm32f4

    .. method:: SPI.init(mode, baudrate=328125, \*, prescaler, polarity=1, phase=0, bits=8, firstbit=SPI.MSB, ti=False, crc=None)

       使用给定参数初始化SPI总线:

         - ``mode`` 须为 ``SPI.MASTER`` 或 ``SPI.SLAVE``.
         - ``baudrate`` 为SCK时钟频率。
         - ``prescaler`` 从APB总线频率中派生SCK所使用的的前置分频器；
           ``prescaler`` 的使用覆盖 ``baudrate``.
         - ``polarity`` 可为0或1，为空闲时钟线所在的水平。
         - ``phase`` 可为0或1，分别在第一或第二时钟沿上采样数据。
         - ``bits`` 可为8或16，且为每个传输词的位数。
         - ``firstbit`` 可为 ``SPI.MSB`` 或 ``SPI.LSB``.
         - ``crc`` 在无CRC时可为None，或一个多项式说明符。

       注意：SPI时钟线未必是符合要求的波特率。硬件仅支持APB总线频率除以预分频器的波特率（见pyb.freq()），
       可为2、4、8、16、32、64、128和256。SPI(1)在AHB2上，SPI(2)在AHB1上。若要对SPI时钟频率进行精准控制，
       则需指定 ``prescaler`` ，而非 ``baudrate`` 。

       打印SPI对象将显示计算波特率和所选预分频器。

.. only:: port_openmvcam

    .. method:: SPI.init(mode, baudrate=328125, \*, prescaler, polarity=1, phase=0, bits=8, firstbit=SPI.MSB, ti=False, crc=None)

       使用给定参数初始化SPI总线：

         - ``mode`` 必须为 ``SPI.MASTER`` 或 ``SPI.SLAVE``.
         - ``baudrate`` 是SCK的时钟频率（只对主机有效）。
         - ``prescaler`` 是用来从APB的总线频率中获取SCK的预标量； ``prescaler`` 的使用优先于 ``baudrate`` .
         - ``polarity`` 可为0或1极性可为0或1，即闲置时钟线所在水平。
         - ``phase`` 可为0或1，各在第一第二个时钟边缘采样数据。
         - ``bits`` can be 8 or 16, and is the number of bits in each transferred word.
         - ``firstbit`` 可为 ``SPI.MSB`` 或者 ``SPI.LSB``.
         - ``crc`` 可为None（无CRC时）或一个多项式。

       注意：SPI时钟频率可能并非所请求的波特率。硬件仅支持为APB总线频率（见 pyb.freq()）除以预分频数的波特率，
       其数值可为2, 4, 8, 16, 32, 64, 128, 256。SPI(2)在AHB1上。要精确控制SPI时钟频率，
       请指定 ``prescaler`` ，而非 ``baudrate`` 。

       打印SPI对象将显示计算所得波特率和所选择的预分频数。

.. only:: port_pyboard or port_openmvcam or port_moxingstm32f4

    .. method:: SPI.recv(recv, \*, timeout=5000)

       在总线上接收数据：

         - ``recv`` 可为一个整数，即接收字节的数量，或为一个使用收到的字节来填充的可变缓冲区。
         - ``timeout`` 是以毫秒计的等待接收的超时时长。

       返回值：若 ``recv`` 为一个整数，则有一个新的字节缓冲区来接收，否则这一缓冲区将传递给 ``recv`` 。

    .. method:: SPI.send(send, \*, timeout=5000)

       在总线上发送数据：

         - ``send`` 是要发送的数据（一个整数或一个缓冲区对象）。
         - ``timeout`` 是以毫秒计的等待接收的超时时长。

       返回值： ``None``.

    .. method:: SPI.send_recv(send, recv=None, \*, timeout=5000)

       同时在总线上发送和接收数据：

         - ``send`` 是要发送的数据（一个整数或一个缓冲区对象）。
         - ``recv`` 是一个使用收到的字节来填充的可变缓冲区。可与 ``send`` 相同或省略。若省略，则创建一个新的缓冲区。
         - ``timeout`` 是以毫秒计的等待接收的超时时长。

       返回值：内含接收字节的缓冲区。

常量
---------

.. only:: port_pyboard or port_openmvcam or port_moxingstm32f4

    .. data:: SPI.MASTER
    .. data:: SPI.SLAVE

       将SPI总线初始化为主模式或从模式。

    .. data:: SPI.LSB
    .. data:: SPI.MSB

       将首位设置为最小或最重要的位。
