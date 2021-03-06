****************************************
:mod:`network` --- 网络配置
****************************************

.. module:: network
   :synopsis: 网络配置

该模块提供网络驱动程序和路由配置。特定硬件的网络驱动程序可以在此模块中使用，并用于配置硬件网络接口。然后可通过socket模块使用配置的接口。要使用该模块，必须安装固件的网络构建。

例如::

    # connect/ show IP config a specific network interface 配置一个特定的网络接口
    # see below for examples of specific drivers 特定驱动示例见下
    import network
    import utime
    nic = network.Driver(...)
    if not nic.isconnected():
        nic.connect()
        print("Waiting for connection...")
        while not nic.isconnected():
            utime.sleep(1)
    print(nic.ifconfig())

    # now use usocket as usual 现在正常使用socket
    import usocket as socket
    addr = socket.getaddrinfo('micropython.org', 80)[0][-1]
    s = socket.socket()
    s.connect(addr)
    s.send(b'GET / HTTP/1.1\r\nHost: micropython.org\r\n\r\n')
    data = s.recv(1000)
    s.close()

常见的网络适配器接口
================================

本节描述针对不同硬件的MicroPython不同端口实现的所有网络接口类的一个(隐含的)抽象基类。
这也就意味着MicroPython实际上不会提供 `AbstractNIC` 类，但是正如以下部分中所述，任何实际的NIC类实现此处所述的方法。

.. class:: AbstractNIC(id=None, ...)

将一个网络接口对象实例化。参数取决于网络接口。若同种类接口超过一个，则首个参数应为 `id` 。

    .. method:: active([is_active])

        若布尔值参数被传递，则激活（“up”）或禁用（“down”）网络接口。否则若未提供参数，则查询当前状态。
        大多数其他方法需要一个激活接口（在未激活接口上的回调行为尚未定义）。

    .. method:: connect([service_id, key=None, \*, ...])

       将接口连接到网络。该方法为可选的，且仅适用于不为“总是连接”的接口。若未给定任何参数，
       则连接到默认设备（或唯一设备）。若给定一个参数，即为连接的设备的主要标识符。
       可能附带一个访问该服务的密钥（密码）。根据网络介质类型和/或特定设备，可有更多任意关键字参数。
       参数可用于：a）指定可选的服务标识符种类；b）提供额外的连接参数。对于不同媒介种类，有不同的预定义/推荐参数集，其中：

       * WiFi: 由BSSID（MAC地址）连接的bssid密钥，而非访问点名称

    .. method:: disconnect()

       断开网络连接。

    .. method:: isconnected()

       若连接到网络，则返回 ``True`` ，否则返回 ``False`` 。

    .. method:: scan(\*, ...)

       扫描可用的网络设备/连接。返回一个发现的设备参数的元组列表。对于不同的网络媒介，有不同的预定义/推荐元组格式，其中：

       * WiFi: (ssid, bssid, channel, RSSI, authmode, hidden). 可能有特定于某一设备的进一步的域。

       该函数可能接受附加关键字参数以过滤扫描结果（例如：在特定通道上扫描某一种特定服务等），
       并影响扫描持续时间和其他参数。在可能之处，参数名称应与connect()中的相匹配。

    .. method:: status()

       返回接口的具体状态，值取决于网络介质/技术。

    .. method:: ifconfig([(ip, subnet, gateway, dns)])

       获取/设置IP-层的网络接口参数：IP地址、子网掩码、网关。DNS服务器。当无参数调用时，
       该方法返回一个包含上述信息的4元组。设置上述值，请使用所需信息传递一个4元组。例如::

        nic.ifconfig(('192.168.0.4', '255.255.255.0', '192.168.0.1', '8.8.8.8'))

    .. method:: config('param')
                config(param=value, ...)

       获取或设置网络接口参数。这些方法允许处理标准IP配置（由 `ifconfig()` 处理）外的额外参数。
       这些参数包括特定于网络和特定于硬件的参数以及状态值。设置参数，应使用关键字参数语法，
       且多个参数应一次设置。查询时参数名称应作为一个字符串查询，且一次只能查询一个参数::

        # Set WiFi access point name (formally known as ESSID) and WiFi channel 设置WiFi访问点名称（常称为ESSID）和WiFi通道
        ap.config(essid='My AP', channel=11)
        # Query params one by one 一个一个查询参数
        print(ap.config('essid'))
        print(ap.config('channel'))
        # Extended status information also available this way 扩展状态信息也用此方式
        print(sta.config('rssi'))

