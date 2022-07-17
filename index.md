---
title: Introduction
has_children: false
nav_order: 1
---

# Introduction

test update7

FluidNC is a CNC firmware optimized for use with the powerful and flexible ESP32 controller. It is the next generation of firmware from the creators of Grbl_ESP32. It has a lot of improvements over Grbl_ESP32 as listed below.

### Firmware Architecture

- Object-Oriented hierarchical design
- Hardware abstraction for machine features like spindles, motors, and stepper drivers
- Extensible - Adding new features is much easier for the firmware as well as gcode senders.

### Machine Definition Method

Grbl_ESP32 used C preprocessor machine definition files (myMachineDef.h) and config.h to define a machine. Any change required a recompile. The goal with FluidNC is that virtually everyone uses the same compiled firmware and configures it with a structured [config file](docs_configuration/Configuration.md) text file. That file is dynamically loaded from the local FLASH on ESP32. It can be uploaded via the serial port or Wifi.

You can have multiple config files stored on the ESP32. The default is config.yaml, but you can change that with $Config/Filename= {{"<"}}myOtherConfig {{".yaml>"}}


### Basic Grbl Compatibility
The intent is to maintain as much Grbl compatibility as possible. It is 100% compatible with the day to day operations of running GCode with a sender, so there is no change to the Grbl GCode send/response protocol, and all Grbl GCodes are supported. The Grbl $ settings and commands that a sender might issue after each reset are similarly supported. Grbl $ settings that are used only for initial machine setup, then never changed unless the machine is physically reconfigured, will not be supported. Grbl's $number=number method for machine setup is simply too weak to support the range of machine configurations that FluidNC can handle. (In fact, Grbl's $number machine setup method was too weak even for classic Grbl, which required editing C files and recompiling for many kinds of changes.) What this means is that existing senders will still be able to run GCode jobs on Fluid, but the "setup wizards" that a few senders have will not work in their current form. We are working on a mechanism to enable even better setup wizards - graphical configuration helpers - that can handle the vast range of machines that FluidNC supports, extensible so that senders will be able to automatically support new FluidNC features without sender code changes.

### I/O Pins

Pin configuration is a key step in machine setup. You must assign specific pins - be they GPIOs or pins on I/O expander chips - to functions like stepper control, limit switches, spindle control, and I/O buses that communicate with other devices. Furthermore, pins have attributes like active state (high or low) and internal pullups that must be specified. In classic Grbl, you assign pins by editing a ".h" C preprocessor file, then recompiling. Classic Grbl does let you choose the active state (but not the pullup states) for some pins with $ commands, but the interface is clumsy, requiring the user to perform binary-to-decimal math on "bitmask" variables. The bitmask technique, in addition to being obscure to users who are not programmers, cannot handle things like multiple independent motors on the same axis.

FluidNC's pin assignment syntax is clear, straightforward, capable and consistent. For example, to assign GPIO 4 as the probe pin, active low, with internal pullups enabled, you write:

```js
probe:
  pin: gpio.4:low:pu
```

The following example illustrates that both input and output pins can be assigned in the same way, with the direction being implicit in the function that the pin is used for.

```js
control:
   feed_hold_pin: gpio.14:low:pu
coolant:
   mist_pin: gpio.21
```

The feed hold pin, known to be an input because that is what the feed hold function requires, is set to GPIO 12, active low so a feed hold will be triggered when the pin goes to the electrical low state. The internal pullup is enabled, which is appropriate and necessary if there is no external pullup resistor or active high drive on that signal. The mist pin, known to be an output because the mist function controls an external relay or similar actuator, is assigned to GPIO 21. The signal is active high, which is the default if neither ":low" nor ":high" is explicitly mentioned, and no internal pullups are enabled. If GCode turns on mist coolant, the pin will go to the electrical high state.

The firmware knows additional requirements for pins based on how they are used, applying and checking those automatically. For example, a limit pin must be an input that support interrupts, so the attempt to assign a hardware pin that cannot meet those requirements triggers an error message. Other errors, such at an attempt to use the same pin for two different purposes, are similarly checked.

For functions that are not used, you can either omit the line that would assign a pin to that function, or explicitly list it as "no_pin". For example, these two are equivalent, saying that flood coolant is not supported - and attempts to turn on flood coolant will be silently ignored:

```js
coolant:
  flood_pin: no_pin
```
or you can just omit the "flood:" line in the coolant section - or omit the entire coolant section if the machine does not support any form of coolant control.

```js
coolant:
```
### Tuning
