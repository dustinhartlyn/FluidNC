---
title: Config File Overview
parent: Configuration
nav_order: 1
---

# Config File Overview

FluidNC is configured (adapted to a particular machine) at run-time by reading a config file from the local (ESP32 flash) file system. The config file is a text file that contains a hierarchical description of which machine features are present, the assignment of controller pins to those features, and other parameters that affect the machine behavior.

**Note:** There are still a few items in config.h file and require a recompile. These are rarely changed things and we are working incorporate as many as practical into the config file.

The name of the config file is given by the setting **$Config/Filename**. Its default value is "config.yaml", but you can change it to another name. You must restart the controller if you change the file or filename.

### Upgrading from Grbl_ESP32

This is where I left off ---------------------