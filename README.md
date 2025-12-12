# G5 Physical Controls - RP2040 Firmware

First off, I know calling an I2C device a 'slave' has fallen out of favor. My apologies.

## Overview

This repo contains RP2040 firmware code in C/C++ to support a dual setup consisting of an LED pushbutton plus a pushbutton rotary encoder that act as the physical controls for my simulated G5 displays for MSFS. That repo is over here: https://github.com/ccrawford/CC_G5/

I don't really expect this to be useful to someone else. These are mostly notes to myself. However, if you would like to try building this, I'm happy to help and provide additional clarity.

## Wiring

Wiring diagrams are included, but it's a bit of a rats-nest. Changing pin assignments is possible as long as you update the code to reflect the changes, but be careful with the pins you use for I2C, as only certain RP2040 pins can be used for the first channel and others for the second channel.

### Pin Assignments for RP2040

#### PFD (Primary Flight Display)

| Component | Pin |
|-----------|-----|
| Rotary A | GP18 (4 from bottom right) |
| Rotary B | GP19 (5 up from bottom right) |
| Rotary SW | GP17 (second pin from bottom) |
| Button | GP16 |
| LED | GP10 (Other side of board!) |

**I2C0** (`Wire.begin(20, 21)`):

| Signal | Pin |
|--------|-----|
| SDA | GP20 |
| SCL | GP21 |
| INT | GP22 |

#### HSI (Horizontal Situation Indicator)

| Component | Pin |
|-----------|-----|
| Rotary A | GP2 |
| Rotary B | GP3 |
| Rotary SW | GP4 |
| Button | GP14 |
| LED | GP15 |

**I2C1** (`Wire1.begin(26, 27)`):

| Signal | Pin |
|--------|-----|
| SDA | GP26 |
| SCL | GP27 |
| INT | GP28 |

### Corresponding Code in Main.cpp
Note: if there are any inconsistencies, the code below is definitive answer.

```cpp
// Pin definitions for PFD (Primary Flight Display) control
#define PFD_ENCODER_PIN_A 18
#define PFD_ENCODER_PIN_B 19
#define PFD_ENCODER_PIN_BTN 17
#define PFD_POWER_BUTTON 16
#define PFD_LED_PIN 10
#define I2C_SDA_PIN 20
#define I2C_SCL_PIN 21
#define PFD_INT_PIN 22
#define I2C_SLAVE_ADDR 0x08

// Pin definitions for HSI (Horizontal Situation Indicator) control
#define HSI_ENCODER_PIN_A 2
#define HSI_ENCODER_PIN_B 3
#define HSI_ENCODER_PIN_BTN 4
#define HSI_POWER_BUTTON 14
#define HSI_LED_PIN 15
#define I2C2_SDA_PIN 26
#define I2C2_SCL_PIN 27
#define HSI_INT_PIN 28
#define I2C2_SLAVE_ADDR 0x08
```

## Guition Screen Connection

The header pins coming off the board go into the back of a 4", 480x480 Guition screen with a built-in ESP32-S3. Looking at the back of the screen with the USB and SD card input at the bottom of the screen you'll see a 2 row, 4 column header. We need 5V into the top right, ground on the bottom left, and then the pins as assigned above.

> **Note:** The pin numbers below are for the ESP32 embedded in the Guition screen, NOT the RP2040!

```
--------------------------
| Gnd | Rx  | Tx  |  5v  |
--------------------------
| Gnd | IO1 | IO2 | IO40 |
--------------------------
```

I do NOT solder to the Guition screen. I use header pins, especially for the 5V as I disconnect that when I need to program the RP2040. Note that at runtime no USB connection to the PC is needed if you supply power from the Guition screen. I've never tried, but I wouldn't connect to the PC and to the screen power at the same time. It should be ok, but I wouldn't risk it.

## Why This Exists

The parallel displays I use don't have enough GPIO pins readily available to support the LED power button and the rotary encoder+pushbutton that the G5 has. So this was a quick and easy way to enable the controls using what I had on hand. Originally I had the RP2040 processing MobiFlight I/O, but that caused more problems with dropped messages and lag.

## Parts list
EC11 encoder with push button (2). I have a lot of different types in the parts drawer, but I generally like short-D shaft ones. Just search AliExpress for 'ec11' and you'll see tons of options.
LED momentary NO push button (2). I use the black ones with a LED lighted ring around them. Search AliExpress for 1NO pushbutton LED and you'll find them in different colors and sizes. I use 12mm black w/ white LED.
Rp2040 aka Raspberry Pi Pico. These things are like $1-2 on AliExpress. search 'rp2040'
Wire. I use small guage silicon wire.

## Images

See the [Images](./CC_G5_Slave/Images) directory for wiring photos and diagrams.
