## Dependencies/References:
Based on [RUI3-LowPower-Example by BeeGee-Tokyo](https://github.com/beegee-tokyo/WisBlock-API-V2)
Uses [WisBlock-API-V2](https://github.com/beegee-tokyo/WisBlock-API-V2)

### Platformio Installation (Preferred)
[RakWireless Wiscore Platformio Configuration Guide](https://learn.rakwireless.com/hc/en-us/articles/26687276346775-How-To-Perform-Installation-of-Board-Support-Package-in-PlatformIO)

### AT+Command Configuration, Custom ATC Commands, OTAA enrollment via AT Command
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

### nrfutil, nrf5sdk-tools, arduino-cli, adafruit-nrfutil usage (debugging)
#### Reqs
Compiled nrfutil, nrf5sdk-tools, and adafruit-nrfutil binaries for arch to flash new rak4631-r bootloader and firmware. included those, compiled for arch tho
* adafruit-nrtutil
* nrfutil (the one that includes nrf5sdk-tools, which is dif than the one on pypi ig)
* arduino-cli
* pyinstaller

#### Command References:
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
### Chirpstack Device Management
