.. currentmodule:: pyb
.. _pyb.DAC:

DAC类 – 数字模拟变换
=========================================

.. only:: port_pyboard

    DAC用于在引脚X5或X6上输出模拟值（一个特定电压）。电压介于0至3.3V之间。

    *此模块将会对API进行更改。*

    用法示例::

        from pyb import DAC

        dac = DAC(1)            # create DAC 1 on pin X5 在引脚X5上创建DAC1
        dac.write(128)          # write a value to the DAC (makes X5 1.65V) 将一个值写入DAC（使X5为1.65V）

        dac = DAC(1, bits=12)   # use 12 bit resolution 使用12位分辨率
        dac.write(4095)         # output maximum value, 3.3V 输出最大值，3.3V

    输出连续正弦波::

        import math
        from pyb import DAC

        # create a buffer containing a sine-wave 创建一个包含正弦波的缓冲区
        buf = bytearray(100)
        for i in range(len(buf)):
            buf[i] = 128 + int(127 * math.sin(2 * math.pi * i / len(buf)))

        # output the sine-wave at 400Hz 以400Hz输出正弦波
        dac = DAC(1)
        dac.write_timed(buf, 400 * len(buf), mode=DAC.CIRCULAR)

    以12位分辨率输出连续正弦波::

        import math
        from array import array
        from pyb import DAC

        # create a buffer containing a sine-wave, using half-word samples 使用半字样本创建一个包含正弦波的缓冲区
        buf = array('H', 2048 + int(2047 * math.sin(2 * math.pi * i / 128)) for i in range(128))

        # output the sine-wave at 400Hz 以400Hz输出正弦波
        dac = DAC(1, bits=12)
        dac.write_timed(buf, 400 * len(buf), mode=DAC.CIRCULAR)

.. only:: port_moxingstm32f4

    DAC用于在引脚A1或者A2上输出模拟值(一种特定的电压)。电压数值介于0-3.3v。

    *该模块将对API进行更改。*

    例::

        from pyb import DAC

        dac = DAC(pyb.Pin("A1"))             # create DAC on pin A1 在引脚A1上创建DAC
        dac.write(128)                       # write a value to the DAC (makes A1 1.65V) 为DAC写入一个值（使A1为1.65V）

        dac = DAC(pyb.Pin("A1"), bits=12)    # use 12 bit resolution 使用12位分辨率
        dac.write(4095)                      # output maximum value, 3.3V 输出最大值，3.3V

    输出连续的正弦波::

        import math
        from pyb import DAC

        # create a buffer containing a sine-wave 创建一个包含正弦波的缓冲区
        buf = bytearray(100)
        for i in range(len(buf)):
            buf[i] = 128 + int(127 * math.sin(2 * math.pi * i / len(buf)))

        # output the sine-wave at 400Hz 输出400Hz的正弦波
        dac = DAC(pyb.Pin("A1"))
        dac.write_timed(buf, 400 * len(buf), mode=DAC.CIRCULAR)

    输出12位分辨率的正弦波::

        import math
        from array import array
        from pyb import DAC

        # create a buffer containing a sine-wave, using half-word samples 使用半字样本创建一个包含一个正弦波的缓冲区
        buf = array('H', 2048 + int(2047 * math.sin(2 * math.pi * i / 128)) for i in range(128))

        # output the sine-wave at 400Hz 输出400Hz的正弦波
        dac = DAC(pyb.Pin("A1"), bits=12)
        dac.write_timed(buf, 400 * len(buf), mode=DAC.CIRCULAR)

.. only:: port_openmvcam

    DAC用于在引脚P6上输出模拟值(一种特定的电压)。电压数值介于0-3.3v。

    *该模块将对API进行更改。*

    例::

        from pyb import DAC

        dac = DAC(pyb.Pin("P6"))             # create DAC on pin P6 在引脚P6上创建DAC
        dac.write(128)                       # write a value to the DAC (makes P6 1.65V) 为DAC写入一个值（使P6为1.65V）

        dac = DAC(pyb.Pin("P6"), bits=12)    # use 12 bit resolution 使用12位分辨率
        dac.write(4095)                      # output maximum value, 3.3V 输出最大值，3.3V

    输出连续的正弦波::

        import math
        from pyb import DAC

        # create a buffer containing a sine-wave 创建一个包含正弦波的缓冲区
        buf = bytearray(100)
        for i in range(len(buf)):
            buf[i] = 128 + int(127 * math.sin(2 * math.pi * i / len(buf)))

        # output the sine-wave at 400Hz 输出400Hz的正弦波
        dac = DAC(pyb.Pin("P6"))
        dac.write_timed(buf, 400 * len(buf), mode=DAC.CIRCULAR)

    输出12位分辨率的正弦波::

        import math
        from array import array
        from pyb import DAC

        # create a buffer containing a sine-wave, using half-word samples 使用半字样本创建一个包含一个正弦波的缓冲区
        buf = array('H', 2048 + int(2047 * math.sin(2 * math.pi * i / 128)) for i in range(128))

        # output the sine-wave at 400Hz 输出400Hz的正弦波
        dac = DAC(pyb.Pin("P6"), bits=12)
        dac.write_timed(buf, 400 * len(buf), mode=DAC.CIRCULAR)