.. only:: port_pyboard

    CC3K 类
    ==========

    此类为CC3000 WiFi模块提供驱动程序。 用法示例::

        import network
        nic = network.CC3K(pyb.SPI(2), pyb.Pin.board.Y5, pyb.Pin.board.Y4, pyb.Pin.board.Y3)
        nic.connect('your-ssid', 'your-password')
        while not nic.isconnected():
            pyb.delay(50)
        print(nic.ifconfig())

        # now use socket as usual 现在像往常一样使用socket
        ...

    为使此例运行，CC3000模块需包含以下连接:

        - MOSI 连接到 Y8
        - MISO 连接到 Y7
        - CLK 连接到 Y6
        - CS 连接到 Y5
        - VBEN 连接到 Y4
        - IRQ 连接到 Y3

    使用其他SPI总线并将其他引脚用于CS、VBEN和IRQ都是可行的。

    构造函数
    ------------

    .. class:: CC3K(spi, pin_cs, pin_en, pin_irq)

       创建一个CC3K驱动对象，使用给定SPI总线和引脚初始化CC3000模块，并返回CC3K对象。

       参数为:

         - *spi* is an :ref:`SPI object <pyb.SPI>` 是一个SPI对象，即CC3000连接到的SPI总线（MOSI、MISO和CLK引脚）。
         - *pin_cs* is a :ref:`Pin object <pyb.Pin>` 是一个引脚对象，即连接到CC3000 CS引脚的引脚。
         - *pin_en* is a :ref:`Pin object <pyb.Pin>` 是一个引脚对象，即连接到CC3000 VBEN引脚的引脚。
         - *pin_irq* is a :ref:`Pin object <pyb.Pin>` 是一个引脚对象，即连接到CC3000 IRQ引脚的引脚。

       所有对象将由驱动初始化，因此无需您手动进行。例如，您可使用::

         nic = network.CC3K(pyb.SPI(2), pyb.Pin.board.Y5, pyb.Pin.board.Y4, pyb.Pin.board.Y3)

    方法
    -------

    .. method:: cc3k.connect(ssid, key=None, \*, security=WPA2, bssid=None)

       使用给定SSID和其他安全参数连接到WiFi访问点。

    .. method:: cc3k.disconnect()

       断开与WiFi访问点的连接。

    .. method:: cc3k.isconnected()

       若连接到访问点且有一个有效IP地址，则返回True，否则则返回False。

    .. method:: cc3k.ifconfig()

       使用（ip、子网掩码、网关、DNS服务器、DHCP服务器、MAC地址、SSID）返回一个7元组。

    .. method:: cc3k.patch_version()

       返回CC3000上的补丁程序（固件）的版本。

    .. method:: cc3k.patch_program('pgm')

       将当前固件上传到CC3000。为使上传正常进行，您必须将“pgm”作为首个参数传递。

    常量
    ---------

    .. data:: CC3K.WEP
    .. data:: CC3K.WPA
    .. data:: CC3K.WPA2

       使用的安全类型

    WIZNET5K 类
    ==============

    该类允许您控制基于W5200和W5500芯片组的WIZnet5x00以太网适配器（仅W5200经过测试）。

    用法示例::

        import network
        nic = network.WIZNET5K(pyb.SPI(1), pyb.Pin.board.X5, pyb.Pin.board.X4)
        print(nic.ifconfig())

        # now use socket as usual 现在像往常一样使用socket
        ...

    为使此例运行，WIZnet5x00模块需具有以下连接:

        - MOSI 连接到 X8
        - MISO 连接到 X7
        - SCLK 连接到 X6
        - nSS 连接到 X5
        - nRESET 连接到 X4

    使用其他SPI总线并将其他引脚用于nSS和nRESET都是可行的.

    构造函数
    ------------

    .. class:: WIZNET5K(spi, pin_cs, pin_rst)

       创建一个WIZNET5K驱动对象，使用给定SPI总线和引脚初始化WIZnet5x00模块，并返回WIZNET5K对象。

       参数为:

         - *spi* is an :ref:`SPI object <pyb.SPI>` 是一个SPI对象，即WIZnet5x00连接到的SPI总线（MOSI、MISO和CLK引脚）。
         - *pin_cs* is a :ref:`Pin object <pyb.Pin>` 是一个引脚对象，即连接到WIZnet5x00 nSS引脚的引脚。
         - *pin_rst* is a :ref:`Pin object <pyb.Pin>` 是一个引脚对象，即连接到WIZnet5x00 nRESET引脚的引脚。

       所有引脚都由驱动初始化，因此无需您手动进行。例如，您可使用::

         nic = network.WIZNET5K(pyb.SPI(1), pyb.Pin.board.X5, pyb.Pin.board.X4)

    方法
    -------

    .. method:: wiznet5k.isconnected()

       如果物理以太网链路连接起来，则返回 ``True`` 。
       否则返回 ``False`` 。


    .. method:: wiznet5k.ifconfig([(ip, subnet, gateway, dns)])

       获取/设置IP地址、子网掩码、网关和DNS。

       当无参数调用时，此类函数返回一个带有上述信息的4元组。

       设置上述值，请使用所需信息传递一个4元组。例如::

        nic.ifconfig(('192.168.0.4', '255.255.255.0', '192.168.0.1', '8.8.8.8'))

    .. method:: wiznet5k.regs()

       转储WIZnet5x00寄存器。用于调试。

