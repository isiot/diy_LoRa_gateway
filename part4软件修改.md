LoRa网关的软件部分本来应该做适当修改再进行编译的，但本教程第3部分用来编译的代码是我从原始lora_gateway代码fork的一个分支并做了必要的修改，所以你不需要修改任何文件

如果你想知道我所做的修改，请阅读下文并查看我提交的commits。

我第一次编译时，软件并没有运行起来，我意识到USB驱动没有被识别，因为我手上的mCard与LoRa开发套件（即与https://github.com/Lora-net/lora_gateway配套的套件）

使用了不同的FTDI USB to SPI转换芯片FT232H，每个FT232H都有不同的PID(Product IDentification)，我修改了源文件中的以下文件：
 
loragw_spi.ftdi.c
```
/* parameters for a FT232H */
#define VID     0x0403
#define PID     0x6010
 ```
 
99-libftdi.rules
```
 # FTDI Devices: FT2232H
SUBSYSTEM=="usb", ENV{DEVTYPE}=="usb_device", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6014", MODE="0664", GROUP="plugdev"
```

为了使用FTDI SPI，我们必须做以下修改

library.cfg
```
CFG_SPI= ftdi
```

现在，板子可以被正确识别但是还不能与SX1301通信，因为板子还处于复位状态，复位引脚是由主机通过FTDI的GPIO引脚来控制的，修改以下文件：

loragw_spi.ftdi.c
```
/* toggle pin ADBUS5 of the FT232H */
/* On the MTAC-LORA, it resets the SX1301 */
a = PinLow(mpsse, GPIOL1);
b = PinHigh(mpsse, GPIOL1);
```

现在sx1301似乎开始工作但没有读寄存器，这是因为sx1301没有时钟源，两个sx1257通过共享的TCXO进行时钟驱动，sx1257具有时钟输出，用于驱动sx1301时钟，
查看schematic的文档，我发现是radio_0的时钟输出而不是radio_1的被连接到了sx1301的时钟输入：

global_conf.json
```
"clksrc": 0, /* radio_0 provides clock to concentrator */
```

Note：写完本教程以后，MultiTech公开发布了具有相同补丁的的GIT库文件，地址在


http://git.multitech.net/cgi-bin/cgit.cgi/lora_gateway.git/commit/?h=1.7.0-mts















