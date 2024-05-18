确认BIOS flash在BMC 的哪条SPI上。
配置dts支持



# 2500
## 驱动
    使用的是aspeed-smc.c驱动
## dtsi：
    spi1: spi@1e630000 {
        reg = < 0x1e630000 0xc4
            0x30000000 0x08000000 >;
        #address-cells = <1>;
        #size-cells = <0>;
        compatible = "aspeed,ast2500-spi";
        clocks = <&syscon ASPEED_CLK_AHB>;
        status = "disabled";
        flash@0 {
            reg = < 0 >;
            compatible = "jedec,spi-nor";
            spi-max-frequency = <50000000>;
            status = "disabled";
        };
        flash@1 {
            reg = < 1 >;
            compatible = "jedec,spi-nor";
            spi-max-frequency = <50000000>;
            status = "disabled";
        };
    };
## dts：注意33000000
    &spi1 {
        status = "okay";
        compatible = "aspeed,ast2500-spi-master";

        flash@0 {
            status = "okay";
            spi-max-frequency = <33000000>;
        };
    };
## flashcp 方式升级mtd驱动加载相关指令:
    echo spi1.0  > /sys/bus/spi/drivers/spi-nor/bind    添加spi1.0设备
    随后就会生成 mtd设备，然后使用flashcp升级

## flashrom升级：参考facebook的spi_util.sh的方式升级。



# 2600 
## 驱动
    使用的是spi-aspeed.c驱动 可以从ASPEED的github中获取
## dtsi：
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
## dts：
    &spi1 {
        status = "okay";
        m25p,fast-read;
        pinctrl-names = "default";
        pinctrl-0 = <&pinctrl_spi1_default>;
        flash@0 {
            label = "BIOS_flash";
            status = "okay";
        };
        flash@1 {
            label = "spi1:1";
            status = "okay";
        };
    };
## flashcp 方式升级mtd驱动加载相关指令:
    echo spi1.0  > /sys/bus/spi/drivers/spi-nor/bind    添加spi1.0设备
    随后就会生成 mtd设备，然后使用flashcp升级

## flashrom升级：参考facebook的spi_util.sh的方式升级。





## spi-nor 驱动相关卸载，加载：
echo spi1.0 > /sys/bus/spi/drivers/spidev/unbind
echo spi1.0 > /sys/bus/spi/drivers/at25/unbind
echo spi1.0 > /sys/bus/spi/drivers/spi-nor/unbind

echo spi-nor  > /sys/bus/spi/devices/spi1.0/driver_override 
echo at25 > /sys/bus/spi/devices/spi1.0/driver_override 


echo spi1.0  > /sys/bus/spi/drivers/spi-nor/bind
echo spi1.0  > /sys/bus/spi/drivers/at25/bind



## fmc control 驱动相关卸载，加载：
root@lambda_bmc:/sys/bus/platform/drivers/aspeed-smc# echo 1e620000.spi > unbind 
[ 4406.592924] Deleting MTD partitions on "spi0.0":
[ 4406.597596] Deleting u-boot MTD partition
[ 4406.645843] Deleting env MTD partition
[ 4406.684848] Deleting fit MTD partition
[ 4406.714479] Deleting data0 MTD partition
[ 4406.746610] Removing MTD device #3 (data0) with use count 1
[ 4406.752347] Error when deleting partition "data0" (-16)
[ 4406.757608] Deleting flash0 MTD partition
[ 4406.854379] Deleting MTD partitions on "spi0.1":
[ 4406.859050] Deleting alt-u-boot MTD partition
[ 4406.945112] Deleting alt-env MTD partition
[ 4407.006212] Deleting alt-fit MTD partition
[ 4407.065440] Deleting alt-data0 MTD partition
[ 4407.104158] Deleting flash1 MTD partition
root@lambda_bmc:/sys/bus/platform/drivers/aspeed-smc# 
root@lambda_bmc:/sys/bus/platform/drivers/aspeed-smc# 
root@lambda_bmc:/sys/bus/platform/drivers/aspeed-smc# echo 1e620000.spi > bind   
[ 4415.421263] aspeed-smc 1e620000.spi: Using bus width 2 and 100 MHz SPI frequency
[ 4415.428854] aspeed-smc 1e620000.spi: matao --test JEDEC id bytes: ef 40 20 00 00 00
[ 4415.436855] aspeed-smc 1e620000.spi: before sr3 value =  1
[ 4415.442550] aspeed-smc 1e620000.spi: set flash drive to 100 percentage success sr3 value =  1
[ 4415.451122] aspeed-smc 1e620000.spi: w25q512jv (65536 Kbytes)
[ 4415.457005] aspeed-smc 1e620000.spi: CE0 window [ 0x20000000 - 0x24000000 ] 64MB
[ 4415.464535] aspeed-smc 1e620000.spi: CE1 window [ 0x24000000 - 0x28000000 ] 64MB
[ 4415.472054] aspeed-smc 1e620000.spi: read control register: 203c0741
[ 4415.703716] 5 fixed-partitions partitions found on MTD device spi0.0
[ 4415.710125] Creating 5 MTD partitions on "spi0.0":
[ 4415.715060] 0x000000000000-0x000000060000 : "u-boot"
[ 4416.942578] 0x000000060000-0x000000080000 : "env"
[ 4416.972602] 0x000000080000-0x000003c00000 : "fit"
[ 4417.022591] 0x000003c00000-0x000004000000 : "data0"
[ 4417.049279] 0x000000000000-0x000004000000 : "flash0"
[ 4417.102559] aspeed-smc 1e620000.spi: Using bus width 2 and 100 MHz SPI frequency
[ 4417.110044] aspeed-smc 1e620000.spi: matao --test JEDEC id bytes: ef 40 20 00 00 00
[ 4417.118055] aspeed-smc 1e620000.spi: before sr3 value =  1
[ 4417.123719] aspeed-smc 1e620000.spi: set flash drive to 100 percentage success sr3 value =  1
[ 4417.132380] aspeed-smc 1e620000.spi: w25q512jv (65536 Kbytes)
[ 4417.138182] aspeed-smc 1e620000.spi: CE1 window [ 0x24000000 - 0x28000000 ] 64MB
[ 4417.145707] aspeed-smc 1e620000.spi: CE2 window [ 0x28000000 - 0x2c000000 ] 64MB
[ 4417.153224] aspeed-smc 1e620000.spi: read control register: 203c0741
[ 4417.393233] 5 fixed-partitions partitions found on MTD device spi0.1
[ 4417.399636] Creating 5 MTD partitions on "spi0.1":
[ 4417.404574] 0x000000000000-0x000000060000 : "alt-u-boot"
[ 4418.858694] 0x000000060000-0x000000080000 : "alt-env"
[ 4418.884335] 0x000000080000-0x000003c00000 : "alt-fit"
[ 4418.995553] 0x000003c00000-0x000004000000 : "alt-data0"
[ 4419.104543] 0x000000000000-0x000004000000 : "flash1"
root@lambda_bmc:/sys/bus/platform/drivers/aspeed-smc# 