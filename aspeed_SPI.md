dts配置：
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
	m25p,fast-read;
	pinctrl-names = "default";
	pinctrl-0 = <&pinctrl_spi1_default>;
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
dtsi 配置：
		fmc: spi@1e620000 {
			reg = <0x1e620000 0xc4>,
				<0x20000000 0x10000000>;
			reg-names = "spi_ctrl_reg", "spi_mmap";
			compatible = "aspeed,ast2600-fmc";
			#address-cells = <1>;
			#size-cells = <0>;
			clocks = <&syscon ASPEED_CLK_AHB>;
			status = "disabled";
			interrupts = <GIC_SPI 39 IRQ_TYPE_LEVEL_HIGH>;
			num-cs = <3>;
			flash@0 {
				reg = < 0 >;
				compatible = "jedec,spi-nor";
				spi-max-frequency = <50000000>;
				spi-rx-bus-width = <2>;
				status = "disabled";
			};
			flash@1 {
				reg = < 1 >;
				compatible = "jedec,spi-nor";
				spi-max-frequency = <50000000>;
				spi-rx-bus-width = <2>;
				status = "disabled";
			};
			flash@2 {
				reg = < 2 >;
				compatible = "jedec,spi-nor";
				spi-max-frequency = <50000000>;
				spi-rx-bus-width = <2>;
				status = "disabled";
			};
		};

		spi1: spi@1e630000 {
			reg = <0x1e630000 0xc4>,
				<0x30000000 0x10000000>;
			reg-names = "spi_ctrl_reg", "spi_mmap";
			compatible = "aspeed,ast2600-spi";
			#address-cells = <1>;
			#size-cells = <0>;
			clocks = <&syscon ASPEED_CLK_AHB>;
			num-cs = <2>;
			interrupts = <GIC_SPI 65 IRQ_TYPE_LEVEL_HIGH>;
			status = "disabled";
			flash@0 {
				reg = < 0 >;
				compatible = "jedec,spi-nor";
				spi-max-frequency = <50000000>;
				spi-rx-bus-width = <2>;
				status = "disabled";
			};
			flash@1 {
				reg = < 1 >;
				compatible = "jedec,spi-nor";
				spi-max-frequency = <50000000>;
				spi-rx-bus-width = <2>;
				status = "disabled";
			};
		};

		spi2: spi@1e631000 {
			reg = < 0x1e631000 0xc4>,
				<0x50000000 0x10000000>;
			reg-names = "spi_ctrl_reg", "spi_mmap";
			compatible = "aspeed,ast2600-spi";
			#address-cells = <1>;
			#size-cells = <0>;
			clocks = <&syscon ASPEED_CLK_AHB>;
			num-cs = <3>;
			interrupts = <GIC_SPI 65 IRQ_TYPE_LEVEL_HIGH>;
			status = "disabled";
			flash@0 {
				reg = < 0 >;
				compatible = "jedec,spi-nor";
				spi-max-frequency = <50000000>;
				spi-rx-bus-width = <2>;
				status = "disabled";
			};
			flash@1 {
				reg = < 1 >;
				compatible = "jedec,spi-nor";
				spi-max-frequency = <50000000>;
				spi-rx-bus-width = <2>;
				status = "disabled";
			};
			flash@2 {
				reg = < 2 >;
				compatible = "jedec,spi-nor";
				spi-max-frequency = <50000000>;
				spi-rx-bus-width = <2>;
				status = "disabled";
			};
		};
		
使用aspeed驱动：spi-aspeed.c