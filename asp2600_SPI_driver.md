dts 配置：
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
	flash@2 {
		status = "okay";
		label = "spi0:2";
		compatible = "rohm,dh2228fv";
	};

};

&spi1 {
	status = "okay";
	flash@0 {
		label = "spi1:0";
		status = "okay";
		compatible = "rohm,dh2228fv";
	};
	flash@1 {
		label = "spi1:1";
		status = "okay";
		compatible = "rohm,dh2228fv";
	};
};

//ast2600-spi驱动只支持2个flash 可根据aspeed的代码修复，兼容所有spi驱动，
遇到spi2.1 spi2.2 spi1.1设备驱动起不来可以直接使用aspeed驱动和dts已经dtsi文件
//https://github.com/AspeedTech-BMC/linux
&spi2 {
	status = "okay";
	pinctrl-names = "default";
	pinctrl-0 = <&pinctrl_spi2_default &pinctrl_spi2cs1_default
				&pinctrl_spi2cs2_default>;
	flash@0 {
		label = "spi2:0";
		status = "okay";
		compatible = "rohm,dh2228fv";
	};
	flash@1 {
		label = "spi2:1";
		status = "okay";
		compatible = "rohm,dh2228fv";
	};
	flash@2 {
		label = "spi2:2";
		status = "okay";
		compatible = "rohm,dh2228fv";
	};
};


