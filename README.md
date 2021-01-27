# [Acer-TravelMate-X40-OpenCore0.6.5](https://github.com/jin1ming/Acer-TravelMate-X40)

本引导适用于宏碁墨舞x40，OpenCore使用0.6.5。

在此基础上进行的修改： [linhnguyengas/Hackintosh-Acer-Swift3-SF315-52G](https://github.com/linhnguyengas/Hackintosh-Acer-Swift3-SF315-52G)

参考了：[Acer-Swift3-2018-hackintosh](https://github.com/FallenChromium/Acer-Swift3-2018-hackintosh)

工作内容：

- 修正声卡id

  需配合[ComboJack](https://github.com/hackintosh-stuff/ComboJack)使用

- 增加了AirportItlwm，可原生使用intel网卡，可使用隔空投送

  来自：[itlwm](https://github.com/OpenIntelWireless/itlwm)

- 修正了适用于intel网卡的蓝牙模块

- 修正了适应该笔记本的触摸板热补丁

- 为笔记本镁光1100硬盘增加sata驱动

此外，

- HDMI、VGA未经测试