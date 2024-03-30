# Pirate-Duckling
Pirate Duckling is an ATTiny85 based Rubber Ducky clone. It is built with absolute minimal components in mind.

<p align="center">
  <img src="https://raw.githubusercontent.com/gili-yankovitch/Pirate-Duckling/master/images/front.png" height="400"> <img src="https://raw.githubusercontent.com/gili-yankovitch/Pirate-Duckling/master/images/back.png" height="400">
</p>


## Prerequisits
### Software
Please install the following software to be able to flash software to the device:
* [Arduino SDK](https://www.arduino.cc/en/software)
* [Platformio](https://platformio.org/install/cli) or VScode with Platformio AddOn
* [Digispark Drivers](https://github.com/digistump/DigistumpArduino/releases/download/1.6.7/Digistump.Drivers.zip)
* [micronucleus](https://github.com/micronucleus/micronucleus/releases/download/v2.6/micronucleus-cli-master-882e7b4a-x86_64-MINGW64_NT-10.0-17763.zip) in case platformio's toolchain fails to find libusb0.dll, extract it under `%USERPROFILE%\.platformio\packages\tool-micronucleus`

### Hardware
All PCB specs are provided here under hardware/ and this can be easily manufactured as well as paneled for a larger volume. There are no special requirements for this board's manufacturing process.

#### Bill of Materials
| Quantity | Component Description | Mfr. No | Datasheet |
|----------|-----------------------|---------|-----------|
| 1 | SMD 1 kOhms 125 mW 0805 5% | RC0805JR-071KL | [Datasheet](https://www.mouser.co.il/datasheet/2/447/PYu_RC_Group_51_RoHS_L_11-1984063.pdf) |
| 1 | MCU AVR 8K FLSH 512B EE 512B SRAM ADC 5V | ATTiny85-20SU | [Datasheet](https://www.mouser.co.il/datasheet/2/268/Atmel_2586_AVR_8_bit_Microcontroller_ATtiny25_ATti-1315542.pdf) |
| 1 | 0.2W Intelligent external control surface mount SMD LED | SK6812Mini-E | [Datasheet](https://cdn-shop.adafruit.com/product-files/4960/4960_SK6812MINI-E_REV02_EN.pdf) |

# Software
## Software Building
Building the software is easy using platformio. Either run the [build.bat](build.bat) script, or simply execute:
```
pio run --environment attiny85
```

### Software Flashing Using Arduino as an ISP using the broken-out pins
The [platformio.ini](platformio.ini) project is currently configured to be flashed using the Digispark bootloader, but it can also be flashed directly using an Arduino as an ISP. Simply connect the Arduino to the broken-out pin headers, uncomment the commented lines under platforio.ini and run:
```
pio run -t fuses
pio run --environment attiny85 --target upload
```

### Software Flashing Using the Digispark bootloader using the USB port
Flashing software using the USB port would require flashing the ATTiny85 with the bootloader [Attiny85.hex](Digistump/bootloader/ATtiny85.hex) using the flash script [Burn_AT85_bootloader.bat](Digistump/bootloader/Burn_AT85_bootloader.bat) which also sets the appropriate fuses at flash time.
```
<ArduinoFolder>\packages\arduino\tools\avrdude\6.3.0-arduino17\bin\avrdude.exe -C"<ArduinoFolder>\packages\arduino\tools\avrdude\6.3.0-arduino17\etc\avrdude.conf" -v -pattiny85 -cstk500v1 -P<COMPORT> -b19200 -Uflash:w:"ATtiny85.hex":i -U lfuse:w:0xe1:m -U hfuse:w:0xdd:m -U efuse:w:0xfe:m
```
* Don't forget to swap `<ArduinoFolder>` and `<COMPORT>` to the appropriate arduino install folder and the Arduino COM port.

After that, you can build the software normally and flash it using the USB port. Run the following command and remember to only plug in the USB once the flash utility indicates it:
```
pio run --environment digispark-tiny --target upload
```
