# CombiesGit's I2C EEPROM Library

Version 0.3 from 06/01/2016 12:52:30

Can be used with the Arduino IDE. This library is based on Wire.


## Download

[ZIP of repo for Arduino IDE installation](https://github.com/Prehistoricman/I2C_EEPROM/archive/master.zip")



## Features

  * Mostly call compatible with the Arduino EEPROM Lib
  * Support for many I2C EEPROMs
  * Relatively easy creation of user-defined EEPROMs
  * Low RAM consumption


## Supported EEPROMs
Support for other EEPROMs can be added. See instructions in I2C_EEPROM.h.

### Atmel

Class name | Size (bytes) | Page size (bytes) | Addressing mode
------------ | ------------- | ------------ | -------------
AT24C01 | 128 | 8 | 1
AT24C02 | 256 | 8 | 1
AT24C04 | 512 | 16 | 1
AT24C08 | 1024 | 16 | 1
AT24C16 | 2048 | 16 | 1
AT24C32 | 4096 | 32 | 2
AT24C64 | 8192 | 64 | 2
AT24C128 | 16384 | 64 | 2
AT24C256 | 32768 | 64 | 2
AT24C512 | 65536 | 128 | 2


### STMicroelectronics

Class name | Size (bytes) | Page size (bytes) | Addressing mode
------------ | ------------- | ------------ | -------------
M24C01 | 128 | 16 | 1
ST24C01 | 128 | 8 | 1
M24C02 | 256 | 16 | 1
ST24C02 | 256 | 8 | 1
M24C04 | 512 | 16 | 1
ST24C04 | 512 | 8 | 1
M24C08 | 1024 | 16 | 1
ST24C08 | 1024 | 16 | 1
M24C16 | 2048 | 16 | 1
M24C32 | 4096 | 32 | 2
M24C64 | 8192 | 32 | 2
M24128 | 16384 | 64 | 2
M24256 | 32768 | 64 | 2
M24512 | 65536 | 64 | 2


### Microchip

Class name | Size (bytes) | Page size (bytes) | Addressing mode
------------ | ------------- | ------------ | -------------
MC24C01C | 128 | 16 | 1
MC24AA02E48 | 256 | 8 | 1
MC24AA025E48 | 256 | 16 | 1


## Purpose

Many strings had to be managed for menu guidance. Even the F() macro was only of limited use as it was running out of flash memory. The EEPROM built into the AVR brought relief at the beginning, but with the size of the project it was no longer enough. And anyway, the AVR EEPROMs are rather small.

The Atmel AT24C32 I2C EEPROM already installed on the RTC used promised a remedy. I have come up with some good things in the search for usable libs. But also a lot of creepy things. But what was common to all, they cover only a few types. And each library has its own methods, which have little in common with the methods of the original Arduino EEPROM library.

## Installation

This library is available as a zip file. In the Arduino IDE Library Manager, select and install the zip file.
Main menu -> Sketch -> Include Library -> Add .ZIP Library

## Dependencies

This library depends on Wire.h. Wire.begin() must be called before using any library functions. EEPROMs must accept ACK polling.

Here is the status.ino from the library's examples:
```#include <Wire.h>
#include <I2C_EEPROM.h>
AT24C32 <> eep; // The EEPROM on the usual China RTC, default address 0x50 (80) 


void setup () {
    Serial.begin(9600);
    Wire.begin();
    
    // The memory size of the EEPROM is saved in the class
    // can therefore be read out without the EEPROM really
    // ready
    Serial.print("EEPROM lenth:");
    Serial.println(eep.length());
    
    
    if (eep.ready()) { // EEPROM ready?
        // EEPROM has been found and is ready
        Serial.println("EEPROM is ready!");
    } else {
        // EEPROM did not respond
        // Even after writing, the EEPROM blocks a few ms
        Serial.println("EEPROM is not ready, check wiring");
    }
}


void loop () {
    
}
```

## Usage

### void begin()
There is no begin method.

### uint16_t length()
Returns the size of the EEPROM. This value is stored in the class, so no connection to the EEPROM is necessary.

### uint8_t read(unit16_t address)
Reads 1 byte from the specified address.

### void write(unit16_t address, uint8_t value)
Writes 1 byte to the specified address.

### void update(unit16_t address, uint8_t value)
Reads from the specified address, and writes to it if that value differs from the specified value.

### void put(unit16_t address,  customvar)
Writes any variable content to the specified address. This method internally uses the update() method.

### void get(unit16_t address,  customvar)
Reads into any variable from the specified address.

### bool ready()
Returns true if the block is ready.

### void fastBlockWrite(uint16_t address, void \*start, uint16_t length)
Writes any data block to the specified address. Paging is handled internally. Significantly faster than using put().

### void fastBlockRead(uint16_t address, void \*start, uint16_t length)
Reads a block of data from the specified address. Paging is handled internally. Significantly faster than using get().

### void onWaiting(void (\*callback)())
After writing, the EEPROM may need time to complete the process. A callback function can be called to use this time. It is not advisable to make further EEPROM calls in the callback.
