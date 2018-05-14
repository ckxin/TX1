# TX1 #

by xin

---

![Jetson TX1](F:/md/res/TX1_1.jpg)


## 1. DC Power ##

VDD_IN(min:5.5V  max:19.6V)

VDD_RTC(min:2.5V  max:3.5V)

单电源供电，VDD_IN必须由Jetson TX1所连接的载板提供，所有内部模块电压及I/O口电压,均由VDD_IN通过电源管理集成电路(PMIC)及电压可调节电路产生。

VDD_RTC为备用电源，当无VDD_IN时，VDD_RTC为RTC(实时时钟)供电。

## 2.Antennas ##

用来连接WIFI/BT，支持的天线具有以下特性：

1）母I-PEX连接器

2）双频道，偶极子

3）2.4GHz和5GHz频段 

4）50Ω阻抗 

## 3.USB ##

Jetson TX1具有一个USB Type A 和一个Micro USB，支持USB增强Host控制器接口规范1.0，USB规范2.0，USB规范3.0.

USB Type A可用来连接具有USB接口的外设，如鼠标，键盘等，还可接HUB用来扩展USB接口;Micro USB类似于手机数据线接口，可用来传输数据进行通信。

## 4.M.2E ##

M.2(原NGFF,Next Generation Form Factor)是取代了mSATA用于内置计算机扩展卡和相关连接器的规范。M.2更灵活的物理规格支持不同长宽尺寸的模块，并且配上更先进的接口能力，使得M.2比mSATA更适合于固态存储应用，特别是应用于小型设备，比如超极本，平板电脑等。

M.2E(M.2 Key E)是M.2的一种模组，支持PCIe×2,USB2.0,I2C,SDIO,UART和PCM接口，主要应用于无线连接，如WiFi，蓝牙，NFC，GNSS。

## 5.HDMI ## 

HDMI（High-Definition Multimedia Interface 高清多媒体接口）是专有的音频/视频接口，用于将未压缩的视频数据和压缩或未压缩的数字音频数据从兼容HDMI的源设备（如显示控制器）传输到兼容的计算机显示器，视频投影仪，数字电视或数字音频设备。

TX1 HDMI标准：HDMI规范v2.0、HDCP系统规范 v2.2、HDCO系统规范 v1.3。

1）HDMI2.0模式（3.4Gbps < 数据速率 <=6Gbps）

2）HDMI1.4模式（数据速率 <= 3.4Gbps）

3）来自HAD控制器的多通道音频，多达8个通道，192kHz24-bit

4）厂家专有的帧内（VSI）包传输

5）24-bit RGB和24-bit YUV444（HDMI）像素格式

6）变化最小化差分信号（TDMS）功能上达340MHz像素时钟速率


## 6.SD-Card ##

SDCARD接口用来支持SD卡插座,可外界SD卡来扩展内存。


## 7.Ethernet ##

以太网口，支持10/100/1000Mbps以太网，可用来连接以太网络，还可以通过网线与其他设备进行通信。

## 8.PCI-E*4 ##

PCI Express（Peripheral Component Interconnect Express）是一种高速串行计算机扩展总线标准,PCIE采用总线拓扑结构，总线链路支持任意两个端点之间的全双工通信，发送端和接收端中都含有TX(发送逻辑)和RX(接收逻辑)。在PCIe总线的物理链路的一个数据通路中，由两组差分信号，共4根信号线组成。其中发送链路公用一组差分信号，接受链路共用一组差分信号。一个PCIe链路可以由多个数据通路组成。

TX1集成了x5数据通路PCIe桥，使能控制路径从TX1到外部PCIe设备，2个PCIe Gen2控制器（一个x4，一个x1）支持连接到1个或2个终端上。

PCIE可用于接外部GPU，外接存储设备(如存储卡和固态驱动器(SSD)),还可用于群集互连等。


## 9.Display ##

显示接口，外接4K屏幕

## 10.GPIO ##

GPIO(General-purpose input/output通用输入输出)是集成电路或计算机板上的通用引脚，其行为在用户运行时是可控制的。GPIO引脚没有预定义的用途，默认情况下不使用，有时系统集成商构建完整系统可能需要一些额外的控制线，通过芯片提供这些GPIO线可避免再增加额外的电路。TX1上有两个GPIO扩展口。

**功能：**

1）GPIO引脚可以配置为输入或输出

2）GPIO引脚可以被使能/禁止

3）输入值是可读的（通常是高或低）

4）输出值是可写/可读的

5）输入值通常可用作IRQ（通常用于唤醒事件）

**用途：**

1）引脚不足的器件：集成电路（如片上系统，嵌入式和定制硬件）以及可编程逻辑器件（例如FPGA）

