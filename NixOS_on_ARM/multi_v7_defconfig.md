---
title: NixOS on ARM multi v7 defconfig
permalink: /NixOS_on_ARM/multi_v7_defconfig/
---

As of kernel 4.2, the `multi_v7_defconfig` configuration target comes with device tree files for these boards:

-   **Acer Chromebook 13 CB5-311:** `tegra124-nyan-big.dtb`
-   **Adapteva Parallella Board:** `zynq-parallella.dtb`
-   **Allwinner A23 Evaluation Board:** `sun8i-a23-evb.dtb`
-   **Allwinner A31 APP4 EVB1 Evaluation Board:** `sun6i-a31-app4-evb1.dtb`
-   **Allwinner GA10H Quad Core Tablet (v1.1):** `sun8i-a33-ga10h-v1.1.dtb`
-   **Altera SOCFPGA Arria 10:** `socfpga_arria10_socdk_sdmmc.dtb`
-   **Altera SOCFPGA Arria V SoC Development Kit:** `socfpga_arria5_socdk.dtb`
-   **Altera SOCFPGA Cyclone V SoC Development Kit:** `socfpga_cyclone5_socdk.dtb`
-   **Altera SOCFPGA VT:** `socfpga_vt.dtb`
-   **Alt:** `r8a7794-alt.dtb`
-   **AM335x Chiliboard:** `am335x-chiliboard.dtb`
-   **Annapurna Labs Alpine Dev Board:** `alpine-db.dtb`
-   **APE6EVM:** `r8a73a4-ape6evm.dtb`
-   **aristainetos2 i.MX6 Dual Lite Board 4:** `imx6dl-aristainetos2_4.dtb`
-   **aristainetos2 i.MX6 Dual Lite Board 7:** `imx6dl-aristainetos2_7.dtb`
-   **aristainetos i.MX6 Dual Lite Board 4:** `imx6dl-aristainetos_4.dtb`
-   **aristainetos i.MX6 Dual Lite Board 7:** `imx6dl-aristainetos_7.dtb`
-   **Armadeus APF6 Quad / Dual Module on APF6Dev Board:** `imx6q-apf6dev.dtb`
-   **Armadeus APF6 Solo Module on APF6Dev Board:** `imx6dl-apf6dev.dtb`
-   **Armadeus Systems APF51Dev docking/development board:** `imx51-apf51dev.dtb`
-   **Armadeus Systems APF51 module:** `imx51-apf51.dtb`
-   **armadillo 800 eva:** `r8a7740-armadillo800eva.dtb`
-   **Asus RT-AC56U (BCM4708):** `bcm4708-asus-rt-ac56u.dtb`
-   **Asus RT-AC68U (BCM4708):** `bcm4708-asus-rt-ac68u.dtb`
-   **Asus RT-AC87U:** `bcm4709-asus-rt-ac87u.dtb`
-   **Asus RT-N18U (BCM47081):** `bcm47081-asus-rt-n18u.dtb`
-   **Atmel SAMA5D31-EK:** `sama5d31ek.dtb`
-   **Atmel SAMA5D33-EK:** `sama5d33ek.dtb`
-   **Atmel SAMA5D34-EK:** `sama5d34ek.dtb`
-   **Atmel SAMA5D35-EK:** `sama5d35ek.dtb`
-   **Atmel SAMA5D36-EK:** `sama5d36ek.dtb`
-   **Atmel SAMA5D4-EK:** `at91-sama5d4ek.dtb`
-   **Atmel SAMA5D4 Xplained:** `at91-sama5d4_xplained.dtb`
-   **Auxtek t004 A10s hdmi tv-stick:** `sun5i-a10s-auxtek-t004.dtb`
-   **Avionic Design Medcom-Wide board:** `tegra20-medcom-wide.dtb`
-   **Avionic Design Plutux board:** `tegra20-plutux.dtb`
-   **Avionic Design Tamonten Evaluation Carrier:** `tegra20-tec.dtb`
-   **BA10 tvbox:** `sun4i-a10-ba10-tvbox.dtb`
-   **BCM21664 Garnet board:** `bcm21664-garnet.dtb`
-   **BCM28155 AP board:** `bcm28155-ap.dtb`
-   **Benign BV07 Netbook:** `vt8500-bv07.dtb`
-   **bockw:** `r8a7778-bockw.dtb`
-   **bq Aquaris5:** `mt6589-aquaris5.dtb`
-   **bq Curie 2:** `rk3066a-bqcurie2.dtb`
-   **Broadcom STB (bcm7445), SVMB reference board:** `bcm7445-bcm97445svmb.dtb`
-   **Buffalo WXR-1900DHP:** `bcm4709-buffalo-wxr-1900dhp.dtb`
-   **Buffalo WZR-1750DHP (BCM4708):** `bcm4708-buffalo-wzr-1750dhp.dtb`
-   **Buffalo WZR-600DHP2 (BCM47081):** `bcm47081-buffalo-wzr-600dhp2.dtb`
-   **Buffalo WZR-900DHP (BCM47081):** `bcm47081-buffalo-wzr-900dhp.dtb`
-   **Calao Systems Snowball platform with device tree:** `ste-snowball.dtb`
-   **Calxeda ECX-2000:** `ecx-2000.dtb`
-   **Calxeda Highbank:** `highbank.dtb`
-   **Chuwi V7 CW0825:** `sun4i-a10-chuwi-v7-cw0825.dtb`
-   **CompuLab CM-FX6:** `imx6q-cm-fx6.dtb`
-   **CompuLab CM-QS600:** `qcom-apq8064-cm-qs600.dtb`
-   **CompuLab CM-T3517:** `omap3-cm-t3517.dtb`
-   **CompuLab CM-T3530:** `omap3-cm-t3530.dtb`
-   **CompuLab CM-T3730:** `omap3-cm-t3730.dtb`
-   **CompuLab CM-T54 on SB-T54:** `omap5-sbc-t54.dtb`
-   **CompuLab CM-T54:** `omap5-cm-t54.dtb`
-   **Compulab SBC-A510:** `dove-sbc-a510.dtb`
-   **CompuLab SBC-T3517 with CM-T3517:** `omap3-sbc-t3517.dtb`
-   **CompuLab SBC-T3530 with CM-T3530:** `omap3-sbc-t3530.dtb`
-   **CompuLab SBC-T3730 with CM-T3730:** `omap3-sbc-t3730.dtb`
-   **Compulab TrimSlice board:** `tegra20-trimslice.dtb`
-   **Conexant Equinox CX92755 EVK:** `cx92755_equinox.dtb`
-   **CSQ CS908 top set box:** `sun6i-a31s-cs908.dtb`
-   **CSR SiRFatlas6 Evaluation Board:** `atlas6-evb.dtb`
-   **CSR SiRFatlas7 Evaluation Board:** `atlas7-evb.dtb`
-   **CSR SiRFprimaII Evaluation Board:** `prima2-evb.dtb`
-   **Cubietech Cubieboard2:** `sun7i-a20-cubieboard2.dtb`
-   **Cubietech Cubieboard4:** `sun9i-a80-cubieboard4.dtb`
-   **Cubietech Cubieboard:** `sun4i-a10-cubieboard.dtb`
-   **Cubietech Cubietruck:** `sun7i-a20-cubietruck.dtb`
-   **Cygnus Enterprise Phone (BCM911360_ENTPHN):** `bcm911360_entphn.dtb`
-   **Cygnus SVK (BCM911360K):** `bcm911360k.dtb`
-   **Cygnus SVK (BCM958300K):** `bcm958300k.dtb`
-   **Cygnus Wireless Audio (BCM958305K):** `bcm958305k.dtb`
-   **Data Modul eDM-QMX6 Board:** `imx6q-dmo-edmqmx6.dtb`
-   **DENX M53EVK:** `imx53-m53evk.dtb`
-   **DFI FS700-M60-6DL i.MX6dl Q7 Board:** `imx6dl-dfi-fs700-m60.dtb`
-   **DFI FS700-M60-6QD i.MX6qd Q7 Board:** `imx6q-dfi-fs700-m60.dtb`
-   **Digi ConnectCore CC(W)-MX51 JSK:** `imx51-digi-connectcore-jsk.dtb`
-   **D-Link DNS-327L:** `armada-370-dlink-dns327l.dtb`
-   **DM8168 EVM:** `dm8168-evm.dtb`
-   **EBV SOCrates:** `socfpga_cyclone5_socrates.dtb`
-   **EMEV2 KZM9D Board:** `emev2-kzm9d.dtb`
-   **ET Q8 Quad Core Tablet (v1.6):** `sun8i-a33-et-q8-v1.6.dtb`
-   **Eukrea CPUIMX51:** `imx51-eukrea-mbimxsd51-baseboard.dtb`
-   **Firefly-RK3288 Beta:** `rk3288-firefly-beta.dtb`
-   **Firefly-RK3288:** `rk3288-firefly.dtb`
-   **Freescale i.MX50 Evaluation Kit:** `imx50-evk.dtb`
-   **Freescale i.MX51 Babbage Board:** `imx51-babbage.dtb`
-   **Freescale i.MX53 Automotive Reference Design Board:** `imx53-ard.dtb`
-   **Freescale i.MX53 Quick Start Board:** `imx53-qsb.dtb`
-   **Freescale i.MX53 Quick Start-R Board:** `imx53-qsrb.dtb`
-   **Freescale i.MX53 Smart Mobile Reference Design Board:** `imx53-smd.dtb`
-   **Freescale i.MX6 DualLite Nitrogen6x Board:** `imx6dl-nitrogen6x.dtb`
-   **Freescale i.MX6 DualLite SABRE Lite Board:** `imx6dl-sabrelite.dtb`
-   **Freescale i.MX6 DualLite SABRE Smart Device Board:** `imx6dl-sabresd.dtb`
-   **Freescale i.MX6 DualLite/Solo SABRE Automotive Board:** `imx6dl-sabreauto.dtb`
-   **Freescale i.MX6 Quad Armadillo2 Board:** `imx6q-arm2.dtb`
-   **Freescale i.MX6 Quad Nitrogen6x Board:** `imx6q-nitrogen6x.dtb`
-   **Freescale i.MX6 Quad SABRE Automotive Board:** `imx6q-sabreauto.dtb`
-   **Freescale i.MX6 Quad SABRE Lite Board:** `imx6q-sabrelite.dtb`
-   **Freescale i.MX6 Quad SABRE Smart Device Board:** `imx6q-sabresd.dtb`
-   **Freescale i.MX6 SoloLite EVK Board:** `imx6sl-evk.dtb`
-   **Freescale i.MX6 SoloX Sabre Auto Board:** `imx6sx-sabreauto.dtb`
-   **Freescale i.MX6 SoloX SDB RevA Board:** `imx6sx-sdb-reva.dtb`
-   **Freescale i.MX6 SoloX SDB RevB Board:** `imx6sx-sdb.dtb`
-   **FriendlyARM TINY4412 board based on Exynos4412:** `exynos4412-tiny4412.dtb`
-   **Gateworks Ventana GW5400-A:** `imx6q-gw5400-a.dtb`
-   **Gateworks Ventana i.MX6 DualLite/Solo GW51XX:** `imx6dl-gw51xx.dtb`
-   **Gateworks Ventana i.MX6 DualLite/Solo GW52XX:** `imx6dl-gw52xx.dtb`
-   **Gateworks Ventana i.MX6 DualLite/Solo GW53XX:** `imx6dl-gw53xx.dtb`
-   **Gateworks Ventana i.MX6 DualLite/Solo GW54XX:** `imx6dl-gw54xx.dtb`
-   **Gateworks Ventana i.MX6 DualLite/Solo GW551X:** `imx6dl-gw551x.dtb`
-   **Gateworks Ventana i.MX6 DualLite/Solo GW552X:** `imx6dl-gw552x.dtb`
-   **Gateworks Ventana i.MX6 Dual/Quad GW51XX:** `imx6q-gw51xx.dtb`
-   **Gateworks Ventana i.MX6 Dual/Quad GW52XX:** `imx6q-gw52xx.dtb`
-   **Gateworks Ventana i.MX6 Dual/Quad GW53XX:** `imx6q-gw53xx.dtb`
-   **Gateworks Ventana i.MX6 Dual/Quad GW54XX:** `imx6q-gw54xx.dtb`
-   **Gateworks Ventana i.MX6 Dual/Quad GW551X:** `imx6q-gw551x.dtb`
-   **Gateworks Ventana i.MX6 Dual/Quad GW552X:** `imx6q-gw552x.dtb`
-   **Gemei G9 Tablet:** `sun4i-a10-gemei-g9.dtb`
-   **Geniatech ATV1200:** `meson6-atv1200.dtb`
-   **Genmai:** `r7s72100-genmai.dtb`
-   **Globalscale D2Plug:** `dove-d2plug.dtb`
-   **Globalscale D3Plug:** `dove-d3plug.dtb`
-   **Globalscale Mirabox:** `armada-370-mirabox.dtb`
-   **Goldelico GTA04A3:** `omap3-gta04a3.dtb`
-   **Goldelico GTA04A4:** `omap3-gta04a4.dtb`
-   **Goldelico GTA04A5:** `omap3-gta04a5.dtb`
-   **Google Chromecast:** `berlin2cd-google-chromecast.dtb`
-   **Google Peach Pi Rev 10+:** `exynos5800-peach-pi.dtb`
-   **Google Peach Pit Rev 6+:** `exynos5420-peach-pit.dtb`
-   **Google Snow:** `exynos5250-snow.dtb`
-   **Google Spring:** `exynos5250-spring.dtb`
-   **Gumstix Pepper:** `am335x-pepper.dtb`
-   **HAOYU Electronics Marsboard A10:** `sun4i-a10-marsboard.dtb`
-   **Hardkernel ODROID-U3 board based on Exynos4412:** `exynos4412-odroidu3.dtb`
-   **Hardkernel ODROID-X2 board based on Exynos4412:** `exynos4412-odroidx2.dtb`
-   **Hardkernel ODROID-X board based on Exynos4412:** `exynos4412-odroidx.dtb`
-   **Hardkernel Odroid XU3 Lite:** `exynos5422-odroidxu3-lite.dtb`
-   **Hardkernel Odroid XU3:** `exynos5422-odroidxu3.dtb`
-   **Henninger:** `r8a7791-henninger.dtb`
-   **Hisilicon D01 Development Board:** `hip04-d01.dtb`
-   **Hisilicon Hi4511 Development Board:** `hi3620-hi4511.dtb`
-   **Hisilicon HIP01 Development Board:** `hip01-ca9x2.dtb`
-   **Hisilicon HIX5HD2 Development Board:** `hisi-x5hd2-dkb.dtb`
-   **HP Chromebook 14:** `tegra124-nyan-blaze.dtb`
-   **HSG H702:** `sun5i-a13-hsg-h702.dtb`
-   **Hyundai A7HD:** `sun4i-a10-hyundai-a7hd.dtb`
-   **I12 / Q5 / QT840A A20 tvbox:** `sun7i-a20-i12-tvbox.dtb`
-   **IGEP COM AM335x on AQUILA Expansion:** `am335x-base0033.dtb`
-   **IGEP COM MODULE Rev. E (TI OMAP AM/DM37x):** `omap3-igep0030.dtb`
-   **IGEP COM MODULE Rev. G (TI OMAP AM/DM37x):** `omap3-igep0030-rev-g.dtb`
-   **IGEPv2 Rev. C (TI OMAP AM/DM37x):** `omap3-igep0020.dtb`
-   **IGEPv2 Rev. F (TI OMAP AM/DM37x):** `omap3-igep0020-rev-f.dtb`
-   **INCOstartec LILLY-DBB056 (DM3730):** `omap3-lilly-dbb056.dtb`
-   **INet-97F Rev 02:** `sun4i-a10-inet97fv2.dtb`
-   **Insignal Arndale evaluation board based on EXYNOS5250:** `exynos5250-arndale.dtb`
-   **Insignal Arndale Octa evaluation board based on EXYNOS5420:** `exynos5420-arndale-octa.dtb`
-   **Insignal Origen evaluation board based on Exynos4210:** `exynos4210-origen.dtb`
-   **Insignal Origen evaluation board based on Exynos4412:** `exynos4412-origen.dtb`
-   **Ippo Q8H Dual Core Tablet (v1.2):** `sun8i-a23-ippo-q8h-v1.2.dtb`
-   **Ippo Q8H Dual Core Tablet (v5):** `sun8i-a23-ippo-q8h-v5.dtb`
-   **Jesurun Q5:** `sun4i-a10-jesurun-q5.dtb`
-   **Ka-Ro electronics TX53 module (LCD):** `imx53-tx53-x03x.dtb`
-   **Ka-Ro electronics TX53 module (LVDS):** `imx53-tx53-x13x.dtb`
-   **Ka-Ro electronics TX6DL Module on CoMpact TFT:** `imx6dl-tx6dl-comtft.dtb`
-   **Ka-Ro electronics TX6Q-1010 Module on CoMpact TFT:** `imx6q-tx6q-1010-comtft.dtb`
-   **Ka-Ro electronics TX6Q-1010 Module:** `imx6q-tx6q-1010.dtb`
-   **Ka-Ro electronics TX6Q-1020 Module on CoMpact TFT:** `imx6q-tx6q-1020-comtft.dtb`
-   **Ka-Ro electronics TX6Q-1020 Module:** `imx6q-tx6q-1020.dtb`
-   **Ka-Ro electronics TX6Q-1110 Module:** `imx6q-tx6q-1110.dtb`
-   **Ka-Ro electronics TX6U-801x Module:** `imx6dl-tx6u-801x.dtb`
-   **Ka-Ro electronics TX6U-811x Module:** `imx6dl-tx6u-811x.dtb`
-   **Koelsch:** `r8a7791-koelsch.dtb`
-   **KZM-A9-GT:** `sh73a0-kzm9g.dtb`
-   **Lager:** `r8a7790-lager.dtb`
-   **LeMaker Banana Pi:** `sun7i-a20-bananapi.dtb`
-   **LeMaker Banana Pro:** `sun7i-a20-bananapro.dtb`
-   **Lenovo Iomega ix4-300d:** `armada-xp-lenovo-ix4-300d.dtb`
-   **LinkSprite pcDuino3 Nano:** `sun7i-a20-pcduino3-nano.dtb`
-   **LinkSprite pcDuino3:** `sun7i-a20-pcduino3.dtb`
-   **LinkSprite pcDuino:** `sun4i-a10-pcduino.dtb`
-   **Linksys WRT1200AC:** `armada-385-linksys-caiman.dtb`
-   **Linksys WRT1900AC:** `armada-xp-linksys-mamba.dtb`
-   **Linksys WRT1900ACv2:** `armada-385-linksys-cobra.dtb`
-   **LogicPD Zoom DM3730 Torpedo Development Kit:** `logicpd-torpedo-37xx-devkit.dtb`
-   **LS1021A QDS Board:** `ls1021a-qds.dtb`
-   **LS1021A TWR Board:** `ls1021a-twr.dtb`
-   **Luxul XWC-1000 (BCM4708):** `bcm4708-luxul-xwc-1000.dtb`
-   **MarsBoard RK3066:** `rk3066a-marsboard.dtb`
-   **Marvell Armada 370 Evaluation Board:** `armada-370-db.dtb`
-   **Marvell Armada 370 Reference Design:** `armada-370-rd.dtb`
-   **Marvell Armada 375 Development Board:** `armada-375-db.dtb`
-   **Marvell Armada 385 Access Point Development Board:** `armada-385-db-ap.dtb`
-   **Marvell Armada 385 Development Board:** `armada-388-db.dtb`
-   **Marvell Armada 385 GP:** `armada-388-gp.dtb`
-   **Marvell Armada 385 Reference Design:** `armada-388-rd.dtb`
-   **Marvell Armada 398 Development Board:** `armada-398-db.dtb`
-   **Marvell Armada XP Development Board DB-MV784MP-GP:** `armada-xp-gp.dtb`
-   **Marvell Armada XP Evaluation Board:** `armada-xp-db.dtb`
-   **Marvell Armada XP Matrix Board:** `armada-xp-matrix.dtb`
-   **Marvell BG2-Q DMP:** `berlin2q-marvell-dmp.dtb`
-   **Marvell DB-MV88AP510-BP Development Board:** `dove-dove-db.dtb`
-   **Marvell RD-AXPWiFiAP:** `armada-xp-axpwifiap.dtb`
-   **marzen:** `r8a7779-marzen.dtb`
-   **MediaTek MT8127 Moose Board:** `mt8127-moose.dtb`
-   **MediaTek MT8135 evaluation board:** `mt8135-evbp1.dtb`
-   **Mele A1000G Quad top set box:** `sun6i-a31-mele-a1000g-quad.dtb`
-   **Mele A1000:** `sun4i-a10-a1000.dtb`
-   **Mele I7 Quad top set box:** `sun6i-a31-i7.dtb`
-   **Mele M3:** `sun7i-a20-m3.dtb`
-   **Mele M9 top set box:** `sun6i-a31-m9.dtb`
-   **Merrii A20 Hummingbird:** `sun7i-a20-hummingbird.dtb`
-   **Merrii A31 Hummingbird:** `sun6i-a31-hummingbird.dtb`
-   **Merrii A80 Optimus Board:** `sun9i-a80-optimus.dtb`
-   **MicroSys sbc6x board:** `imx6q-sbc6x.dtb`
-   **Miniand Hackberry:** `sun4i-a10-hackberry.dtb`
-   **MINIX NEO-X8:** `meson8-minix-neo-x8.dtb`
-   **MK802-A10s:** `sun5i-a10s-mk802.dtb`
-   **MK802ii:** `sun4i-a10-mk802ii.dtb`
-   **MK802:** `sun4i-a10-mk802.dtb`
-   **mk808c:** `sun7i-a20-mk808c.dtb`
-   **mt6592 evb:** `mt6592-evb.dtb`
-   **Netgear R6250 V1 (BCM4708):** `bcm4708-netgear-r6250.dtb`
-   **Netgear R6300 V2 (BCM4708):** `bcm4708-netgear-r6300-v2.dtb`
-   **Netgear R8000 (BCM4709):** `bcm4709-netgear-r8000.dtb`
-   **NETGEAR ReadyNAS 102:** `armada-370-netgear-rn102.dtb`
-   **NETGEAR ReadyNAS 104:** `armada-370-netgear-rn104.dtb`
-   **NETGEAR ReadyNAS 2120:** `armada-xp-netgear-rn2120.dtb`
-   **Newflow AM335x NanoBone:** `am335x-nano.dtb`
-   **Nokia N900:** `omap3-n900.dtb`
-   **Nokia N950:** `omap3-n950.dtb`
-   **Nokia N9:** `omap3-n9.dtb`
-   **NovaTech OrionLXm:** `am335x-lxm.dtb`
-   **NVIDIA Seaboard:** `tegra20-seaboard.dtb`
-   **NVIDIA SHIELD:** `tegra114-roth.dtb`
-   **NVIDIA Tegra114 Dalmore evaluation board:** `tegra114-dalmore.dtb`
-   **NVIDIA Tegra124 Jetson TK1:** `tegra124-jetson-tk1.dtb`
-   **NVIDIA Tegra124 Venice2:** `tegra124-venice2.dtb`
-   **NVIDIA Tegra20 Harmony evaluation board:** `tegra20-harmony.dtb`
-   **NVIDIA Tegra20 Ventana evaluation board:** `tegra20-ventana.dtb`
-   **NVIDIA Tegra20 Whistler evaluation board:** `tegra20-whistler.dtb`
-   **NVIDIA Tegra30 Beaver evaluation board:** `tegra30-beaver.dtb`
-   **NVIDIA Tegra30 Cardhu A02 evaluation board:** `tegra30-cardhu-a02.dtb`
-   **NVIDIA Tegra30 Cardhu A04 (A05, A06, A07) evaluation board:** `tegra30-cardhu-a04.dtb`
-   **Olimex A10-OLinuXino-LIME:** `sun4i-a10-olinuxino-lime.dtb`
-   **Olimex A10s-Olinuxino Micro:** `sun5i-a10s-olinuxino-micro.dtb`
-   **Olimex A13-Olinuxino Micro:** `sun5i-a13-olinuxino-micro.dtb`
-   **Olimex A13-Olinuxino:** `sun5i-a13-olinuxino.dtb`
-   **Olimex A20-OLinuXino-LIME2:** `sun7i-a20-olinuxino-lime2.dtb`
-   **Olimex A20-OLinuXino-LIME:** `sun7i-a20-olinuxino-lime.dtb`
-   **Olimex A20-Olinuxino Micro:** `sun7i-a20-olinuxino-micro.dtb`
-   **OMAP35xx Gumstix Overo on Alto35:** `omap3-overo-alto35.dtb`
-   **OMAP35xx Gumstix Overo on Chestnut43:** `omap3-overo-chestnut43.dtb`
-   **OMAP35xx Gumstix Overo on Gallop43:** `omap3-overo-gallop43.dtb`
-   **OMAP35xx Gumstix Overo on Palo43:** `omap3-overo-palo43.dtb`
-   **OMAP35xx Gumstix Overo on Summit:** `omap3-overo-summit.dtb`
-   **OMAP35xx Gumstix Overo on Tobi:** `omap3-overo-tobi.dtb`
-   **OMAP36xx/AM37xx/DM37xx Gumstix Overo on Alto35:** `omap3-overo-storm-alto35.dtb`
-   **OMAP36xx/AM37xx/DM37xx Gumstix Overo on Chestnut43:** `omap3-overo-storm-chestnut43.dtb`
-   **OMAP36xx/AM37xx/DM37xx Gumstix Overo on Gallop43:** `omap3-overo-storm-gallop43.dtb`
-   **OMAP36xx/AM37xx/DM37xx Gumstix Overo on Palo43:** `omap3-overo-storm-palo43.dtb`
-   **OMAP36xx/AM37xx/DM37xx Gumstix Overo on Summit:** `omap3-overo-storm-summit.dtb`
-   **OMAP36xx/AM37xx/DM37xx Gumstix Overo on Tobi:** `omap3-overo-storm-tobi.dtb`
-   **OMAP4430 Gumstix Duovero on Parlor:** `omap4-duovero-parlor.dtb`
-   **OnRISC Baltos iR 5221:** `am335x-baltos-ir5221.dtb`
-   **Orange Pi Mini:** `sun7i-a20-orangepi-mini.dtb`
-   **Orange Pi:** `sun7i-a20-orangepi.dtb`
-   **Overkiz Kizbox 2:** `at91-kizbox2.dtb`
-   **Pandora Handheld Console 1GHz:** `omap3-pandora-1ghz.dtb`
-   **Pandora Handheld Console:** `omap3-pandora-600mhz.dtb`
-   **PHYTEC Cosmic/Cosmic+ Board:** `vf610-cosmic.dtb`
-   **Phytec phyFLEX-i.MX6 DualLite/Solo Carrier-Board:** `imx6dl-phytec-pbab01.dtb`
-   **Phytec phyFLEX-i.MX6 Quad Carrier-Board:** `imx6q-phytec-pbab01.dtb`
-   **PineRiver Mini X-Plus:** `sun4i-a10-mini-xplus.dtb`
-   **PlatHome OpenBlocks AX3-4 board:** `armada-xp-openblocks-ax3-4.dtb`
-   **Qualcomm APQ8064/IFC6410:** `qcom-apq8064-ifc6410.dtb`
-   **Qualcomm APQ8074 Dragonboard:** `qcom-apq8074-dragonboard.dtb`
-   **Qualcomm APQ8084/IFC6540:** `qcom-apq8084-ifc6540.dtb`
-   **Qualcomm APQ 8084-MTP:** `qcom-apq8084-mtp.dtb`
-   **Qualcomm IPQ8064/AP148:** `qcom-ipq8064-ap148.dtb`
-   **Qualcomm MSM8660 SURF:** `qcom-msm8660-surf.dtb`
-   **Qualcomm MSM8960 CDP:** `qcom-msm8960-cdp.dtb`
-   **R7 A10s hdmi tv-stick:** `sun5i-a10s-r7-tv-dongle.dtb`
-   **Radxa Rock:** `rk3188-radxarock.dtb`
-   **Rayeager PX2:** `rk3066a-rayeager.dtb`
-   **Rex Basic i.MX6 Dual Lite Board:** `imx6dl-rex-basic.dtb`
-   **Rex Pro i.MX6 Quad Board:** `imx6q-rex-pro.dtb`
-   **RIoTboard i.MX6S:** `imx6dl-riotboard.dtb`
-   **SAMA5D3 Xplained:** `at91-sama5d3_xplained.dtb`
-   **SAMSUNG SD5v1 board based on EXYNOS5440:** `exynos5440-sd5v1.dtb`
-   **SAMSUNG SMDK5250 board based on EXYNOS5250:** `exynos5250-smdk5250.dtb`
-   **Samsung SMDK5410 board based on EXYNOS5410:** `exynos5410-smdk5410.dtb`
-   **Samsung SMDK5420 board based on EXYNOS5420:** `exynos5420-smdk5420.dtb`
-   **Samsung SMDK evaluation board based on Exynos4412:** `exynos4412-smdk4412.dtb`
-   **Samsung smdkv310 evaluation board based on Exynos4210:** `exynos4210-smdkv310.dtb`
-   **SAMSUNG SSDK5440 board based on EXYNOS5440:** `exynos5440-ssdk5440.dtb`
-   **Samsung Trats 2 based on Exynos4412:** `exynos4412-trats2.dtb`
-   **Samsung Trats based on Exynos4210:** `exynos4210-trats.dtb`
-   **Samsung Universal C210 based on Exynos4210 rev0:** `exynos4210-universal_c210.dtb`
-   **SAMSUNG XYREF5260 board based on EXYNOS5260:** `exynos5260-xyref5260.dtb`
-   **Sinlinx SinA33:** `sun8i-a33-sinlinx-sina33.dtb`
-   **SmartRG SR400ac:** `bcm4708-smartrg-sr400ac.dtb`
-   **SolidRun CuBox (Engineering Sample):** `dove-cubox-es.dtb`
-   **SolidRun Cubox-i Dual/Quad:** `imx6q-cubox-i.dtb`
-   **SolidRun Cubox-i Solo/DualLite:** `imx6dl-cubox-i.dtb`
-   **SolidRun CuBox:** `dove-cubox.dtb`
-   **SolidRun HummingBoard Dual/Quad:** `imx6q-hummingboard.dtb`
-   **SolidRun HummingBoard Solo/DualLite:** `imx6dl-hummingboard.dtb`
-   **Sony NSZ-GS7:** `berlin2-sony-nsz-gs7.dtb`
-   **Sony Xperia Z1:** `qcom-msm8974-sony-xperia-honami.dtb`
-   **ST-Ericsson CCU9540 platform with Device Tree:** `ste-ccu9540.dtb`
-   **ST-Ericsson HREF (pre-v60) and ST UIB:** `ste-hrefprev60-stuib.dtb`
-   **ST-Ericsson HREF (pre-v60) and TVK1281618 UIB:** `ste-hrefprev60-tvk.dtb`
-   **ST-Ericsson HREF (v60+) and ST UIB:** `ste-hrefv60plus-stuib.dtb`
-   **ST-Ericsson HREF (v60+) and TVK1281618 UIB:** `ste-hrefv60plus-tvk.dtb`
-   **ST-Ericsson U8540 platform with Device Tree:** `ste-ccu8540.dtb`
-   **STiH407 B2120:** `stih407-b2120.dtb`
-   **STiH410 B2120:** `stih410-b2120.dtb`
-   **STiH415 B2000 Board:** `stih415-b2000.dtb`
-   **STiH415 B2020 Board:** `stih415-b2020.dtb`
-   **STiH416 B2000:** `stih416-b2000.dtb`
-   **STiH416 B2020 REV-E:** `stih416-b2020e.dtb`
-   **STiH416 B2020:** `stih416-b2020.dtb`
-   **STiH418 B2199:** `stih418-b2199.dtb`
-   **ST SPEAr1310 Evaluation Board:** `spear1310-evb.dtb`
-   **ST SPEAr1340 Evaluation Board:** `spear1340-evb.dtb`
-   **Synology DS213j:** `armada-370-synology-ds213j.dtb`
-   **Synology DS414:** `armada-xp-synology-ds414.dtb`
-   **TBS2910 Matrix ARM mini PC:** `imx6q-tbs2910.dtb`
-   **TeeJet Mt.Ventoux:** `am3517_mt_ventoux.dtb`
-   **Tegra Note 7:** `tegra114-tn7.dtb`
-   **Terasic SoCkit:** `socfpga_cyclone5_sockit.dtb`
-   **Texas Instruments Keystone 2 Edison EVM:** `k2e-evm.dtb`
-   **Texas Instruments Keystone 2 Kepler/Hawking EVM:** `k2hk-evm.dtb`
-   **Texas Instruments Keystone 2 Lamarr EVM:** `k2l-evm.dtb`
-   **TI AM335x BeagleBone Black:** `am335x-boneblack.dtb`
-   **TI AM335x BeagleBone:** `am335x-bone.dtb`
-   **TI AM335x EVM-SK:** `am335x-evmsk.dtb`
-   **TI AM335x EVM:** `am335x-evm.dtb`
-   **TI AM3517 CraneBoard (TMDSEVM3517):** `am3517-craneboard.dtb`
-   **TI AM3517 EVM (AM3517/05 TMDSEVM3517):** `am3517-evm.dtb`
-   **TI AM437x GP EVM:** `am437x-gp-evm.dtb`
-   **TI AM437x Industrial Development Kit:** `am437x-idk-evm.dtb`
-   **TI AM437x SK EVM:** `am437x-sk-evm.dtb`
-   **TI AM43x EPOS EVM:** `am43x-epos-evm.dtb`
-   **TI AM5728 BeagleBoard-X15:** `am57xx-beagle-x15.dtb`
-   **TI DRA722:** `dra72-evm.dtb`
-   **TI DRA742:** `dra7-evm.dtb`
-   **TimLL OMAP3 Devkit8000:** `omap3-devkit8000.dtb`
-   **TI OMAP3430 LDP (Zoom1 Labrador):** `omap3-ldp.dtb`
-   **TI OMAP3430 SDP:** `omap3430-sdp.dtb`
-   **TI OMAP35XX EVM (TMDSEVM3530):** `omap3-evm.dtb`
-   **TI OMAP37XX EVM (TMDSEVM3730):** `omap3-evm-37xx.dtb`
-   **TI OMAP3 BeagleBoard:** `omap3-beagle.dtb`
-   **TI OMAP3 BeagleBoard xM:** `omap3-beagle-xm-ab.dtb`
-   **TI OMAP3 BeagleBoard xM:** `omap3-beagle-xm.dtb`
-   **TI OMAP3 HEAD acoustics baseboard with TAO3530 SOM:** `omap3-ha.dtb`
-   **TI OMAP3 HEAD acoustics LCD-baseboard with TAO3530 SOM:** `omap3-ha-lcd.dtb`
-   **TI OMAP3 Thunder baseboard with TAO3530 SOM:** `omap3-thunder.dtb`
-   **TI OMAP4 PandaBoard-ES:** `omap4-panda-es.dtb`
-   **TI OMAP4 PandaBoard:** `omap4-panda.dtb`
-   **TI OMAP4 SDP board:** `omap4-sdp.dtb`
-   **TI OMAP4 SDP board:** `omap4-sdp-es23plus.dtb`
-   **TI OMAP5 uEVM board:** `omap5-uevm.dtb`
-   **TI Zoom3:** `omap3-zoom3.dtb`
-   **Toby Churchill SL50 Series:** `am335x-sl50.dtb`
-   **Toradex Apalis T30 on Apalis Evaluation Board:** `tegra30-apalis-eval.dtb`
-   **Toradex Colibri T20 512MB on Iris:** `tegra20-iris-512.dtb`
-   **Toradex Colibri T30 on Colibri Evaluation Board:** `tegra30-colibri-eval-v3.dtb`
-   **Toradex Colibri VF50 on Colibri Evaluation Board:** `vf500-colibri-eval-v3.dtb`
-   **Toradex Colibri VF61 on Colibri Evaluation Board:** `vf610-colibri-eval-v3.dtb`
-   **Toshiba AC100 / Dynabook AZ:** `tegra20-paz00.dtb`
-   **TQ MBa53 starter kit:** `imx53-mba53.dtb`
-   **???:** `omap4-panda-a4.dtb`
-   **???:** `rk3288-evb-act8846.dtb`
-   **???:** `rk3288-evb-rk808.dtb`
-   **Udoo i.MX6 Dual-lite Board:** `imx6dl-udoo.dtb`
-   **Udoo i.MX6 Quad Board:** `imx6q-udoo.dtb`
-   **UniPhier PH1-LD4 Reference Board:** `uniphier-ph1-ld4-ref.dtb`
-   **UniPhier PH1-Pro4 Reference Board:** `uniphier-ph1-pro4-ref.dtb`
-   **UniPhier PH1-sLD3 Reference Board:** `uniphier-ph1-sld3-ref.dtb`
-   **UniPhier PH1-sLD8 Reference Board:** `uniphier-ph1-sld8-ref.dtb`
-   **Utoo P66:** `sun5i-a13-utoo-p66.dtb`
-   **V2P-CA15_CA7:** `vexpress-v2p-ca15_a7.dtb`
-   **V2P-CA15:** `vexpress-v2p-ca15-tc1.dtb`
-   **V2P-CA5s:** `vexpress-v2p-ca5s.dtb`
-   **V2P-CA9:** `vexpress-v2p-ca9.dtb`
-   **Variscite VAR-DVK-OM44:** `omap4-var-dvk-om44.dtb`
-   **Variscite VAR-STK-OM44:** `omap4-var-stk-om44.dtb`
-   **VF610 Cortex-M4:** `vf610m4-colibri.dtb`
-   **VF610 Tower Board:** `vf610-twr.dtb`
-   **VIA APC8750:** `wm8750-apc8750.dtb`
-   **Voipac i.MX53 X53-DMM-668:** `imx53-voipac-bsb.dtb`
-   **Wandboard i.MX6 Dual Lite Board:** `imx6dl-wandboard.dtb`
-   **Wandboard i.MX6 Dual Lite Board:** `imx6dl-wandboard-revb1.dtb`
-   **Wandboard i.MX6 Quad Board:** `imx6q-wandboard.dtb`
-   **Wandboard i.MX6 Quad Board:** `imx6q-wandboard-revb1.dtb`
-   **WaRP Board:** `imx6sl-warp.dtb`
-   **Wexler TAB7200:** `sun7i-a20-wexler-tab7200.dtb`
-   **WITS A31 Colombus Evaluation Board:** `sun6i-a31-colombus.dtb`
-   **Wondermedia WM8505 Netbook:** `wm8505-ref.dtb`
-   **Wondermedia WM8650-MID Tablet:** `wm8650-mid.dtb`
-   **Wondermedia WM8850-W70v2 Tablet:** `wm8850-w70v2.dtb`
-   **XENVM-4.2:** `xenvm-4.2.dtb`
-   **Zealz GK802:** `imx6q-gk802.dtb`
-   **Zynq ZC702 Development Board:** `zynq-zc702.dtb`
-   **Zynq ZC706 Development Board:** `zynq-zc706.dtb`
-   **Zynq Zed Development Board:** `zynq-zed.dtb`
-   **Zynq ZYBO Development Board:** `zynq-zybo.dtb`