This is a port of Nordics [802154_sniffer example](https://github.com/nrfconnect/sdk-nrf/tree/main/samples/peripheral/802154_sniffer) for the Seeed Xiao BLE board. Nordic doen't accept changes for external boards so this is maintained on its own repo.

This example supports either the default Adafruit bootloader used for USB loading or being a standalone firmware flashed by an external debugger.

Idential sections to the examples Readme are ommitted for brevity. This port contains no functional changes so refer to that documentation for regular usage.

# IEEE 802.15.4 Sniffer

.. contents::
   :local:
   :depth: 2

The IEEE 802.15.4 Sniffer listens to a selected IEEE 802.15.4 channel (2.4GHz O-QPSK with DSSS) and integrates with the nRF 802.15.4 sniffer extcap for Wireshark.

## Requirements

The sample supports the following development kits:

- xiao_ble
- xiao_ble_sense

## Overview

The application presents the user with a command-line interface.

See the :ref:`802154_sniffer_commands` for the list of the available commands.

Heartbeat LED:
   LED2 (green)
   When the capture is stopped the LED blinks with a period of 2 seconds with 50% duty cycle.
   When the capture is ongoing the LED blinks with a period of 0.5 seconds with 50% duty cycle.

Network Activity LED:
   LED3 (blue)
   When the sniffer captures a packet the LED is toggled on and off.

### bootloader - reboot the device to the bootloader

The ``bootloader`` command reboots the device in bootloader mode.

   .. parsed-literal::
      :class: highlight

      bootloader

The device reboots into bootloader mode. This is only supported on builds with the Adafruit bootloader.

## Building

### Building for use with Adafruit Bootloader

Seeed Xiao BLE boards ship with an Adafruit nRF52 bootloader and need a compliant UF2 file to update via the USB bootloader. This is built into the board configurations for the xaio_ble board but sysbuild ignores ignores these files in an apparent bug. Partition files have been added to the app directory to address this issue

```shell
west build --board xiao_ble/nrf52840
# or
west build --board xiao_ble/nrf52840 --no-sysbuild
```

### Building Standalone Firmware Image

Due to the aforementioned issue with sysbuild, the config parameters to build a standalone image will not work with sysbuild. The workaround is to remove the pm_static files or don't use sysbuild

```shell
west build --board xiao_ble/nrf52840 --no-sysbuild -DEXTRA_CONF_FILE=boards/xiao_ble_nrf52840_nobootloader.conf
# or remove the partition file for the sysbuild process
mv pm_static_xiao_ble_nrf52840.yml _pm_static_xiao_ble_nrf52840.yml
west build --board xiao_ble/nrf52840 -DEXTRA_CONF_FILE=boards/xiao_ble_nrf52840_nobootloader.conf
mv _pm_static_xiao_ble_nrf52840.yml pm_static_xiao_ble_nrf52840.yml
```

Note: The resulting hex file will require an external debugger to flash.
