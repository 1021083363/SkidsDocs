.. currentmodule:: pyb
.. _pyb.ADC:

ADC类 – 模数转换
=========================================

.. only:: port_pyboard or port_openmvcam or port_moxingstm32f4

    使用::

        import pyb

        adc = pyb.ADC(pin)              # create an analog object from a pin 从引脚中创建一个模拟对象
        val = adc.read()                # read an analog value 读取一个模拟对象

        adc = pyb.ADCAll(resolution)    # create an ADCAll object 创建一个ADCALL对象
        val = adc.read_channel(channel) # read the given channel 读取给定通道
        val = adc.read_core_temp()      # read MCU temperature 读取MCU温度
        val = adc.read_core_vbat()      # read MCU VBAT 读取MCU VBAT
        val = adc.read_core_vref()      # read MCU VREF 读取MCU CREF

构造函数
------------


.. only:: port_pyboard or port_openmvcam or port_moxingstm32f4

    .. class:: pyb.ADC(pin)

       创建一个与给定引脚相关联的ADC对象，然后您就可在引脚中读取模拟值。

方法
-------

.. only:: port_pyboard

    .. method:: ADC.read()

       在模拟引脚上读取值并返回。返回值应介于0至4095之间。

    .. method:: ADC.read_timed(buf, timer)

       以 ``timer`` 对象设定的比率将模拟值读取到 ``buf`` 中。

       ``buf`` 可为字节数组或数组。ADC值有12位分辨率，其中元素大小大于等于16位时，直接储存到 ``buf`` 中。
       若 ``buf`` 仅有8位元素（例如一个字节元组），则样本分辨率将会降低到8位。

       ``timer`` 应为一个定时器对象，定时器每次触发时，样本都会被读取。定时器须已初始化，且以预设的样本频率运行。

       为支持此函数之前的活动， ``timer`` 可为一个指定样本频率（以Hz为单位）的整数。在此种情况下，定时器（6）可自动配置为以给定频率运行。

       定时器对象用法示例（最佳方法）::

           adc = pyb.ADC(pyb.Pin.board.X19)    # create an ADC on pin X19 在X19引脚上创建一个ADC
           tim = pyb.Timer(6, freq=10)         # create a timer running at 10Hz 创建一个以10Hz运行的定时器
           buf = bytearray(100)                # creat a buffer to store the samples 创建一个储存样本的缓冲区
           adc.read_timed(buf, tim)            # sample 100 values, taking 10s 样本100个值，耗时10s

       Example using an integer for the frequency::

           adc = pyb.ADC(pyb.Pin.board.X19)    # create an ADC on pin X19 在引脚X19上创建一个ADC
           buf = bytearray(100)                # create a buffer of 100 bytes 创建100字节的缓冲区
           adc.read_timed(buf, 10)             # read analog values into buf at 10Hz 以10Hz将模拟值读取到缓冲区中
                                               #   this will take 10 seconds to finish 耗时10s后结束
           for val in buf:                     # loop over all values 循环所有值
               print(val)                      # print the value out 打印值

       此函数不分配任何内存。

.. only:: port_openmvcam

    .. method:: ADC.read()

        读取模拟引脚的值并返回该值。该返回值应介于0-4095。

    .. method:: ADC.read_timed(buf, timer)

        将模拟值以 ``time`` 对象设置的速率读取到 ``buf`` 中。

        ``buf`` 可为字节组格式。 ADC值有12位分辨率，
        若其中项大于或等于16位，则直接存入 ``buf`` 。
        若 ``buf`` 只有8位的项，样本分辨率会降低到8位。

        ``timer`` 应为一个定时器对象，每次定时器触发都会读取一个样本。定时器必须被初始化，并以期望的采样频率运行。

        为支持函数的前一步， ``timer`` 可为一个指定采样频率（单位：Hz）的整数。在这种情况下，定时器将被自动配置为在给定的频率下运行。.

        例：使用一个定时器对象（最佳方法)::

            adc = pyb.ADC('P6')                 # create an ADC on pin P6在P6引脚上创建一个ADC
            tim = pyb.Timer(6, freq=10)         # create a timer running at 10Hz创建一个以10Hz运行的定时器
            buf = bytearray(100)                # create a buffer to store the samples创建一个用以储存样本的缓冲区
            adc.read_timed(buf, tim)            # sample 100 values, taking 10s样本100，取10s

        例：使用整数频率::

            adc = pyb.ADC('P6')                 # create an ADC on pin P6在P6引脚上创建一个ADC
            buf = bytearray(100)                # create a buffer of 100 bytes创建也该100字节的缓冲区
            adc.read_timed(buf, 10)             # read analog values into buf at 10Hz将模拟值读取到10Hz的缓冲区中
                                    #   this will take 10 seconds to finish此步骤耗时10秒
            for val in buf:                     # loop over all values循环所有值
                print(val)                      # print the value out打印值

        这一函数不分配任何内存。