.. only:: port_esp8266

    函数
    =========

    .. function:: phy_mode([mode])

        获取或设置PHY模式。

        如果提供了 *mode* 参数，则将模式设置为其值。 如果在没有参数的情况下调用该函数，则返回当前模式。

        可取的模式定义为常量：
            * ``MODE_11B`` -- IEEE 802.11b,
            * ``MODE_11G`` -- IEEE 802.11g,
            * ``MODE_11N`` -- IEEE 802.11n.

    WLAN 类
    ==========

    This class provides a driver for WiFi network processor in the ESP8266.  Example usage::

        import network
        # enable station interface and connect to WiFi access point
        nic = network.WLAN(network.STA_IF)
        nic.active(True)
        nic.connect('your-ssid', 'your-password')
        # now use sockets as usual

    Constructors
    ------------
    .. class:: WLAN(interface_id)

    Create a WLAN network interface object. Supported interfaces are
    ``network.STA_IF`` (station aka client, connects to upstream WiFi access
    points) and ``network.AP_IF`` (access point, allows other WiFi clients to
    connect). Availability of the methods below depends on interface type.
    For example, only STA interface may `connect()` to an access point.

    Methods
    -------

    .. method:: wlan.active([is_active])

        Activate ("up") or deactivate ("down") network interface, if boolean
        argument is passed. Otherwise, query current state if no argument is
        provided. Most other methods require active interface.

    .. method:: wlan.connect(ssid, password)

        Connect to the specified wireless network, using the specified password.

    .. method:: wlan.disconnect()

        Disconnect from the currently connected wireless network.

    .. method:: wlan.scan()

        Scan for the available wireless networks.

        Scanning is only possible on STA interface. Returns list of tuples with
        the information about WiFi access points:

            (ssid, bssid, channel, RSSI, authmode, hidden)

        *bssid* is hardware address of an access point, in binary form, returned as
        bytes object. You can use `ubinascii.hexlify()` to convert it to ASCII form.

        There are five values for authmode:

            * 0 -- open
            * 1 -- WEP
            * 2 -- WPA-PSK
            * 3 -- WPA2-PSK
            * 4 -- WPA/WPA2-PSK

        and two for hidden:

            * 0 -- visible
            * 1 -- hidden

    .. method:: wlan.status()

        Return the current status of the wireless connection.

        The possible statuses are defined as constants:

            * ``STAT_IDLE`` -- no connection and no activity,
            * ``STAT_CONNECTING`` -- connecting in progress,
            * ``STAT_WRONG_PASSWORD`` -- failed due to incorrect password,
            * ``STAT_NO_AP_FOUND`` -- failed because no access point replied,
            * ``STAT_CONNECT_FAIL`` -- failed due to other problems,
            * ``STAT_GOT_IP`` -- connection successful.

    .. method:: wlan.isconnected()

        In case of STA mode, returns ``True`` if connected to a WiFi access
        point and has a valid IP address.  In AP mode returns ``True`` when a
        station is connected. Returns ``False`` otherwise.

    .. method:: wlan.ifconfig([(ip, subnet, gateway, dns)])

       Get/set IP-level network interface parameters: IP address, subnet mask,
       gateway and DNS server. When called with no arguments, this method returns
       a 4-tuple with the above information. To set the above values, pass a
       4-tuple with the required information.  For example::

        nic.ifconfig(('192.168.0.4', '255.255.255.0', '192.168.0.1', '8.8.8.8'))

    .. method:: wlan.config('param')
    .. method:: wlan.config(param=value, ...)

       Get or set general network interface parameters. These methods allow to work
       with additional parameters beyond standard IP configuration (as dealt with by
       `wlan.ifconfig()`). These include network-specific and hardware-specific
       parameters. For setting parameters, keyword argument syntax should be used,
       multiple parameters can be set at once. For querying, parameters name should
       be quoted as a string, and only one parameter can be queries at time::

        # Set WiFi access point name (formally known as ESSID) and WiFi channel
        ap.config(essid='My AP', channel=11)
        # Query params one by one
        print(ap.config('essid'))
        print(ap.config('channel'))

       Following are commonly supported parameters (availability of a specific parameter
       depends on network technology type, driver, and MicroPython port).

       =========  ===========
       Parameter  Description
       =========  ===========
       mac        MAC address (bytes)
       essid      WiFi access point name (string)
       channel    WiFi channel (integer)
       hidden     Whether ESSID is hidden (boolean)
       authmode   Authentication mode supported (enumeration, see module constants)
       password   Access password (string)
       =========  ===========

