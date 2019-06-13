# dpi-pizero-gpicase
Overlay for dpi 3.2 TFT screen specific configuration for gpicase

# Modification of dpi24 for gpicase

GPICase is using a DPI screen. It uses & 18bits color configuration. GPIcase uses GPIO 18 and 19 for audio, so theses GPIOs need to be avoid during 
the configuration using the overlay. So we need to modify the dpi24 overlay as follow : 

```c
brcm,pins = <0 1 2 3 4 5 6 7 8 9 10 11
	     12 13 14 15 16 17 20
	     21 22 23 24 25 26 27>;
```
We keep the GPIOs 18 and 19 unconfigured. They are configured by the specific audio overlay.

With recalbox, the simpler way is to add this line in the `config.txt` file. It will reconfigure the pins at boot without any external software or services. The PWMO will be on the GPIO #18 (pin 12 on the connector), and PWM1 on GPIO #19 (pin 35 on the connector). THIS CONFIGURATION IS SPECIFIC FOR GPI CASE. It uses a DPI TFT screen and its configuration uses RGB 666 Mode 5 see the tablr below :

![dpi-packing.png](http://images.morere.eu/dpi-packing.png)

https://www.raspberrypi.org/documentation/hardware/raspberrypi/dpi/README.md

The TFT screen configuration is the following
```
dpi_output_format=0x6016
```

we have 0x6016 =  110000000010110
Here are the config variables : 

-output_format: DPI_OUTPUT_FORMAT_18BIT_666_CFG2 0110
-rgb_order:   1: DPI_RGB_ORDER_RGB 0010
-output_enable_mode 0
-invert_pixel_clock     0

-hsync_disable          0
-vsync_disable          0
-output_enable_disable  0
-hsync_polarity         1
-vsync_polarity         1
-output_enable_polarity 0

-hsync_phase            0
-vsync_phase            0
-output_enable_phase 0

If you have setup buildroot as described in https://github.com/recalbox/recalbox-os/wiki/Compilation-%26-Modifications-%28EN%29 and already built recalbox, you can compile the dts with :

```bash
output/build/linux-FIRMWARE/scripts/dtc/dtc -@ -I dts -O dtb -o dpi-pizero-gpicase.dtbo dpi-pizero-gpicase.dts
```
with newer kernel you should use : 
```bash
output/build/linux-custom/scripts/dtc/dtc -W no-unit_address_vs_reg -@ -I dts -O dtb -o dpi-pizero-gpicase.dtbo dpi-pizero-gpicase.dts
```
and you copy the dpi-pizero-gpicase.dtbo file in /boot/overlays of your recalbox. The configuration is now simpler : 
```ini
dtoverlay=dpi-pizero-gpicase
```
