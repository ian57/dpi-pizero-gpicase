# dpi-pizero-gpicase
Overlay for dpi 3.2 TFT screen specific configuration for gpicase

# GPIcase uses GPIO 12 and 13 fot audio

With recalbox, the simpler way is to add this line in the `config.txt` file. It will reconfigure the pins at boot without any external software or services. The PWMO will be on the GPIO #18 (pin 12 on the connector), and PWM1 on GPIO #19 (pin 35 on the connector). THIS CONFIGURATION IS SPECIFIC FOR GPI CASE. It uses a DPI TFT screen and its configuration uses RGB 666 Mode 5 see the tablr below :

![dpi-packing.png](http://images.morere.eu/dpi-packing.png)

```ini
dtoverlay=pwm-2chan,pin=12,func=2,pin2=13,func2=2
```

As described in the page https://hackaday.io/project/9467-piboy-zero/log/35090-pi-zero-pwm-audio-device-tree-overlay, you can make you own overlay with the following source code


```c
/dts-v1/;
/plugin/;

/ {
  compatible = "brcm,bcm2708";

  fragment@0 {
    target = <&gpio>;
    __overlay__ {
      pinctrl-names = "default";
      pinctrl-0 = <&pwm_audio_pins>;

    pwm_audio_pins: pwm_audio_pins {
	brcm,pins = <12 13>;   /* gpio no ('BCM' number) */
	brcm,function = <2 2>; /* 0:in, 1:out, 2: alt5, 3: alt4, 4: alt0, 5: alt1, 6: alt2, 7: alt3 */
	brcm,pull = <0 0>;     /* 2:up 1:down 0:none */
      };
    };
  };
 };
```

With recalbox, the simpler way is to add this line in the `config.txt` file. It will reconfigure the pins at boot without any external software or services. The PWMO will be on the GPIO #18 (pin 12 on the connector), and PWM1 on GPIO #19 (pin 35 on the connector). THIS CONFIGURATION IS SPECIFIC FOR GPI CASE. It uses a DPI TFT screen and its configuration uses RGB 666 Mode 5 see the tablr below :

![dpi-packing.png](http://images.morere.eu/dpi-packing.png)

https://www.raspberrypi.org/documentation/hardware/raspberrypi/dpi/README.md

dpi_output_format=0x6016

6016 =  110000000010110

output_format: DPI_OUTPUT_FORMAT_18BIT_666_CFG2
rgb_order:   1: DPI_RGB_ORDER_RGB
output_enable_mode 0
invert_pixel_clock     0

hsync_disable          0
vsync_disable          0
output_enable_disable  0
hsync_polarity         1
vsync_polarity         1
output_enable_polarity 0

hsync_phase            0
vsync_phase            0
output_enable_phase 0

If you have setup buildroot as described in https://github.com/recalbox/recalbox-os/wiki/Compilation-%26-Modifications-%28EN%29 and already built recalbox, you can compile the dts with :

```bash
output/build/linux-FIRMWARE/scripts/dtc/dtc -@ -I dts -O dtb -o pwm-audio-pi-zero-overlay.dtbo pwm-audio-pi-zero-overlay.dts
```
with newer kernel you should use : 
```bash
output/build/linux-custom/scripts/dtc/dtc -W no-unit_address_vs_reg -@ -I dts -O dtb -o pwm-audio-pizero-gpicase-overlay.dtbo pwm-audio-pizero-gpicase-overlay.dts
```
and you copy the pwm-audio-pizero-gpicase-overlay.dtbo file in /boot/overlays of your recalbox. The configuration is now simpler : 
```ini
dtoverlay=pwm-audio-pizero-gpicase
```
#Quick test of the sound output

You can test if it works without creating the filtering circuit. You can connect a headphone speaker directly between the PWM pin and a ground as chown in the next figure. 

It should work but the sound can have flaw. 

#Enhance the sound output

In order to create your filter you'll need some electronic components : 		
-* 2 x 270 Ohms resistances		
-* 2 x 150 Ohms ressitances		
-* 2 x 10µF capacitors		
-* 2 x 33nF capacitors (can be replaced by 22nF or 10nF capacitors)		
		
Here is the schematics		
		
![audioFilter_bb.png](http://images.morere.eu/audioFilter_bb.png)		
		
and the real filter		
		
![audioFilter.jpg](http://images.morere.eu/audioFilter.jpg)		
		
You'll need to amplify the output because the signals are filtered and have very low levels.		
		
# Amplification and volume control		
		
You can use a small 5V 2x3W amplificator as this one http://www.ebay.fr/itm/121952326706?trksid=p2060353.m2749.l2649&ssPageName=STRK%3AMEBIDX%3AIT. You can add a an stereo audio potentiometer to be able to tune the volume.		
		
![audioAmpli.jpg](http://images.morere.eu/audioAmpli.jpg)


Same page https://github.com/recalbox/recalbox-os/wiki/Analog-Audio-Pi-Zero-%28EN%29 for details

