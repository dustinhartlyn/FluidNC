## Introduction

FluidNC is a CNC firmware optimized for use with the powerful and flexible ESP32 controller. It is the next generation of firmware from the creators of Grbl_ESP32. It has a lot of improvements over Grbl_ESP32 as listed below.

## Firmware Architecture

- Object-Oriented hierarchical design
- Hardware abstraction for machine features like spindles, motors, and stepper drivers
- Extensible - Adding new features is much easier for the firmware as well as gcode senders.

## Machine Definition Method

Grbl_ESP32 used C preprocessor machine definition files (myMachineDef.h) and config.h to define a machine. Any change required a recompile. The goal with FluidNC is that virtually everyone uses the same compiled firmware and configures it with a structured config file text file. That file is dynamically loaded from the local FLASH on ESP32. It can be uploaded via the serial port or Wifi.

You can have multiple config files stored on the ESP32. The default is config.yaml, but you can change that with $Config/Filename=<myOtherConfig.yaml>

## Basic Grbl Compatibility