构造函数
------------

.. only:: port_pyboard

    .. class:: pyb.DAC(port, bits=8)

       构造一个新的DAC对象。

       ``port`` 可为一个引脚对象，或一个整数（1或2）。DAC(1)位于引脚X5上，DAC(2)位于引脚X6。

       ``bits`` 为一个指定分辨率的整数，可为8或12。write和write_timed函数的最大值为
       2\*\*``bits``-1.

.. only:: port_moxingstm32f4

    .. class:: pyb.DAC(port, bits=8)

       构建一个新的DAC对象：

       ``port`` 可为一个引脚对象。DAC(‘A1’)在引脚A1(PA5)上,DAC(‘A2’)在引脚A2(PA4)上。

       ``bits`` 是一个指定分辨率的整数，可为8或12。写入和写入定时方法的最大值是2** ``bits`` -1。

.. only:: port_openmvcam

    .. class:: pyb.DAC(port, bits=8)

       构建一个新的DAC对象：

       ``port`` 可为一个引脚对象。DAC(‘P6’)在引脚P6(PA5)上。

       ``bits`` 是一个指定分辨率的整数，可为8或12。写入和写入定时方法的最大值是2** ``bits`` -1。

方法
-------

.. method:: DAC.init(bits=8)

   重新启动DAC。 ``bits`` 可为8或12。

.. method:: DAC.noise(freq)

   生成一个伪随机噪声信号。以给定频率将一个新的随机样本写入DAC输出。

.. method:: DAC.triangle(freq)

   生成一个三角形波。DAC输出值以给定频率变化。重复三角形波的频率为2048倍。

.. method:: DAC.write(value)

   直接访问DAC输出。最小值为0。最大值为2** ``bits`` -1，当创建DAC对象或使用 ``init`` 方法时在此设定bits。

.. only:: port_pyboard

    .. method:: DAC.write_timed(data, freq, \*, mode=DAC.NORMAL)

       使用DMA传输启动RAM到DAC的突发。输入数据以8位模式的字节数组和12位模式下的无符号半字（类型编码“H”）数组形式处理。

       ``freq`` 可为指定使用定时器(6)写入DAC样本的频率的整数。其也可为一个用来触发DAC样本的已初始化定时器对象。有效定时器为2、4、5、6、7和8。

       ``mode`` 可为 ``DAC.NORMAL`` 或 ``DAC.CIRCULAR``.

       同时使用DAC的示例::

         dac1 = DAC(1)
         dac2 = DAC(2)
         dac1.write_timed(buf1, pyb.Timer(6, freq=100), mode=DAC.CIRCULAR)
         dac2.write_timed(buf2, pyb.Timer(7, freq=200), mode=DAC.CIRCULAR)

.. only:: port_moxingstm32f4

    .. method:: DAC.write_timed(data, freq, \*, mode=DAC.NORMAL)

       使用DMA传输启动RAM到DAC的突发。输入数据以8位模式的字节数组和12位模式下的无符号半字（类型编码“H”）数组形式处理。

       ``freq`` 可为指定使用定时器(6)写入DAC样本的频率的整数。其也可为一个用来触发DAC样本的已初始化定时器对象。有效定时器为2、4、5、6、7和8。

       ``mode`` 可为 ``DAC.NORMAL`` 或 ``DAC.CIRCULAR``.

       同时使用DAC的示例::

         dac1 = DAC(pyb.Pin("A1"))
         dac2 = DAC(pyb.Pin("A2"))
         dac1.write_timed(buf1, pyb.Timer(6, freq=100), mode=DAC.CIRCULAR)
         dac2.write_timed(buf2, pyb.Timer(7, freq=200), mode=DAC.CIRCULAR)

.. only:: port_openmvcam

    .. method:: DAC.write_timed(data, freq, \*, mode=DAC.NORMAL)

       使用DMA传输启动一个RAM对DAC的突发。输入数据在8位模式下被当作一个字节数组，而在12位模式下为一组无符号半字(数组类型代码为H)。

       ``freq`` 为一个指定使用定时器(6)写入DAC样本的频率的整数。或为一个用来触发DAC样本的已初始化的定时器对象。有效定时器为2、4、5、6、7、8。

       ``mode`` 可为 ``DAC.NORMAL`` 或 ``DAC.CIRCULAR`` 。


       例::

         dac = pyb.DAC(pyb.Pin("P6"))
         dac.write_timed(buf1, pyb.Timer(4, freq=100), mode=DAC.CIRCULAR)
