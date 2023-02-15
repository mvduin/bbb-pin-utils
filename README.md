# show-pins

This perl script gives a nicely formatted overview of the current pin configuration of your BeagleBone White.

## Installation

```bash
cd /usr/local/sbin
sudo wget -N https://raw.githubusercontent.com/mvduin/bbb-pin-utils/white/show-pins
sudo chmod a+x show-pins
```

## Usage

```bash
sudo show-pins | sort	# sorted by expansion header pin
sudo show-pins		# sorted by pin index
sudo show-pins -v	# show more pins
sudo show-pins -vv	# show all configurable pins
```

## Output details

![](/doc/images/show-pins.png)

Every line represents one of the configurable pins of the [AM3358 SoC](http://www.ti.com/product/am3358) on the BeagleBone White. By default only the 70 pins that connect to the expansion headers P8 and P9 are listed. If the `-v` (or `--verbose`) option is passed, more pins that may be of interest are shown. If the `-v` option is given twice, all pins in the pinconfig array are shown except the ddr3 pins.

### Description

The first column is a description of the pin's usage on the BBW.  If connected to an expansion header pin, this is listed first.  Thus, a listing sorted by expansion header pin is obtained simply by piping the script's output through `sort`.

![](/doc/images/show-pins-sorted.png)

If a pin has additional connections on the BBW or other special considerations, these are also listed in the description:
* **cape i²c**: configured as I²C bus by the default Device Tree.
* **spi boot**: depending on boot config, the ROM bootloader may configure these pins as SPI and attempt to boot from an SPI flash. This happens in particular if the S2 button is held down at power-on.
* **jtag emu**: only relevant when using high-speed system trace via the JTAG header.

### Pin index

The second column is the index into the SoC's pin-config array. By default the output is sorted by this, which often helps to get functionally related pins grouped together and in a logical order.

If the `-A` (or `--address`) option is passed once then this column will instead show the offset from the start of the array in hexadecimal.  If the option is passed twice, it shows the last three hexadecimal digits of the address of the pin config register, and if the option is passed more times the full address is shown.

### IO Cell Config

![](/doc/images/io-cell-config.png)

1. Slew rate: fast or slow. Typically this is left at its default value, fast.

2. Whether the input receiver is enabled.

3. Whether internal pull-up or pull-down is enabled.

If the input receiver is disabled then the pin level cannot be observed (reads as zero), but it slightly reduces power consumption and it protects the pin if it's exposed to intermediate voltages (in-between logic-low and logic-high) for extended time.

In a few cases the receiver must be enabled for clock output pins because the peripheral depends on reading them back for retiming purposes. In particular, this applies to SPI clock, I²C clock, and in some cases McASP clock (depending on configuration).

### Pinmux

![](/doc/images/pinmux.png)

Which mux option is selected for the pin (0-7) and a short description thereof. Note that I sometimes abbreviate peripherals, e.g. just "pwm" instead of eHRPWM.

### GPIO

![](/doc/images/gpio.png)

If a pin is muxed as gpio and this gpio is "in use" (by a kernel driver or exported to userspace), then the current state of the gpio is shown:

- `lo >>`  output, driven low
- `hi >>`  output, driven high
- `<< lo`  input, detected low
- `<< hi`  input, detected high
- `<!   `  input, level unknown because receiver is disabled

If the kernel considers the gpio to be unused then information about its state is unavailable and the gpio will be grayed out.

### Usage

![](/doc/images/kernel.png)

This reports what is using the pin according to the kernel. This may show a device name, or for gpios a label provided by the driver that requested it.

The name of the pinctrl node in Device Tree is shown in parentheses.