.. only:: port_wipy

    class WLAN
    ==========

    This class provides a driver for the WiFi network processor in the WiPy. Example usage::

        import network
        import time
        # setup as a station
        wlan = network.WLAN(mode=WLAN.STA)
        wlan.connect('your-ssid', auth=(WLAN.WPA2, 'your-key'))
        while not wlan.isconnected():
            time.sleep_ms(50)
        print(wlan.ifconfig())

        # now use socket as usual
        ...

    Constructors
    ------------

    .. class:: WLAN(id=0, ...)

       Create a WLAN object, and optionally configure it. See `init()` for params of configuration.

    .. note::

       The ``WLAN`` constructor is special in the sense that if no arguments besides the id are given,
       it will return the already existing ``WLAN`` instance without re-configuring it. This is
       because ``WLAN`` is a system feature of the WiPy. If the already existing instance is not
       initialized it will do the same as the other constructors an will initialize it with default
       values.

    Methods
    -------

    .. method:: wlan.init(mode, \*, ssid, auth, channel, antenna)

       Set or get the WiFi network processor configuration.

       Arguments are:

         - *mode* can be either ``WLAN.STA`` or ``WLAN.AP``.
         - *ssid* is a string with the ssid name. Only needed when mode is ``WLAN.AP``.
         - *auth* is a tuple with (sec, key). Security can be ``None``, ``WLAN.WEP``,
           ``WLAN.WPA`` or ``WLAN.WPA2``. The key is a string with the network password.
           If ``sec`` is ``WLAN.WEP`` the key must be a string representing hexadecimal
           values (e.g. 'ABC1DE45BF'). Only needed when mode is ``WLAN.AP``.
         - *channel* a number in the range 1-11. Only needed when mode is ``WLAN.AP``.
         - *antenna* selects between the internal and the external antenna. Can be either
           ``WLAN.INT_ANT`` or ``WLAN.EXT_ANT``.

       For example, you can do::

          # create and configure as an access point
          wlan.init(mode=WLAN.AP, ssid='wipy-wlan', auth=(WLAN.WPA2,'www.wipy.io'), channel=7, antenna=WLAN.INT_ANT)

       or::

          # configure as an station
          wlan.init(mode=WLAN.STA)

    .. method:: wlan.connect(ssid, \*, auth=None, bssid=None, timeout=None)

       Connect to a WiFi access point using the given SSID, and other security
       parameters.

          - *auth* is a tuple with (sec, key). Security can be ``None``, ``WLAN.WEP``,
            ``WLAN.WPA`` or ``WLAN.WPA2``. The key is a string with the network password.
            If ``sec`` is ``WLAN.WEP`` the key must be a string representing hexadecimal
            values (e.g. 'ABC1DE45BF').
          - *bssid* is the MAC address of the AP to connect to. Useful when there are several
            APs with the same ssid.
          - *timeout* is the maximum time in milliseconds to wait for the connection to succeed.

    .. method:: wlan.scan()

       Performs a network scan and returns a list of named tuples with (ssid, bssid, sec, channel, rssi).
       Note that channel is always ``None`` since this info is not provided by the WiPy.

    .. method:: wlan.disconnect()

       Disconnect from the WiFi access point.

    .. method:: wlan.isconnected()

       In case of STA mode, returns ``True`` if connected to a WiFi access point and has a valid IP address.
       In AP mode returns ``True`` when a station is connected, ``False`` otherwise.

    .. method:: wlan.ifconfig(if_id=0, config=['dhcp' or configtuple])

       With no parameters given returns a 4-tuple of *(ip, subnet_mask, gateway, DNS_server)*.

       if ``'dhcp'`` is passed as a parameter then the DHCP client is enabled and the IP params
       are negotiated with the AP.

       If the 4-tuple config is given then a static IP is configured. For instance::

          wlan.ifconfig(config=('192.168.0.4', '255.255.255.0', '192.168.0.1', '8.8.8.8'))

    .. method:: wlan.mode([mode])

       Get or set the WLAN mode.

    .. method:: wlan.ssid([ssid])

       Get or set the SSID when in AP mode.

    .. method:: wlan.auth([auth])

       Get or set the authentication type when in AP mode.

    .. method:: wlan.channel([channel])

       Get or set the channel (only applicable in AP mode).

    .. method:: wlan.antenna([antenna])

       Get or set the antenna type (external or internal).

    .. method:: wlan.mac([mac_addr])

       Get or set a 6-byte long bytes object with the MAC address.

    .. method:: wlan.irq(\*, handler, wake)

        Create a callback to be triggered when a WLAN event occurs during ``machine.SLEEP``
        mode. Events are triggered by socket activity or by WLAN connection/disconnection.

            - *handler* is the function that gets called when the IRQ is triggered.
            - *wake* must be ``machine.SLEEP``.

        Returns an IRQ object.

    Constants
    ---------

    .. data:: WLAN.STA
    .. data:: WLAN.AP

       selects the WLAN mode

    .. data:: WLAN.WEP
    .. data:: WLAN.WPA
    .. data:: WLAN.WPA2

       selects the network security

    .. data:: WLAN.INT_ANT
    .. data:: WLAN.EXT_ANT

       selects the antenna type