.. only:: port_moxingstm32f4

    .. method:: ADC.read()

        读取模拟引脚的值并返回该值。该返回值应介于0-4095。

    .. method:: ADC.read_timed(buf, timer)

        将模拟值以 ``time`` 对象设置的速率读取到 ``buf`` 中。

        ``buf`` 可为字节组格式。 ADC值有12位分辨率，
        若其中项大于或等于16位，则直接存入 ``buf`` 。
        若 ``buf`` 只有8位的项，样本分辨率会降低到8位。

        ``timer`` 应为一个定时器对象，每次定时器触发都会读取一个样本。定时器必须被初始化，并以期望的采样频率运行。

        为支持函数的前一步， ``timer`` 可为一个指定采样频率（单位：Hz）的整数。在这种情况下，定时器将被自动配置为在给定的频率下运行。.

        例：使用一个定时器对象（最佳方法)::

            adc = pyb.ADC('A1')                 # create an ADC on pin A1在A1引脚上创建一个ADC
            tim = pyb.Timer(5, freq=10)         # create a timer running at 10Hz创建一个以10Hz运行的定时器
            buf = bytearray(100)                # create a buffer to store the samples创建一个用以储存样本的缓冲区
            adc.read_timed(buf, tim)            # sample 100 values, taking 10s样本100，取10s

        例：使用整数频率::

            adc = pyb.ADC('A1')                 # create an ADC on pin A1在A1引脚上创建一个ADC
            buf = bytearray(100)                # create a buffer of 100 bytes创建也该100字节的缓冲区
            adc.read_timed(buf, 10)             # read analog values into buf at 10Hz将模拟值读取到10Hz的缓冲区中
                                    #   this will take 10 seconds to finish此步骤耗时10秒
            for val in buf:                     # loop over all values循环所有值
                print(val)                      # print the value out打印值

        这一函数不分配任何内存。
ADCAll对象
-----------------

.. only:: port_pyboard or port_openmvcam or port_moxingstm32f4

    这一实例将所有ADC引脚转换为模拟输入。原始的MCU温度、VREF和VBAT数据可分别在ADC通道16、17和18中访问。需要适度的缩放。芯片上的温度传感器绝对精度不高，只适用于检测温度变化。

    ``ADCAll``  ``read_core_vbat()`` 和 ``read_core_vref()`` 使用3.3 V电源作为参考，读取备用电池电压和(1.21 V)参考电压。
    假定 ``ADCAll`` 对象已通过 ``adc = pyb.ADCAll(12)`` 实例化，则3.3伏电源电压可计算为：

    ``v33 = 3.3 * 1.21 / adc.read_core_vref()``

    若3.3V电压正确，则 ``adc.read_core_vbat()`` 值有效。若电源电压降至3.3V以下，例如在在电池供电的放电电池系统中，该稳压器将无法保持3.3V电源，导致错误读取。此时若要产生一个有效值，请遵循以下步骤：

    ``vback = adc.read_core_vbat() * 1.21 / adc.read_core_vref()``

    在不产生 ``ADCAll`` 副作用的前提下访问该值并非不可能::

        def adcread(chan):                              # 16 temp 17 vbat 18 vref 温度16电池电压17参考电压18
            assert chan >= 16 and chan <= 18, 'Invalid ADC channel'
            start = pyb.millis()
            timeout = 100
            stm.mem32[stm.RCC + stm.RCC_APB2ENR] |= 0x100 # enable ADC1 clock.0x4100
            stm.mem32[stm.ADC1 + stm.ADC_CR2] = 1       # Turn on ADC 启动ADC
            stm.mem32[stm.ADC1 + stm.ADC_CR1] = 0       # 12 bit
            if chan == 17:
                stm.mem32[stm.ADC1 + stm.ADC_SMPR1] = 0x200000 # 15 cycles 15周期
                stm.mem32[stm.ADC + 4] = 1 << 23
            elif chan == 18:
                stm.mem32[stm.ADC1 + stm.ADC_SMPR1] = 0x1000000
                stm.mem32[stm.ADC + 4] = 0xc00000
            else:
                stm.mem32[stm.ADC1 + stm.ADC_SMPR1] = 0x40000
                stm.mem32[stm.ADC + 4] = 1 << 23
            stm.mem32[stm.ADC1 + stm.ADC_SQR3] = chan
            stm.mem32[stm.ADC1 + stm.ADC_CR2] = 1 | (1 << 30) | (1 << 10) # start conversion开始转换
            while not stm.mem32[stm.ADC1 + stm.ADC_SR] & 2: # wait for EOC
                if pyb.elapsed_millis(start) > timeout:
                    raise OSError('ADC timout')
            data = stm.mem32[stm.ADC1 + stm.ADC_DR]     # clear down EOC 清空EOC
            stm.mem32[stm.ADC1 + stm.ADC_CR2] = 0       # Turn off ADC 关闭ADC
            return data

        def v33():
            return 4096 * 1.21 / adcread(17)

        def vbat():
            return  1.21 * 2 * adcread(18) / adcread(17)  # 2:1 divider on Vbat channel Vbat通道的2:1分频器

        def vref():
            return 3.3 * adcread(17) / 4096

        def temperature():
            return 25 + 400 * (3.3 * adcread(16) / 4096 - 0.76)
