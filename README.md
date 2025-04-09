## Dependencies/References:
Based on [RUI3-LowPower-Example by BeeGee-Tokyo](https://github.com/beegee-tokyo/WisBlock-API-V2)
Uses [WisBlock-API-V2](https://github.com/beegee-tokyo/WisBlock-API-V2)

### Platformio Installation
[RakWireless Wiscore Platformio Configuration Guide](https://learn.rakwireless.com/hc/en-us/articles/26687276346775-How-To-Perform-Installation-of-Board-Support-Package-in-PlatformIO)

### AT+Command Configuration, Custom ATC Commands, OTAA enrollment via AT Command
| Description | Command |
| Set Channel Band, For US915 set to band 5 | AT+BAND=5 |
| Set Channel Mask | AT+MASK=002 |
| Read devEUI | AT+DEVEUI=? |
| Write app_key to device | AT+APPKEY=<val_from_chirpstack> |
| Write app_eui to device as 0 | AT+APPEUI=0000000000000000 |
| Check app_eui | AT+APPEUI=? |
| Initiate join sequence | AT+JOIN=1:1:8:3 |
| Set send interval | ATC+SENDINT=3600 |
| Set minimum send interval | ATC+MININT=60 |
| Print device status| ATC+STATUS |

### nrfutil, nrf5sdk-tools, arduino-cli, adafruit-nrfutil usage (debugging)


### Chirpstack Device Management
