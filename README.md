

## Introduction

FluidNC is a CNC firmware optimized for use with the powerful and flexible ESP32 controller. It is the next generation of firmware from the creators of Grbl_ESP32. It has a lot of improvements over Grbl_ESP32 as listed below.

## Firmware Architecture

- Object-Oriented hierarchical design
- Hardware abstraction for machine features like spindles, motors, and stepper drivers
- Extensible - Adding new features is much easier for the firmware as well as gcode senders.

## Machine Definition Method

Grbl_ESP32 used C preprocessor machine definition files (myMachineDef.h) and config.h to define a machine. Any change required a recompile. The goal with FluidNC is that virtually everyone uses the same compiled firmware and configures it with a structured [Configuration](config file) text file. That file is dynamically loaded from the local FLASH on ESP32. It can be uploaded via the serial port or Wifi.

You can have multiple config files stored on the ESP32. The default is config.yaml, but you can change that with $Config/Filename=/<myOtherConfig.yaml>

## Basic Grbl Compatibility
The intent is to maintain as much Grbl compatibility as possible. It is 100% compatible with the day to day operations of running GCode with a sender, so there is no change to the Grbl GCode send/response protocol, and all Grbl GCodes are supported. The Grbl $ settings and commands that a sender might issue after each reset are similarly supported. Grbl $ settings that are used only for initial machine setup, then never changed unless the machine is physically reconfigured, will not be supported. Grbl's $number=number method for machine setup is simply too weak to support the range of machine configurations that FluidNC can handle. (In fact, Grbl's $number machine setup method was too weak even for classic Grbl, which required editing C files and recompiling for many kinds of changes.) What this means is that existing senders will still be able to run GCode jobs on Fluid, but the "setup wizards" that a few senders have will not work in their current form. We are working on a mechanism to enable even better setup wizards - graphical configuration helpers - that can handle the vast range of machines that FluidNC supports, extensible so that senders will be able to automatically support new FluidNC features without sender code changes.

#I/O Pins

Pin configuration is a key step in machine setup. You must assign specific pins - be they GPIOs or pins on I/O expander chips - to functions like stepper control, limit switches, spindle control, and I/O buses that communicate with other devices. Furthermore, pins have attributes like active state (high or low) and internal pullups that must be specified. In classic Grbl, you assign pins by editing a ".h" C preprocessor file, then recompiling. Classic Grbl does let you choose the active state (but not the pullup states) for some pins with $ commands, but the interface is clumsy, requiring the user to perform binary-to-decimal math on "bitmask" variables. The bitmask technique, in addition to being obscure to users who are not programmers, cannot handle things like multiple independent motors on the same axis.

FluidNC's pin assignment syntax is clear, straightforward, capable and consistent. For example, to assign GPIO 4 as the probe pin, active low, with internal pullups enabled, you write:

```js
probe:
  pin: gpio.4:low:pu
```