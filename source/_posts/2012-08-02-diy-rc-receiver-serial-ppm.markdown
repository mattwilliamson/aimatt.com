---
layout: post
title: "DIY RC Receiver (Serial PPM)"
date: 2012-08-02 17:43
comments: true
categories: [Electronics, Multirotor, Arduino, Radio]
---

When I was first getting into multirotors, I didn't have an RC transmitter and receiver pair to control a quadcopter with. But I *did* have a couple [XBees](https://www.sparkfun.com/products/8664) and a [Saitek PC USB Joystick](http://www.amazon.com/gp/product/B0000AW9RE/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B0000AW9RE&linkCode=as2&tag=appdelinc-20). So, I made my own RC setup and I'll show you how.

{% youtube wR0TUg2AFl8 %}

# History


## Mark I: Initial Prototype

My first version was very rudimentary. It was an XBee Series 1 and an Arduino nano on the quadcopter side and on the computer side was the joystick, an [FTDI Basic](https://www.sparkfun.com/products/9716) and another XBee. The Ardunio nano was running a simple sketch which took serial commands in the form of 6 bytes in the range of 0-250 and a terminator byte, 254. The sketch interprets each of the first 6 bytes as a value to output on one pin, using the Arduino Servo library. To test it out, I plugged 6 servos into their respective pins and moved the joystick around and pushed buttons.


## Mark II: Out of range kill switch

The biggest thing I was worried about was stepping on the gas and my quadcopter flying out of range and then off forever, to be found by highway workers, or worse -- seeing on the news that "some mysterious device fell out of the sky and hit an unsuspecting person in the face". So, I added some code to check if the receiver has gotten an update in the last two seconds, and if not, kill the throttle and disarm. It was originally written to auto-land, but I felt that it was too dangerous for both bystanders and the quad itself to have the motors (with *blades*!) spinning for ~20 seconds after control has been lost.


## Mark III: Sum PPM

While chatting with some fellas and gals in the freenode #multiwii channel and I heard about "Sum PPM", also known as "Serial PPM". It's a version of [Pulse Position Modulation](http://en.wikipedia.org/wiki/Pulse-position_modulation), the PWM protocol used to control a servo. The length of a pulse (how long the line is high) determines the servo's position. The range is 1000 microseconds to 2000 microseconds. Which degrees the servo points to for each value is determined by whether it's a 180° servo, or a 360° model.

Sum PPM squeeze all of the channels onto one wire instead of one wire for each channel. The first pulse will be for the first channel, the second pulse for the second channel and so on. It knows which channel is which, by having an extra long pulse of 5000 or more microseconds after all the channels have pulsed. The pulse following the long "sync" pulse is channel 0. So a full datagram for 4 channels may look something like this:

    __-- 1000µs --___-- 1200µs --___-- 2000µs --___-- 1500µs --___-- 5100µs --__


The other big difference in this revision is I bought an [Arduino pro mini](https://www.sparkfun.com/products/11113) and soldered it directly to an [XBee breakout board](https://www.sparkfun.com/products/9132). This reduced the size by about 4x.


## Mark IV: Deadbug

I thought this would be the final version. I have a bunch of DIP ATmega328 chips, so I decided to solder one up [deadbug](http://en.wikipedia.org/wiki/Point-to-point_construction)-style. It worked great and was much smaller than an Arduino Nano, but looked like... well, a dead bug! It just kinda hung there inline with the wires and there wasn't really a nice way to keep it attached to the frame besides a rubber band. I also globbed it with hot glue to strengthen the connections and prevent shorts.

![Deadbug](https://github.com/mattwilliamson/aimatt.com/raw/gh-pages/images/posts/ppm-receiver/deadbug.jpg)



## Mark V: Yes, this many revisions!

The major difference here is that I decided to switch to using a [Roving Networks RN-XV Wifly Module](https://www.sparkfun.com/products/10822). It fits in an XBee footprint which means I don't have to change my receiver at all and I don't need the XBee or the FTDI adapter on the laptop. Wifi has similar range to a Series 1 XBee anyway.


Here's what the current model looks like:

![Receiver Top](https://github.com/mattwilliamson/aimatt.com/raw/gh-pages/images/posts/ppm-receiver/top.jpg)

![Receiver Bottom](https://github.com/mattwilliamson/aimatt.com/raw/gh-pages/images/posts/ppm-receiver/bottom.jpg)


# Usage


## Compatibility

I have tested it and it works on both [MultiWii](http://www.multiwii.com/) and [Aeroquad](http://aeroquad.com/) when the configuration is set for Futubu. It looks like this in MultiWii:

```c
#define SERIAL_SUM_PPM   ROLL,PITCH,THROTTLE,YAW,AUX1,AUX2,AUX3,AUX4 //For Robe/Hitec/Futaba
```


## How to make your own

I am currently considering designing a board which will fit an XBee pin-compatible radio and have a built in microcontroller if there is enough interest. But if you want to make one yourself, you'll need:

* [Saitek USB Joystick](http://www.amazon.com/gp/product/B0000AW9RE/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B0000AW9RE&linkCode=as2&tag=appdelinc-20)
* A [Wifly Module](https://www.sparkfun.com/products/10822)
* An [Arduino pro mini](https://www.sparkfun.com/products/11113)
* An [XBee explorer](https://www.sparkfun.com/products/9132) 
* 4 pins worth of [male header strip](https://www.sparkfun.com/products/116)
* The Arduino sketch at from [my github repo](https://github.com/mattwilliamson/Arduino-RC-Receiver/blob/master/serial_ppm_rx_ino/serial_ppm_rx_ino.ino)
* [Servo wire](https://www.sparkfun.com/products/8738), male-end (I think I cut mine off a dead servo)
* Soldering Iron/Solder
* Hot glue gun


The pins of the XBee explorer and the Arduino Pro Mini line up perfectly. With the Arduino Mini facing down, put the headers into the serial port (pins GND, VCC, RXI, TXO) and solder them in. I would put the short end of headers into the Arduino side. The pins will be sticking out of the bottom of the Arduino when you are done. This is a good time to upload the serial PPM sketch using an [FTDI Basic](https://www.sparkfun.com/products/9716).

Now, strip the ends of the male side of the servo cable. Then, with the Arduino Mini facing down, solder BLACK into GND on the Arduino, RED to VCC and WHITE to Arduino PIN 9. Make sure the wires are coming out of the bottom of the Arduino. This is so when we hot glue the boards together, the hot glue will hold the cable in place and reduce stress on the solder joints.

Flip the Arduino over. Slide the explorer on the headers, facing up. Make sure GND matches up with GND on both boards. Put some hot glue between the boards to help them stick together. Solder the explorer onto the headers. They are now permanently joined! You should have something like this:

![Receiver Bottom](https://github.com/mattwilliamson/aimatt.com/raw/gh-pages/images/posts/ppm-receiver/side.jpg)


## Use it!

Set up your XBee or WiFly chip and insert it into the receiver. Get the code from [joystick.py](https://github.com/mattwilliamson/Arduino-RC-Receiver/blob/master/joystick.py) and make sure the [pygame](http://www.pygame.org/install.html) python module is installed. 

![Plugged In](https://github.com/mattwilliamson/aimatt.com/raw/gh-pages/images/posts/ppm-receiver/pluggedin.jpg)

If you are using a WiFly, make sure you set it up to do Ad-Hoc WiFi network and then connect to it from your computer once it is plugged in. Make sure the following line is uncommented when using a WifFly:

    SERIAL_PORT = 'socket://169.254.1.1:2000'

![Wifi](https://github.com/mattwilliamson/aimatt.com/raw/gh-pages/images/posts/ppm-receiver/wifi.png)

Plug the receiver into your quadcopter. If you are running MultiWii, the signal (white wire) should be connected to `DIGITAL 2` on an ATmega328 Arduino or `DIGITAL 19` on a Mega.

Plug in your joystick and run `joystick.py`. It may take a minute to establish an IP address after connecting and you may see "no route to host". Keep trying until it works. Once it does, you'll see the values being sent to the receiver printed out every couple seconds to make sure it's still alive. And you should be getting PPM output on your quad.

![joystick.py](https://github.com/mattwilliamson/aimatt.com/raw/gh-pages/images/posts/ppm-receiver/console.png)

The receiver status LED will blink until it begins receiving commands and then will be solid.

-----


Hve Fun! Here's a few "flights" using one of the early versions:

{% youtube _Q5dbNigOiQ %}

Let me know in the comments if designing a custom board for purchase is something you are interested in.
