*******************************
:mod:`usocket` -- socket模块
*******************************

.. module:: usocket
   :synopsis: socket模块

该模块实现相应 `CPython` 模块的子集，如下所示。更多信息，请参见
|see_cpython_module| :mod:`python:socket`.

该模块提供BSD socket接口的访问。

对比请参见相应的 CPython 模块 。

.. admonition:: 与CPython区别
   :class: attention

   为提高效率与一致性，MicroPython中的套接字对象直接实现流（类文件）接口。在CPython中，
   需使用 `makefile()` 方法来将套接字转换为类文件对象。该方法仍由MicroPython（但是是无操作）支持，所以在CPython的兼容性问题上，请一定使用该方法。

Socket地址格式
------------------------

下列函数期望一个网络地址，并以(ipv4_address, port)格式接受该地址。
其中地址是一个带有点符号数字IPv4地址的字符串，如 ``"8.8.8.8"`` ；
port是一个取值1-65535的整数端口号。注意：域名不被接受为ipv4地址，
应首先使用 ``socket.getaddrinfo()`` 来分解。 


 ``usocket`` 模块的本机套接字地址格式是一个由 `getaddrinfo` 函数返回的不透明数据类型，须用其来解析文本地址（包括数字型地址）::

    sockaddr = usocket.getaddrinfo('www.micropython.org', 80)[0][-1]
    # You must use getaddrinfo() even for numeric addresses 您必须使用getaddrinfo()，即使是用于数字型地址
    sockaddr = usocket.getaddrinfo('127.0.0.1', 80)[0][-1]
    # Now you can use that address 现在您可以使用这一地址了
    sock.connect(addr)

使用 `getaddrinfo` 是处理地址最有效（在内存和处理能力方面皆是如此）且最便捷的方式。 

但是，如下所述， ``socket`` 模块（注意此处所说的与本机MicroPython  ``usocket`` 模块的不同）提供与
CPython兼容的使用元组指定地址的方法。注意： ``socket`` 模块可为内置或需 `从micropython-lib` 中安装
（在 `MicroPython Unix port` 的情况下），这取决于 `MicroPython port` ，而某些端口仍只接受元组格式的数字地址，
且需使用 `getaddrinfo` 函数以解析域名。

总结:

* 编写便捷式应用程序时，请使用 `getaddrinfo` 。
* 若您的端口支持，下述元组地址可用作快速破解和交互式使用的快捷方式。

 ``socket`` 模块的元组地址格式:

* IPv4: *(ipv4_address, port)*, 此处是一个带有点记法的数字型ipv4_address的字符串，
  例如 ``"8.8.8.8"`` ，端口是一个介于1-65535的整数端口号。注意：域名不被接受为ipv4_address形式，
  需首先使用 `usocket.getaddrinfo()` 解析。
* IPv6: *(ipv6_address, port, flowinfo, scopeid)*, 此处ipv6_address是一个带有冒号标记法的
  数字型ipv6_address的字符串，例如 ``"2001:db8::1"`` ，其端口是一个介于1-65535的整数端口号。
  Flowinfo须为0。scopeid是链接本地地址的接口范围标识符。注意：域名不被接受为ipv6_address形式，
  需首先使用 `usocket.getaddrinfo()` 解析。IPv6支持的可用性取决于 `MicroPython port` 。

函数
---------

.. function:: socket(af=AF_INET, type=SOCK_STREAM, proto=IPPROTO_TCP)

   使用给定的地址群、类型和协议号创建一个新的socket对象。

.. function:: getaddrinfo(host, port)

   将参数翻译为一个5元组序列，该序列包含创建一个与设备连接的socket所需的全部必要参数。该5元组列表有以下结构::

      (family, type, proto, canonname, sockaddr)

   下列示例演示了如何连接到给定的url::

      s = socket.socket()
      s.connect(socket.getaddrinfo('www.micropython.org', 80)[0][-1])

   .. admonition:: Difference to CPython
      :class: attention

      该函数发生错误时，会引发一个 ``socket.gaierror`` 异常（ `OSError` 子类）。
      MicroPython并不具有 ``socket.gaierror`` ，会直接引发OSError。
      注意： `getaddrinfo()` 的错误数量组成一个单独的名称空间，可能与 `uerrno`  -- 系统错误代码模块中的错误数量不匹配。
      为区分 `getaddrinfo()` 错误，该错误使用负数标记，标准系统错误为正数（错误数可通过使用异常对象的 ``e.args[0]`` 特性访问）。暂时使用负数，未来可能改变。


常量
---------

.. data:: AF_INET
          AF_INET6

   家庭类型。
   可用性取决于特定的板。

.. data:: SOCK_STREAM
          SOCK_DGRAM

   socket类型。

.. data:: IPPROTO_UDP
          IPPROTO_TCP

   IP 协议数。

.. data:: usocket.SOL_*

   套接字操作阶层（ `setsockopt()` 的一个参数）。确切目录取决于MicroPython端口。

.. data:: usocket.SO_*

   套接字选项（ `setsockopt()` 的一个参数）。确切目录取决于MicroPython端口。

.. only:: port_wipy

   .. data:: IPPROTO_SEC

      Special protocol value to create SSL-compatible socket.

