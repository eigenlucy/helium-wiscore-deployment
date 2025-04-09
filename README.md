## Dependencies/References:
* Based on [RUI3-LowPower-Example by BeeGee-Tokyo](https://github.com/beegee-tokyo/WisBlock-API-V2)
* Uses [WisBlock-API-V2](https://github.com/beegee-tokyo/WisBlock-API-V2)
* For use with [Disk91's](https://www.disk91.com/2022/technology/helium/installing-chirpstack-lorawan-network-server-for-helium/) [Chirpstack LoRaWan network server Helium IoT](https://github.com/disk91/helium-chirpstack-community)

## Platformio Installation (Preferred)
[RakWireless Wiscore Platformio Configuration Guide](https://learn.rakwireless.com/hc/en-us/articles/26687276346775-How-To-Perform-Installation-of-Board-Support-Package-in-PlatformIO)

## Firmware Overview
| Program | Description |
| -------------- | -------------- |
| buoyfish-tracker.ino | main program of the firmware. the events are all attached and configured here. once the device boots, everything is triggered by the events and timers attached in the setup |
| app.h | Enable/disable MYLOG debug, configure Cayenne LPP channels for sensors, initialize sensor variables, forward declarations, etc |
| custom_at.cpp | contains the custom ATC+Commands used to program the device over serial |
| dr_calculator.cpp | Automatically adjusts datarate / spreading factor / time on air according to payload size |
| RAK1904_acc.cpp | For the rak1904 accelerometer board. Assign WisBlock slot with IO assignments |
| RAK12500_gnss.cpp | for the rak12500 gps board. Assign WisBlock slot with IO assignments |
| wisblock_cayenne.cpp / wisblock_cayenne.h | Handle the formatting of sensor data as lora packets |

![Flow chart showing the states and events the device progresses through after booting, taken from BeeGee-Tokyo's WisBlock-API-V2 Repo](https://github.com/eigenlucy/helium-wiscore-deployment/blob/master/refs/firmware_overview.png)
![Block diagram showing the structure of the API and application functions, taken from BeeGee-Tokyo's WisBlock-API-V2 Repo](https://github.com/eigenlucy/helium-wiscore-deployment/blob/master/refs/APIFirmwareStructure.png)

## AT+Command Configuration, Custom ATC Commands, OTAA enrollment via AT Command
### AT Configuration Command List
| Description | Command |
| --------- | --------- |
| Set Channel Band, For US915 set to band 5 | AT+BAND=5 |
| Set Channel Mask | AT+MASK=002 |
| Read devEUI | AT+DEVEUI=? |
| Write app_key to device | AT+APPKEY={val_from_chirpstack} |
| Write app_eui to device as 0 | AT+APPEUI=0000000000000000 |
| Check app_eui | AT+APPEUI=? |
| Initiate join sequence | AT+JOIN=1:1:8:3 |
| Set send interval | ATC+SENDINT=3600 |
| Set minimum send interval | ATC+MININT=60 |
| Print device status | ATC+STATUS |

## nrfutil, nrf5sdk-tools, arduino-cli, adafruit-nrfutil usage (advanced firmware debugging, bootloader update, etc)
### Reqs
Compiled nrfutil, nrf5sdk-tools, and adafruit-nrfutil binaries for arch to flash new rak4631-r bootloader and firmware. included those, compiled for arch tho
* adafruit-nrtutil
* nrfutil (the one that includes nrf5sdk-tools, which is dif than the one on pypi ig)
* arduino-cli
* pyinstaller

### Command References:
* This checks if a port has been seized by another program
```
$ lsof | grep ttyACM
```
* This compiled the project folder
```
$ arduino-cli compile --fqbn rak_rui:nrf52:WisCoreRAK4631Board buoyfish-tracker/ --build-path build/ -e
```
* this is what acutally build the dfu package after i compiled thenrfutil binary. Ran in the builds file that the arduino-cli build toolchain dumps things into
```
$ nrfutil pkg generate --hw-version 52 --application-version 1 --application buoyfish-tracker.ino.hex --sd-req 0x00 buoyfish_tracker_v1_dfu_nrfutil.zip
```
* This is what works to actualy flash the device
```
$ nrfutil nrf5sdk-tools dfu serial -pkg buoyfish_tracker_v1_dfu_nrfutil.zip -p /dev/ttyACM0
```

## Chirpstack Device Management
![Picture of the Chirpstack Console with arrows indicating the submenus device registration is acccesed under (Tenant/Applications/)](https://github.com/eigenlucy/helium-wiscore-deployment/blob/master/refs/DeviceEnrollment1.png)
![Picture of the Chirpstack Console with arrows indicating the fields to fill out for device enrollment (Name, Device EUI, Device Profile, Join EUI)](https://github.com/eigenlucy/helium-wiscore-deployment/blob/master/refs/DeviceEnrollment2.png)

## Helium Enrollment Process
![Flow chart showing the movement of data from the through lorawan/helium to chirpstack/node-red](https://github.com/eigenlucy/helium-wiscore-deployment/blob/master/refs/NetworkDiagram.png)
![Network Diagram Showing the Device Enrollment Process](https://github.com/eigenlucy/helium-wiscore-deployment/blob/master/refs/Device_Registration_Process.png)

