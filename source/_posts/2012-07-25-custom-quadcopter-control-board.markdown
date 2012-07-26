---
layout: post
title: "Custom Quadcopter Control Board"
date: 2012-07-25 18:39
comments: true
categories: [Electronics, Multirotor, Arduino]
---

After seeing hobbyist-oriented batch PCB services, such as Sparkfun's BatchPCB and Laen's OSHPark, I decided instead of squeezing an Arduino Mega and a DFRobotics shield into a plastic food container, I would make my own, compact PCB and send it out to be manufactured. To my dismay, it all worked on the first try!

![PCB Unpopulated](https://github.com/mattwilliamson/aimatt.com/raw/gh-pages/images/posts/quadcopter/quad_pcb_unpopulated.jpg)


## Step 1: Establish requirements

There are some measures to cut cost and complexity (and thus board size) that were taken due to the nature of a multirotor. First, we can get rid of the voltage regulator circuit, since we will be powering the board with a 5v [BEC](http://en.wikipedia.org/wiki/Battery_eliminator_circuit) such as [this one](http://www.hobbyking.com/hobbyking/store/__4319__TURNIGY_3A_UBEC_w_Noise_Reduction.html) or powered by an [ESC](http://www.hobbyking.com/hobbyking/store/__61__182__ESC_UBEC_VR-All_Speed_Controllers.html) motor controller with a BEC built in. This allows us to remove a voltage regulator and two electrolytic capacitors. 

I also left out the FTDI USB serial converter. Thie probably cuts $10 off the board and we can just use a [standard USB-TTL converter](https://www.sparkfun.com/products/9716) for all the boards to program them.


## Step 2: Get it working on the breadboard

I've done breadboard Ardunio projects a bunch of times, so I'm pretty familiar with the process. I recommend trying it out, if you haven't already. Here's a good link for instruction: [http://arduino.cc/en/Main/Standalone](http://arduino.cc/en/Main/Standalone). I chose to use the [MultiWii](http://www.multiwii.com/) firmware for the quadcopter, which just needs 2 lines for I2C sensors, 1 line for Serial PPM from an RC receiver and 1 line for each rotor to control the motors. 

Next, I needed to map out the pins. 

For an ATmega328 microcontroller, which I chose to use for this project, the pins are as follows based on the diagrams from [MultiWii](http://www.multiwii.com/connecting-elements):

* Arduino Pin 3 -> Motor 1
* Arduino Pin 10 -> Motor 2
* Arduino Pin 9 -> Motor 3
* Arduino Pin 11 -> Motor 4
* Arduino Pin 2 -> Serial PPM Receiver
* Arduino Analog Pin 4 -> Sensor I2C Bus SDA (data)
* Arduino Analog Pin 5 -> Sensor I2C Bus SCL (clock)

Then I needed to map these pins to the ATmega328's pins, since Arduino's pins don't match the microcontroller's. So I used the following image, snagged from the [Arduino.cc](http://arduino.cc) site.

![Arduino Pins](https://github.com/mattwilliamson/aimatt.com/raw/gh-pages/images/posts/Atmega168PinMap2.png)


## Step 3: Pick out the parts

I bought all the crystals, capacitors, resistors, LEDs, headers and buttons on eBay, in quantities of between 20 and 100 each. For small quantities, I use Sparkfun. For the ATmega328P-AU, the [SMT](http://en.wikipedia.org/wiki/Surface-mount_technology) version of the microcontroller of the Arduino Uno, I went to [Mouser](http://uk.mouser.com/ProductDetail/Atmel/ATMEGA328P-AU/?qs=K8BHR703ZXjB9uTYboq2G8iArio/9fOg) which gives discounts with higher quantities. Each board needs:

* 2x [330 Ohm resistors](https://www.sparkfun.com/products/8377) for LEDs
* 2x 2.2 kOhm resistors for I2C bus
* 1x [momentary push button](https://www.sparkfun.com/products/97)
* 1x 0.1" 2x5 shrouded header for ICSP
* 1x [16mhz crystal oscillator](https://www.sparkfun.com/products/536)
* 1x [3mm red LED](https://www.sparkfun.com/products/533)
* 1x [3mm green LED](https://www.sparkfun.com/products/9650)
* [0.1" male headers](https://www.sparkfun.com/products/116)
* [0.1" female headers](https://www.sparkfun.com/products/115)
* 2x 22pF ceramic capacitors
* 1x [10DOF IMU sensor board](http://www.goodluckbuy.com/10dof-l3g4200dadxl345hmc5883lbmp085-nine-axis-imu-module.html)

I hooked it all up to a breadboard to test out with a through-hole version of the ATmega328 to make sure it was all working before doing the design.


## Step 4: Design the board

Now that I'd figured out to get all the parts working together, it was time to figure out how do design a PCB. I read [Sparkfun's EAGLE tutorial](http://www.sparkfun.com/tutorials/108) a couple times to let it sink in. Then, I went and downloaded the [free version of EAGLE](http://www.cadsoftusa.com/) and got started.

First, I laid out the schematic, which was made **so much easier** thanks to [Sparkfun's parts library](https://github.com/sparkfun/SparkFun-Eagle-Library).

![Schematic](https://github.com/mattwilliamson/aimatt.com/raw/gh-pages/images/posts/quadcopter/quad_pcb_schematic.png)

Then I roughly laid out the board, following the tutorial and just used EAGLE's autorouter to route the traces.

![Layout](https://github.com/mattwilliamson/aimatt.com/raw/gh-pages/images/posts/quadcopter/quad_pcb_layput.png)


## Step 5: Fabrication

I was looking into various hobbyist PCB fabrication services and I was liking the DorkbotPDX service the most when I realized it had been just re-done and had become [OSHPark.com](http://oshpark.com/). Since then, I've basically been using that site as a Gerber file viewer to see how my design will look on a final board. When I finally got to a point where I liked the board, I submitted my board and paid the $30 to have it fabricated and shipped to me. It took about 3 or 4 weeks to get them, but it was like Christmas when I did! 

![OSHPark](https://github.com/mattwilliamson/aimatt.com/raw/gh-pages/images/posts/quadcopter/pcb_order.png)


## Step 6: Soldering

I'd never soldered SMT before, but Sparkfun [has a tutorial for that](http://www.sparkfun.com/tutorials/107), too. I laid down the ATmega328 on it's pads and soldered one corner to tack it down. Then I laid globs of solder down on each side and used solder wick to pick up the excess. Since I didn't have flux, it was very frustrating to do at first, but I finally got the hang of it. Looking under a magnifying glass, it looked like there were no solder bridges, so I crossed my fingers and hoped all the pads were making contact with the pins and moved on.

Next, I put in all the through-hole components in and soldered them. Nothing interesting there. Then I soldered the headers in, one at a time. They are tricky since they wiggle and end up crooked. Sparkfun's parts library has some "lock" headers which have the holes jagged, for a tighter fit, and I wish I had used them.


## Step 7: Programming

Time for the fun part. I bought an [USBasp](http://www.fischl.de/usbasp/) from ebay to program AVR microcontrollers which is why I used the 2x5 shrouded headers for ICSP. I plugged it into the computer and the board and once again crossed my fingers. The power LED didn't light up. Dang! I decided to try to burn the Arduino firmware on there anyway. I launched the Arduino IDE and hit the "Burn Bootloader" button and the status LED began flashing! Yay! 

I wondered what was wrong with power LED, so I took another LED out of the bag and touched the leads to the pads, thinking I had inserted the LED backwards, and it lit up. I checked the orientation and the LED was in the right way, but was either defective or burned up from soldering too long. I sucked the solder out and put the new LED and everything was operational with the default Arduino blinky sketch. 

It was time to put MultiWii on. I downloaded the Arduino MultiWii project and customized the config to match up with the hardware and used my Sparkfun FTDI Basic to program it. Since I did not implement auto-reset, I had to hold the reset button until the Arduino IDE says it is uploading to the board. 

I then launched the MultiWiiConfig application on my computer and saw the orientation of the sensors were wrong, so I fiddled with them for about and hour, re-flashing every time I tested a change. Once the orientation was correct, I decided it was time for a test.


## Step 8: Installation

I hot-glued some standoffs on the plastic container and screwed the board onto the standoffs. Plugged in the BEC for power, plugged in the motors (easy thanks to the silkscreen) and plugged in the serial PPM receiver. Easy as pie, compared to using an Arduino where you have to constantly check out the MultiWii site to figure out which pins were which.

![PCB Installed](https://github.com/mattwilliamson/aimatt.com/raw/gh-pages/images/posts/quadcopter/pcb_installed.jpg)

Then I plugged in the battery and, amazingly, I heard beeps from all 4 ESCs. Success! 

I figured I had to make sure they were all working, so I armed to motors and all spun up. I took it out back and practiced my hover until my battery died.



This was my first PCB and my first quadcopter, though I have had the copter for a while now and I was able to get it working. If I could design a PCB and have it work on the first try, anyone can. Hobbyist electronics has really come a long way in the past few years. 

Thank you to the open source community and a *huge* thank you goes out to [Sparkfun](http://sparkfun.com) for sharing all of their secrets with the general public.



As a side note, I had a Hawaiian themed party last week and we have a left over fake parrot. I figured it could help me keep track of the orientation of my quadcopter instead of forgetting which way it's facing. Here's how it looks:


![PCB Installed](https://github.com/mattwilliamson/aimatt.com/raw/gh-pages/images/posts/quadcopter/quadcopter_parrot.jpg)