2）多功能芯片：电源管理器，音频编解码器和视频卡

3）嵌入式应用，使用GPIO用于从各种环境读取传感器（视频，温度，加速度等），以及用于将输出写入到直流电动机，音频，液晶显示器或LED状态。

## 11.Camera Module ##

摄像机接口，外接摄像机模块。

## 12.Fan Power ##

风扇开关

## 13.UART ##

UART(Universal  Asynchronous  Receiver/Transmitter,通用异步接收机-发射机)是一种用于异步串行通信的计算机硬件设备，其数据格式和传输速度都是可配置的。UART发送和接收必须设置为相同的位速度，字符长度，奇偶校验和停止位才能正常工作。消息帧从一个低位起始位开始，后面是5~8个数据位，一个可用的奇偶位和一个或几个高位停止位。在嵌入式设计中，UART用于主机与辅助设备通信，如汽车音响与外接AP之间的通信，与PC机通信包括与监控调试器和其它器件，如EEPROM通信。

TX1的UART控制器为发送和接收部分提供串行数据同步和数据转换(串转并和并转串)。通过对发送数据添加起止位构成数据字符来完成串行数据流的同步，通过在对数据字符添加奇偶校验位来完成数据的完整性。

## 14.JTAG ##

JTAG(Joint Test Action Group；联合测试工作组)是一种国际标准测试协议，主要用于芯片内部测试。标准的JTAG接口是4线：TMS、TCK、TDI、TDO，分别为模式选择、时钟、数据输入和数据输出线。

JTAG就是在每个I/O引脚增加一个移位寄存器单元，因为这些寄存器位于芯片的边界，所以叫做边界扫描寄存器。当芯片处于调试状态时，边界扫描寄存器可以将芯片和外围的输入／输出隔离开来。通过边界扫描寄存器单元，可以实现对芯片输入／输出信号的观察和控制。对于芯片的输入管脚，可以通过对应的边界扫描单元中把信号加载到管脚，而对于输出管脚，则通过对应的边界扫描单元捕获芯片输出的信号。在正常的运行状态下，边界扫描寄存器对芯片来说是透明的，所以正常的运行不会受到任何影响。这样，边界扫描寄存器提供了一种便捷的方式用于观测和控制所需调试的芯片。所有这些扫描单元串行连接起来，就形成了一个扫描链。一般芯片都会提供几条扫描链，用来实现完整的测试功能。

通常所说的JTAG大致分两类，一类用于测试芯片的电气特性，检测芯片是否有问题；一类用于Debug，一般支持JTAG的CPU内都包含了这两个模块。JTAG 主要应用于：电路的边界扫描测试和可编程芯片的在线系统编程。

## 15.SATA ##

SATA（Serial Advanced Technology Attachment，串行高级技术附件）是一种基于行业标准的串行硬件驱动器接口，可将主机总线适配器连接到大容量存储设备，如硬盘驱动器，光盘驱动器和固态驱动器。SATA在承继旧的PATA(Parallel Advanced Technology Attachment，并行高级技术附件)标准基础上提供了一些新的优势：减少的电缆尺寸和成本，本地热插拔，通过更高的信号速率实现更快的数据传输，以及通过（可选的）I/O排队协议进行更高效的传输。

Jetson TX1的SATA控制器使能一个从Jetson TX1到外部SATA设备的控制通道。能够连接SSD/HDD/ODD驱动，支持二代驱动器的最大吞吐率。

**特征：**

1）支持端口复用

2）支持的接口和连接器：标准的内部连接器、内部微连接器、mSATA连接器、BGA SSD接口

3）不支持的接口和连接器：外部连接器（eSATA）、USM、内部LIF-SATA

## 16.Power Button ##

电源按钮

## 17.Force Recovery ##

强制恢复按钮

## 18.User Defined Button ##

用户自定义按钮

## 19.Reset Button ##

复位按钮




----
# reference #

* [developer.nvidia.com](https://developer.nvidia.com/embedded/downloads#?search=module%20data%20sheet&cl=date,1&tx=$product,jetson_tx1)
* [wiki/M.2](https://en.wikipedia.org/wiki/M.2)
* [wiki/HDMI](https://en.wikipedia.org/wiki/HDMI)
* [wiki/Universal_asynchronous_receiver-transmitter](https://en.wikipedia.org/wiki/Universal_asynchronous_receiver-transmitter)
* [item/UART](https://baike.baidu.com/item/UART/4429746#8)
* [item/JTAG](https://baike.baidu.com/item/JTAG/5013317)
* [blog.chinaunix.net](http://blog.chinaunix.net/uid-29567807-id-4183980.html)
* [Serial_ATA#Topology](https://en.wikipedia.org/wiki/Serial_ATA#Topology)