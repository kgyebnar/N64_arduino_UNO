# N64 arduino UNO converter...
This is the modification of unforgiven512s code, i had some difficulities to using with the cheap chinese N64 controller.
The "A" button was completly "flickering" state no metter what i did...this completely ruining the usability of the controller. 
I spent couple of hours to identify the root couse of the problem...looks like the controller sending "garbage" before the first command.

Finally i found the solution: sending some initialising command before we start to talk with the N64 controller (sending 0x00 to the controller before we doing anything else...)
After this mod the code below works perfectly with 2 controllers.


This is the Magic:

  // Initialize the gamecube controller by sending it a null byte.
  // This is unnecessary for a standard controller, but is required for the
  // Chinese controller
  unsigned char initialize = 0x00;
  noInterrupts();
  N64_send(&initialize, 1);
  N64_send2(&initialize, 1);



# n64-arduino
Plug two N64 controllers to an Arduino and turn your Arduino in a USB HID joystick! Play your favourite games on the pc with the best controller ever!

I've had some trouble setting up MickMad's original project, so I decided to fix it. Somehow my Arduino swapped the buttons with the joystick, so I had to [fix the order](https://github.com/twinone/n64-arduino/commit/7aa0165292885c81427e76dc71b11373e79d2d90).
I use [mupen64plus](http://www.mupen64plus.org/) ([github](https://github.com/mupen64plus)), so I generated the config file so that both controllers work with it directly.

# Instructions

We will use Linux or OSX to program the Arduino's main processor and also the atmega16u2 (the programmer). The atmega16u2 chip allows us to use the Arduino as a USB HID device.

### 0. Download this project to somewhere you like

`git clone github.com/twinone/n64-arduino n64-arduino`

### 1. Install dfu-programmer

osx:

`brew install dfu-programmer`

aptitude:

`apt-get install dfu-programmer`

### 2. Upload the sketch
* Make sure you use an Arduino Uno R3 (or other board with an atmega16u2 for the programmer).
* Open the [sketch](https://github.com/twinone/n64-arduino/blob/master/N64-To-USB/N64_To_USB/N64_To_USB.ino) in the [Arduino IDE](https://www.arduino.cc/en/Main/Software) and upload it to your Arduino.

### 3. Metaprogramming: Programming the programmer

* Connect your Arduino to your computer via USB.
* Short the two pins marked in this picture together with a jumper wire (don't worry, you won't burn anything):
<img width=400 src="https://www.arduino.cc/en/uploads/Hacking/Uno-front-DFU-reset.png">

* Unshort them again.
* Make sure your Arduino isn't listed in the Arduino IDE's serial port list.

I've attached a very simple script that does the flashing, mainly because I was too lazy to write the 3 commands all the time while I was debugging.

* `./flash.sh joystick.hex`

This will render the programmer useless. If you want to upload a new sketch to your Arduino, you'll have to use `./flash.sh uno.hex` again, which contains the programmer's original firmware.

### 4. The wiring

Here's the pinout for your N64 Controller's plug:

<img width=400 src="http://www.pieter-jan.com/images/N64_Controller/Connector.JPG">

* Connect the Controller's `VCC` to the Arduino's `3v3 pin` (3.3v)
* Connect the Controller's `DATA` to the Arduino's `digital pin 2` (pin 3 for the second controller)
* Connect the Controller's `GND` to the Arduino's `GND` pin

### 5. Reconnect, configure, play!

* Reconnect your arduino to your computer
* Copy the config file to where it belongs:
```
mkdir -p ~/.config/mupen64plus/
cp mupen64plus.cnf ~/.config/mupen64plus/
```
(If you already have a config file, just copy the controller parts)

* Play!


# Credits
All credits to unforgiven512

This project is an extension of [MickMad](https://github.com/MickMad)'s awesome [project](https://github.com/MickMad/N64-To-USB)

More awesome people this project is built upon
Darran original [Arduino Joystick](http://hunt.net.nz/users/darran/weblog/a3599/Arduino_UNO_Joystick_HID_firmware.html)

Andrew Brown's [instructable](http://www.instructables.com/id/Use-an-Arduino-with-an-N64-controller/)
