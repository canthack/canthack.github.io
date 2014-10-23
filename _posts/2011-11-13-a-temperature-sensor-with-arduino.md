---
author: tris
comments: true
date: 2011-11-13 18:45:06+00:00
layout: post
slug: a-temperature-sensor-with-arduino
title: A Temperature Sensor with Arduino
wordpress_id: 429
categories:
- Arduino
- Hardware
---

After getting the [Arduino](http://arduino.cc/) Uno for Christmas last year, I set about doing what every geek does after getting bored of the usual blinky blinky pin 13 stuff, and started thinking about what I could do with it that was _cool_, and maybe even _useful_.

I decided to make a temperature sensor, using the cheap and easy-to-use [LM35](http://www.national.com/mpf/LM/LM35.html#Overview) integrated circuit, which is a compact package that looks just like a transistor.

[caption id="attachment_1039" align="alignleft" width="150" caption="LM35"][![LM35](http://canthack.org/uploads/lm35-150x150.jpg)](http://canthack.org/2011/11/a-temperature-sensor-with-arduino/lm35/)[/caption]

It offers a linear 10mV per degree C voltage change with temperature and so requires minimal mathematics to covert the analogue input into a temperature reading.

The current temperature, and a running minimum and maximum are displayed on an LCD display (using the [LiquidCrystal Library](http://arduino.cc/en/Reference/LiquidCrystal)), with the pushbutton allowing the maximum and minimum to be reset. The temperatures are calculated using a circular buffer providing a running mean value over 10 samples.

[caption id="attachment_1025" align="alignright" width="300" caption="SD card hack"][![SD card hack](http://canthack.org/uploads/sd1-300x160.jpg)](http://canthack.org/2011/11/a-temperature-sensor-with-arduino/sd1/)[/caption]

The project also writes the temperature values to an SD card using the [SD Library](http://arduino.cc/en/Reference/SD), which was attached via an awesome hack I came up with involving soldering a pin header directly to it, although in this case it's a micro-SD adaptor to allow for changing the card more easily. The pinout is almost exactly lined up to make this hack work!



[caption id="attachment_1030" align="alignleft" width="300" caption="Temperature Sensor"][![Temperature Sensor](http://canthack.org/uploads/circuit-300x279.jpg)](http://canthack.org/2011/11/a-temperature-sensor-with-arduino/circuit/)[/caption]

What I like about this project is that it combines all the usual cool things a beginner would want to do with their Arduino. Sensor reading, LCD screen output, SD card read/write, and button capture (which will be done with an interrupt, when I get a chance to do it).

The code, and Fritzing schematic, can be found on my [GitHub page](https://github.com/tristan2468/ArduinoTemperatureSensor).
