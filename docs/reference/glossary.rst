术语表
========

.. glossary::

    baremetal
        类似 :term:`MCU` 的一个无（完全）操作系统的系统。在baremetal系统上运行时，
        MicroPython实际上是通过一个命令解释器（REPL）来实现其面向用户的操作系统。

    board
       一个PCB板。该术语通常用于表示一个 :term:`MCU` 系统的特定模型，也指特定于
        某个板的 :term:`MicroPython port` （也可能指类似于 :term:`Unix port <MicroPython Unix port>` 的这种"无板"端口）。

    callee-owned tuple
       一些内置函数/方法返回的元组，包含在有限的时间内有效的数据，通常在下次使用给出
        相同的功能（或一组相关的功能）。下次使用后，可以改变元组中的数据，这也导致被使用
		的元组信息丢失。唯一有效的操作是提取他们的重要部分（包括制作副本）。callee-owned tuple
        是一个特定于MicroPython的构造（不可用于平常的Python语言），介绍用于内存分配优化。
        这个方法是被调用者拥有的元组被分配一次并存储在被叫方。后续调用不需要分配，允许在无法分配时返回多个值
        （例如在中断上下文中）或不可取（因为分配固有地导致内存碎片）。请注意，被叫方拥有元组是有效可变的元组，是一个例外
        Python的规则，元组是不可变的。（这可能很有趣,为什么元组被用于这样的目的，而不是可变的列表,原因是列表可以从用户变化
        应用程序方面也是如此，因此用户可以对被叫方拥有的东西做 被调用者不期望并且可能导致问题的列表;而一个元组受到保护。)

    CPython
        CPython是Python编程语言的参考实现，是目前最为著名且被大多数人使用的一种编程语言，
        不过也只是众多实现（包括Jython, IronPython, PyPy, MicroPython）中的其中一种。
        由于没有正式的Python语言规范，只有CPython文档，所以很难区分Python语言和CPython的特定实现。
        当然这也使得其他实现更为自由。例如，与CPython相比，MicroPython执行了许多不同的工作，
        但仍希望成为Python语言的实现。

    GPIO
        通用输入/输出。控制电信号的最简单方法。通过GPIO，用户可将硬件信号配置为输入或输出，
        并设置或获取其数字信号值（逻辑"0"或"1"）。MicroPython使用 :class:`machine.Pin`
        和 :class:`machine.Signal` 类提取访问GPIO权限。

    GPIO port
         一组 :term:`GPIO` 引脚，通常基于引脚的硬件特性（例如：可通过同一寄存器控制）。

    interned string
	    
		一种由其特殊标志（唯一）而不是他的具体地址辨识的字符串。interned string因此可以快速通过
		特殊索引找到，而不是通过比较内容。interned string 的缺点是内容上的操作花费时间（与现有
		interned string的数量成正比，即随着时间的推移变得越来越慢）并且用于interned string的存储空间不可回收。
        interned string 由MicroPython编译器和runtimer自动完成，它要么是实施所规定的（例如，功能关键字，参数由
		interned string  id表示）要么就是有价值的（例如 足够短的字符串，有机会要重复，因此实习他们会节省记忆
		副本）由于上述缺点，大多数字符串和I/O操作都不会生成interned string。
		
    MCU
         微控制器。与完整的计算系统相比，微控制器的资源通常要少得多，但是也更小、价格更低且耗电更少。
        MicroPython的设计小而优化，可在一般的现代微控制器上运行。

    micropython-lib
        MicroPython（通常）作为一个单独的可执行/二进制文件分配，仅有很少的内置模块。
        与 :term:`CPython`相比，MicroPython无扩展标准库。但有一个相关但独立的项目
        `micropython-lib <https://github.com/micropython/micropython-lib>`_
        ，该项目提供许多来自标准库中模块的实现。但是，这些模块的较大子集需要类似POSIX的环境
        （部分支持Linux、MacOS、Windows），因此仅可在MicroPython Unix端口上运行。
        模块的某些子集在baremetal端口上也可用。

        与 :term:`CPython` 单片标准库不同，micropython-lib模块设计为手动复制或
        使用 :term:`upip`来单独安装。
    MicroPython port
        MicroPython支持不同的板（ :term:`boards <board>`），RTOS和OS可相对容易地适应新系统。
        支持特定系统的MicroPython被称为该系统的"端口"。不同端口的功能特性相差极大。此文档旨在为在
        不同端口（"MicroPython核心"）上可用的通用API提供参考。注意：一些端口可能删除此处所述的API
        （由于资源限制）。这类区别、以及超出MicroPython核心功能的特定于端口的扩展都将在但单独的、
        特定于端口的文件中进行介绍。

    MicroPython Unix port
         Unix端口是MicroPython（ :term:`MicroPython ports <MicroPython port>`）的主要端口之一。
        其设计为在与POSIX兼容的操作系统上运行，如Linux、MacOS、FreeBSD、Solaris等。
        该端口还可作为Windows端口的基础。端口的重要性在于在有许多不同板（ :term:`boards <board>`）时，
        任意两个用户不可能使用相同的板，几乎所有的现代操作系统都具有一定程度的POSIX兼容性，
        此时Unix端口可作为一种所有用户都可访问的"共同基础"。因此，Unix端口用于初始原型、不同种类
        的测试、开发独立于机器的特性等。我们建议所有MicroPython用户（甚至包括仅在 :term:`MCU` 系统中
        运行MicroPython的用户）都了解一下Unix（或Windows）端口，因为该端口可提高工作效率，且为MicroPython工作流的一部分。


    port
        :term:`MicroPython port` 或 :term:`GPIO port`。若您尚未理解上文，建议您使用如上述示例的完整规格。


    stream
       也称为“类文件对象”。 提供顺序的对象对底层数据的读写访问。 流对象实现一个相应的接口，由``read（）``
	   等方法组成。``write（）``，``readinto（）``，``seek（）``，``flush（）``，``close（）``等。
	   流是MicroPython中的一个重要概念，即许多I / O对象实现流接口，因此可以一致地使用在不同的背景下可互换。
	   参见`uio`模块，有更多MicroPython中的流的信息。

    upip
       （字面意思为"micro pip"）。MicroPython的包管理器，灵感来自 :term:`CPython`的pip，但是更小功能也更少。
        Upip可在 :term:`Unix port <MicroPython Unix port>` 和
        :term:`baremetal` 端口上运行（提供文件系统和网络支持）。
