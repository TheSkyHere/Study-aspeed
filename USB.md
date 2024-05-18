### 关于ethernet over USB的使用(2600 ocp):
1.dts中需要enable vhub, 同时需要disable:ehci0、ehci1、uhci
2.打开config配置:
    CONFIG_USB_CONFIGFS_ECM=y
    CONFIG_USB_ASPEED_VHUB=y
    CONFIG_USB_GADGET=y
# USB CDC复合设备是一种可以同时支持多种USB设备类型的设备。它可以同时作为一个串口设备和一个以太网设备使用
    CONFIG_USB_CDC_COMPOSITE=y
# USB以太网是一种通过USB接口连接计算机和以太网网络的设备。它可以用于连接各种网络设备，如路由器、交换机、网络存储设备等
    CONFIG_USB_ETH=y
# 同时打开CONFIG_USB_CDC_COMPOSITE和CONFIG_USB_ETH会出现两个usb网口的，使用其中一种即可。


### 在2500 fb中发现只enable vhub   usb0 网络就可以正常使用。


### AST2500 支持USB2A USB2B 两个port口
# DTS中默认
# vhub使用USB2A port (有这个的基础USB 网口可以出来,同时需要打开对应的config-usb-net)
# ehci0使用USB2A port (通过lsusb可以查看到对应的usb设备)
# ehci1使用USB2B port (通过lsusb可以查看到对应的usb设备)
# uhci :it will follow EHCI (通过lsusb可以查看到对应的usb设备)