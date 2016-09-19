Raspberry Pi足够强大，Raspbian（Raspbian，树莓派自带的基于debian的OS）系统自带了GCC，Git…

这样我们就可以在Raspberry Pi里面进行软件编译，而不需要在桌面PC上编译后上传二进制文件到Raspberry Pi。

打开一个控制台或SSH会话，默认的用户名是“pi”和密码“raspberry”。

首先我们需要FTDI芯片FT232HQ的驱动（libmpsse.so），不要试图在网上找到它，因为它是依赖于平台的，所以最好是从源码编译：

sudo apt-get update

sudo apt-get install git --assume-yes

sudo apt-get install libftdi-dev --assume-yes

cd ~

wget https://storage.googleapis.com/google-code-archive-downloads/v2/code.google.com/libmpsse/libmpsse-1.3.tar.gz

tar zxvf libmpsse-1.3.tar.gz

cd libmpsse-1.3/src

./configure --prefix=/usr --disable-python

make

sudo make install

cd /etc/udev/rules.d

sudo wget https://raw.githubusercontent.com/mirakonta/lora_gateway/master/libloragw/99-libftdi.rules

sudo udevadm control --reload-rules

sudo udevadm trigger

sudo adduser pi plugdev

cd ~

现在我们可以继续关于LoRa网关的部分了：下载源代码和编译，一旦完成编译，就把他们复制到 ~/lora/exec：

mkdir ~/lora

cd ~/lora

mkdir exec

git clone https://github.com/mirakonta/lora_gateway.git

git clone https://github.com/mirakonta/packet_forwarder.git

cd ~/lora/lora_gateway

make clean all

cd ~/lora/packet_forwarder

make clean all

cp ~/lora/packet_forwarder/basic_pkt_fwd/basic_pkt_fwd ~/lora/exec/

cp ~/lora/packet_forwarder/gps_pkt_fwd/gps_pkt_fwd ~/lora/exec/

cp ~/lora/packet_forwarder/gps_pkt_fwd/global_conf.json ~/lora/exec/

cd ~/lora/exec

最后，我们执行的LoRa gateway：

sudo ./gps_pkt_fwd

本教程使用这些链接：

[](https://github.com/Lora-net/lora_gateway/blob/v3.1.0/libloragw/install_ftdi.txt)
[](https://github.com/Lora-net/packet_forwarder/wiki/Use-with-Raspberry-Pi)