.. only:: port_openmvcam

    WINC类 – wifi扩展板驱动
    ================================

    ``WINC`` 类用于控制wifi扩展板。

    用法示例::

        import network

        wlan = network.WINC()
        wlan.connect("SSID", "KEY")

        wlan.ifconfig()

    构造函数
    ------------

    .. class:: WINC([mode=MODE_STATION])

       创建一个WINC驱动对象，并连接使用I/O引脚P1, P2, P3, P6, P7,P8的wifi扩展板。

       ``mode`` 控制WINC模块运行的模式:

         * network.WINC.MODE_STATION

           模块作为客户端连接到访问点。这是默认模式。

         * network.WINC.MODE_AP

           该模块将创建一个AP(访问点)并接受来自客户端的连接。

           .. note::
              设置AP模式后，必须调用start_ap()方法来配置AP。

              此外，WINC1500的AP实现有一些限制:

              * 一次只能连接一个客户端。
              * 仅支持OPEN或WEP安全性。
              * WiFi模块FW中存在一个错误，当客户端断开任何绑定套接字丢失时（它们只是停止工作）。 解决方法是，为服务器套接字设置超时以强制它引发异常，然后重新打开它（请参阅示例脚本）。

         * network.WINC.MODE_FIRMWARE:

           此模式支持WiFi模块固件更新。

           .. note::

              除非您知道自己在做什么，否则不要使用，模块附带最新的固件更新，无需更新固件。

    方法
    -------

    .. method:: winc.connect(ssid, [key=None, [security=WPA_PSK]])

       使用带有安全性 ``security`` 的密钥 ``key`` 连接到一个带有 ``ssid`` 的wifi网络。
       连接到网络后，使用 `usocket` 模块来打开TCP/UDP端口，以发送与接收数据。

       .. note::

          返回此方法需要一些时间。

    .. method:: winc.start_ap(ssid, [key=None, [security=OPEN, [channel=1]]])

       在AP模式下运行时，必须在创建WINC对象以配置和启动AP后调用此方法。

         * ssid: AP SSID  (必须设置).
         * key:  AP加密密钥。仅在安全性为WEP时才需要密钥。
         * security: AP security mode (only OPEN or WEP are supported).
         * channel: WiFi通道, 若有另外一个AP运行在此通道中，则改变此通道。

    .. method:: winc.disconnect()

       断开与wifi网络的连接。

    .. method:: winc.isconnected()

       若连接到访问点，并获取了一个IP地址，则返回True。

    .. method:: winc.connected_sta()

       此方法返回包含已连接客户端IP地址的列表。

    .. method:: winc.wait_for_sta(timeout)

       此方法阻止并等待客户端连接。 如果timeout为0，则将永久阻止。 此方法返回包含已连接客户端的IP地址的列表。

    .. method:: winc.ifconfig()

       Returns a list containing:

          * [0]: RSSI - 接收信号强度指示器(int)
          * [1]: 权限类型（见常量）
          * [2]: 设置服务标识符字符串(SSID)
          * [3]: MAC地址字符串 (XX:XX:XX:XX:XX:XX) (BSSID)
          * [4]: IP地址字符串 (XXX.XXX.XXX.XXX)

       当连接到网络。

    .. method:: winc.scan()

       返回包含的列表:

          * [0]: 通道号(int)
          * [1]: RSSI - 接收信号强度指示器 (int)
          * [2]: 权限类型（见常量）
          * [3]: MAC地址字符串 (XX:XX:XX:XX:XX:XX) (BSSID)
          * [4]: 设置服务标识符字符串(SSID)

       您无需连接即可调用。

    .. method:: winc.rssi()

       返回当前连接的网络的接收信号强度指示器(int)。

    .. method:: winc.fw_version()

       返回一个包含wifi扩展板固件版本数字的元组。

          * [0]: 固件主版本号(int)
          * [1]: 固件次版本号(int)
          * [2]: 固件补丁版本号int)
          * [3]: 驱动主版本号(int)
          * [4]: 驱动次版本号 (int)
          * [5]: 驱动补丁版本号 (int)
          * [6]: 硬件版本号- 芯片ID (int)

    .. method:: winc.fw_dump(path)

       将wifi扩展板固件转储到 ``path`` 的二进制文件中。 您必须将模块置于固件模式才能使用它。

    .. method:: winc.fw_update(path)

       使用在 ``path`` 中找到的二进制图像编程wifi扩展板。 您必须将模块置于固件模式才能使用它。

    常量
    ---------

    .. data:: winc.OPEN

       连接到一个开放的wifi网络。

    .. data:: winc.WEP

       连接到基于WEP的密码保护网络。

    .. data:: winc.WPA_PSK

       连接到基于WEP/PSK的密码保护网络。

    .. data:: winc.MODE_STA

       以station模式启动（即连接到网络）。

    .. data:: winc.MODE_AP

       从AP模式开始（即成为网络）。

   .. data:: winc.MODE_FIRMWARE

      在固件更新模式下设置。

   .. _network.WLAN:
