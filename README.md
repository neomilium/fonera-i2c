_La Fonera_ is a small router with wireless functions created by FON.

_La Fonera_'s firmware can easily be replaced by _OpenWrt_ which provides any required elements to enable I2C bus usage. This feature allow us to use _La Fonera_ to drive I2C devices like eeproms, temperature sensors, etc.

If you want to add I2C bus(es) to _La Fonera_, a this wiki page have been made to help you!

# Introduction #

This guide will help you to connect _La Fonera_ to real world.

_La Fonera_ is a little router that runs Linux kernel. Like many routers, _La Fonera_ had general purpose inputs/outputs (GPIO) on board. In case of _La Fonera_, not all these GPIO are used and they can be used to create one or two I2C buses.

# Hardware modifications #
  * Open the case
![http://fonera-i2c.googlecode.com/files/FON2100_case_bottom.jpg](http://fonera-i2c.googlecode.com/files/FON2100_case_bottom.jpg)

Remove the two rubber feet on the opposite site to the antenna jack and unscrew the revealed screws.

  * Locate SW1
![http://fonera-i2c.googlecode.com/files/FON2100_pcb_top_small.jpg](http://fonera-i2c.googlecode.com/files/FON2100_pcb_top_small.jpg)

On this picture, SW1 is located on top-right corner and already have a tulip connector sold on.

  * Remove caps
On these GPIO there is debouncing RC, caps are on top side of PCB, resistors are located on bottom side.
To prevent this RC to filter data (SDA) or clock (SCL) I2C signals, its recommended to remove caps.

  * Add pull-ups resistors
To works correctly, 2.2KOhms resistor pull-up need to be placed on both signal lines.
Note: I made some tests with only one I2C device on bus, 1KOhms and 2.2KOhms but 10KOhms doesn't work, just like if these pull-ups are not present.

  * (Optionnal) Sold internal connector
You can add a cheap "easy-to-plug" connector on GPIOs in order to be able to plug/unplug I2C device without dismantle all box each time (i.e. no need to remove PCB from box).

![http://fonera-i2c.googlecode.com/files/FON2100_pcb_internal_gpio_connector_small.jpg](http://fonera-i2c.googlecode.com/files/FON2100_pcb_internal_gpio_connector_small.jpg)

As you can see on picture, 5x1 tulip connector (2.54 mm / 0.1 inch) is just perfect if you remove center pin to prevent from short circuits. Even PCB doesn't fit with 2.54 mm / 0.1 inch, this 5x1 connector can be sold on this 6x1 pads on PCB. To ease soldering, you should clean these pads (metalic vias) before attempt to put tulip connector.

  * (Optionnal) Add external connector

# Software #
  * Install OpenWrt 8.09.x
  * Install kernel modules that allow to use GPIO as I2C bus
  * Configure module loading options
    * Create a new file /etc/modules.d/58-i2c-gpio-custom
In this file, you must put options to i2c-gpio-custom module loading.
In example, this content allow i2c-gpio-custom module loading in order to create I2C device interface `/dev/i2c-0` using GPIO1 as SDA and GPIO7 as SCL.
```
i2c-gpio-custom bus0=0,1,7
```

  * Reboot in order to load kmod-i2c-gpio-custom before kmod-i2c-gpio
Atfer reboot, `dmesg` command should say something like that:
```
i2c /dev entries driver
Custom GPIO-based I2C driver version 0.1.1
i2c-gpio i2c-gpio.0: using pins 1 (SDA) and 7 (SCL)
```
  * Install i2c-tools
```
opkg update && opkg install i2c-tools
```

  * Probe I2C devices (i2cdetect)

  * Test using i2cset/i2cget (e.g. with PCF 8574)
# To-do list #
## HW ##
  * Attempt to drive I2C devices without removing caps
  * Attempt to drive I2C devices with bottom resistors removed but without pull-ups.
  * Test to driver multiple I2C device
## SW ##
  * Write a little C sample to drive PCF 8574.
