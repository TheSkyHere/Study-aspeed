u-boot:
&fmc {
	status = "okay";

	pinctrl-names = "default";
	pinctrl-0 = <&pinctrl_fmcquad_default>;

	flash@0 {
		compatible = "spi-flash", "sst,w25q256";
		status = "okay";
		spi-max-frequency = <50000000>;
	};
	flash@1 { 
		compatible = "spi-flash", "sst,w25q256"; 
		status = "okay"; 
		spi-max-frequency = <50000000>; 
	};
};

kernel:
&fmc {
	status = "okay";
	flash@0 {
		status = "okay";
		label = "bmc";
		m25p,fast-read;
		spi-max-frequency = <50000000>;
#include "openbmc-flash-layout-64.dtsi"
	};
	flash@1 {
		status = "okay";
		label = "alt-bmc";
		m25p,fast-read;
		spi-max-frequency = <50000000>;
#include "openbmc-flash-layout-64-alt.dtsi"
	};
};

设置OTP寄存器才能支持dual flash（需要什么模式的dual flash 看2600SPEC）

uboot设置5分钟的WDT2超时时间：
mw 0x1e620068 0x00000bb8 //设置5分钟
mw 0x1e620068 0x00000000 //设置0分钟

mw 0x1e62006c 0x4755 //加载时间

uboot中关闭WDT2
mw 0x1e620064 0x00000000

查看flash启动位子：
md 0x1e620064

WDT2 timeout后会自动选择另一块flash启动


设置5分钟的WDT2超时时间：
devmem 0x1e620068 32 0x00000bb8 //设置5分钟
devmem 0x1e620068 32 0x00000000 //设置0分钟

devmem 0x1e62006c 32 0x4755 //加载时间

uboot中关闭WDT2
devmem 0x1e620064 32 0x00000000

查看flash启动位子：
devmem 0x1e620064

enable WDT2
devmem 0x1e62 0064 32 0x00000001

WDT2 timeout后会自动选择另一块flash启动
devmem 0x1e620068 32 0x00000000
devmem 0x1e62006c 32 0x4755
devmem 0x1e620064 32 0x00000001

mw 0x1e620068 32 0x00000000
mw 0x1e62006c 32 0x4755
mw 0x1e620064 32 0x00000001


2500切到从flash
devmem 0x1e785034 w 0x01
devmem 0x1e785024 32 0x00989680
devmem 0x1e785028 32 0x4755
devmem 0x1e78502c 32 0x000000b3

devmem 0x1e785034 w 0x01
devmem 0x1e785024 32 0x00989680
devmem 0x1e785028 32 0x4755
devmem 0x1e78502c 32 0x00000033


读64Mflash中的数据0x0000 0000 ~ 0x0400 0000
devmem 0x04000000 32 0x000000b3
devmem 0x3FFFC00 32 0x000000b3

//2500
//从master切换到slave
mw 0x1e785034 0x01
mw 0x1e785024 0x00989680
mw 0x1e785028 0x4755
mw 0x1e78502c 0x000000b3

devmem 0x1e785034 32 0x01
devmem 0x1e785024 32 0x00989680
devmem 0x1e785028 32 0x4755
devmem 0x1e78502c 32 0x000000b3

//2500
//从slave切换到master
mw 0x1e785034 0x01
mw 0x1e785024 0x00989680
mw 0x1e785028 0x4755
mw 0x1e78502c 0x00000033

devmem 0x1e785034 32 0x01
devmem 0x1e785024 32 0x00989680
devmem 0x1e785028 32 0x4755
devmem 0x1e78502c 32 0x00000033

关闭wdt2：
devmem 0x1e78502c 32 0x00
