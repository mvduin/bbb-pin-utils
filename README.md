# show-pins

This perl script gives a nicely formatted overview of the current pin configuration of your BeagleBone Black.

## Installation

```bash
cd /usr/local/sbin
sudo wget https://raw.githubusercontent.com/mvduin/bbb-pin-utils/master/show-pins
sudo chmod a+x show-pins
```

## Usage

```bash
sudo show-pins | sort	# sorted by expansion header pin
sudo show-pins		# in order of pin-config array
sudo show-pins -v	# show more pins
sudo show-pins -vv	# show all configurable pins
```

## Output details

Every line represents one of the configurable pins of the [AM3358 SoC](http://www.ti.com/product/am3358) on the BeagleBone Black. By default only the 70 pins that connect to the expansion headers P8 and P9 are listed. If the `-v` option is passed, more pins that may be of interest are shown. If the `-v` option is given twice, all pins in the pinconfig array are shown except the ddr3 pins.

### Description

The first column is a description of the pin's usage on the BBB.  If connected to an expansion header pin, this is listed first.  Thus, a listing sorted by expansion header pin is obtained simply by piping the script's output through `sort`.

![](/doc/images/show-pins-sorted.png)

If a pin has additional connections on the BBB or other special considerations, these are also listed in the description:
* **eMMC**: connected to eMMC; you must leave these alone unless eMMC has been disabled!
* **hdmi**: connected to HDMI framer; used for video output if HDMI is enabled.
* **hdmi audio**: connected to HDMI framer; used for audio output if HDMI audio is enabled.
* **audio osc**: audio oscillator output, if enabled (e.g. when HDMI audio is enabled).
* **cape i²c**: configured as I²C bus by the default Device Tree.
* **spi boot**: depending on boot config, the ROM bootloader may configure these pins as SPI and attempt to boot from an SPI flash. This happens in particular if the S2 button is held down at power-on and no bootable μSD card is present.
* **jtag emu**: only relevant when using high-speed system trace via the JTAG header.

### (README unfinished...)

![](/doc/images/show-pins.png)

![](/doc/images/io-cell-config.png)

![](/doc/images/pinmux.png)

![](/doc/images/gpio.png)

![](/doc/images/kernel.png)