socket类
============

方法
-------

.. method:: socket.close()

   标记关闭的socket。一旦发生这种情况，所有将对socket对象进行的操作都将失败。远程端将无法再接收到任何数据（队列数据刷新后）。

   Socket在垃圾回收时自动关闭，但是建议您明确地关闭它们，或在使用时附加声明语句。

.. method:: socket.bind(address)

   将socket绑定到地址。socket不能为已绑定的。

.. method:: socket.listen([backlog])

   启用一个服务器以接受连接。若指定积压工作，则其不得小于0（若数值小于0，则需设置为0）；指定系统在拒绝新连接之所允许的未接受的连接的数量。若未指定，则选择一个默认的合理值。

.. method:: socket.accept()

   接受连接。Socket须绑定到一个地址并监听连接。返回值是两个数值（conn, address），其中conn为一个新的socket对象，可用来发送和接收连接上的数据，而address是绑定到连接另一端的socket的地址。

.. method:: socket.connect(address)

   连接到地址上的远程socket。

.. method:: socket.send(bytes)

   将数据发送到socket。Socket须连接到一个远程socket。

.. method:: socket.sendall(bytes)

   将数据发送到socket。Socket须连接到一个远程socket。

   将所有数据发送到套接字。套接字须连接到一个远程套接字。与 `send()` 不同，该方法会通过连续发送尽量发送所有数据。

   该方法在非阻塞套接字上的行为尚未定义。因此在MicroPython中，推荐使用 `write()` 方法，该方法对阻塞套接字实行相同的“无短写”方法，并将返回在非阻塞套接字上发送的字节的数量。

.. method:: socket.recv(bufsize)

   从socket上接收数据。返回值是一个表示接收到的数据的字节对象。要接收到的数据最大数量由缓冲区大小指定。

.. method:: socket.sendto(bytes, address)

   将数据发送到socket。Socket不应连接到远程socket，因为目的地socket由 *address* 指定。

.. method:: socket.recvfrom(bufsize)

   从socket上接收数据。返回值是两个数值（bytes, address），其中bytes是一个表示接收到的数据的字节对象，address是发送数据的socket地址。

.. method:: socket.setsockopt(level, optname, value)

   设置给定的socket选项的值。在socket模块(SO_* etc.)中定义了所需的符号常量。该值可为一个整数，也可为一个表示缓冲区的bytes类对象。

.. method:: socket.settimeout(value)

   设置阻塞socket操作的超时时间。值参数可以是一个表示秒或None的非负浮点数。若给定一个非零值，且在操作完成前超时时间已过期，则后续的socket操作将会引发异常。若给定0值，则socket采用非阻塞模式。若给定None，则socket给定阻塞模式。

   .. admonition:: 与CPython区别
      :class: attention

      CPython在超时时引发 ``socket.timeout`` 异常，即一个 `OSError` 的子类。MicroPython则直接引发OSError。
      若您使用 ``except OSError:`` 来匹配异常，您的代码将在MicroPython和CPython中同时运行。

.. method:: socket.setblocking(flag)

   设置socket的阻塞或非阻塞模式：若标记为false，则将该socket设置为非阻塞模式，而非阻塞模式。

   该方法为某些settimeout()调用的简写::

   * ``sock.setblocking(True)`` is equivalent to ``sock.settimeout(None)``
   * ``sock.setblocking(False)`` is equivalent to ``sock.settimeout(0)``

.. method:: socket.makefile(mode='rb', buffering=0)

   返回一个与socket相关联的文件对象。具体的返回类型取决于给定makefile()的参数。该支持仅限于二进制模式（‘rb’和‘wb’）. 
   
   CPython的参数为：不支持 *encoding* 、 *errors* 、 *newline* 。

   Socket须为阻塞模式；允许超时存在，但若出现超时，文件对象的内部缓冲区可能会以不一致状态结束。

   .. admonition:: 与CPython区别
      :class: attention

      由于MicroPython不支持缓冲流，则将忽略缓冲参数的值，且将按照该值为0（未缓冲）时处理。

   .. admonition:: 与CPython区别
      :class: attention

      关闭所有由makefile()返回的文件对象，同样将关闭原始socket。

.. method:: socket.read([size])

   从socket中读取size字节。返回一个字节对象。若未给定 ``size`` ，则按照类似 ``socket.readall()`` 的模式运行，见下。

.. method:: socket.readinto(buf[, nbytes])

   将字节读取入缓冲区。若指定 *nbytes* ，则最多读取该数量的字节。否则，最多读取 *len(buf)* 数量的字节。正如 ``read()`` ，该方法遵循“无短读”方法。

   返回值：读取并存入缓冲区的字节数量。

.. method:: socket.readline()

   读取一行，以换行符结尾。

   返回值：读取的行。

.. method:: socket.write(buf)

   将字节缓冲区写入socket。

   返回值：写入的字节数量。

.. exception:: socket.error

   MicroPython中没有这一异常。

   .. admonition:: 与CPython区别
        :class: attention

        CPython曾经有 ``socket.error`` 异常（ `OSError` 的别名），现已弃用。在MicroPython中，直接使用 `OSError` 。
