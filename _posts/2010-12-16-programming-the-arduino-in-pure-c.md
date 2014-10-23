---
author: edd
comments: true
date: 2010-12-16 12:39:45+00:00
layout: post
slug: programming-the-arduino-in-pure-c
title: Programming the Arduino in Pure C.
wordpress_id: 253
categories:
- Arduino
- Hardware
- Programming
---

![](http://canthack.org/uploads/arduino-150x150.jpg)For hack #2, I started looking at how I could program an Arduino; an affordable hobby development board which uses an Atmega AVR microcontroller. More information [here](http://www.arduino.cc/).  We add a twist by not using the Arduino IDE and instead using the old faithful [text editor](http://vim.org).



## Stuff You Need







  * [Arduino](http://www.arduino.cc/) - I was using a [Duemilanove](http://www.arduino.cc/en/Main/ArduinoBoardDuemilanove)


  * [AVR-GCC](www.avrfreaks.net/AVRGCC)


  * [Avrdude](http://www.nongnu.org/avrdude/)


  * Your favorite text editor


  * 6 x LEDs


  * 6 x 220 Ohm Resistors


  * A breadboard


  * Cables


  * USB cable


  * [C knowledge](http://www.amazon.co.uk/C-Programming-Language-2nd/dp/0131103628/ref=sr_1_3?s=books&ie=UTF8&qid=1292502807&sr=1-3)



Total cost, £30 or so. All of the software is free and open.



## Implementation



On the web you will find the [Arduino port map](http://arduino.cc/en/Hacking/PinMapping168). From here you can figure out how to hook 6 LEDs (with a resisitor each) up to port B pins 0-5.  It's pretty simple stuff, you don't need a schematic. Now we need to make those leds-a-blinken.

What I needed then was a tutorial. Google quickly lead me to [Chris Kuethe's Arduino tutorial](https://www.mainframe.cx/~ckuethe/avr-c-tutorial/). As it turns out, ckuethe is the avr-gcc maintainer for OpenBSD -- small world. By following these tutorials, you can easily get up to speed. Half an hour later and I had a "Knight Rider Emulator" using 6 LEDs:

[sourcecode language="cpp"]
/* $CSK: lesson1.c,v 1.3 2009/05/17 06:22:44 ckuethe Exp $ */
/*
 * Copyright (c) 2008 Chris Kuethe <chris.kuethe@gmail.com>
 *
 * Permission to use, copy, modify, and distribute this software 
 * for any purpose with or without fee is hereby granted, 
 * provided that the above copyright notice and this permission
 * notice appear in all copies.
 *
 * THE SOFTWARE IS PROVIDED "AS IS" AND THE AUTHOR DISCLAIMS ALL 
 * WARRANTIES WITH REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED 
 * WARRANTIES OF MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL 
 * THE AUTHOR BE LIABLE FOR ANY SPECIAL, DIRECT, INDIRECT, OR 
 * CONSEQUENTIAL DAMAGES OR ANY DAMAGES WHATSOEVER RESULTING
 * FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN ACTION OF 
 * CONTRACT, NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF
 * OR IN CONNECTION WITH THE USE OR PERFORMANCE OF THIS SOFTWARE.
 *
 * Adapted as Knight Rider LEDs on an Arduino Duemilanove
 * (c) Edd Barrett 2010
 */

#include <avr/io.h>
#include <util/delay.h>

int main (void)
{
	uint8_t		leds = 1;

	/* set PORTB for output*/
	DDRB = 0xFF;

	while(1) {
		for (leds = 1; leds <= (1 << 5); leds <<= 1) {
			PORTB = leds;
			_delay_ms(200);
		}
		for (leds = (1 << 4); leds >= 2; leds >>= 1) {
			PORTB = leds;
			_delay_ms(200);
		}
	}

	return 0;
}
[/sourcecode]



## Uploading with AvrDude



But how do we upload the binary onto the storage of the arduino?
This part I had already figured out the night before and it took me quite some time, partially because I did not know what baud rate the Arduino was expecting. Once I had it figured, I adapted the sample makefile found on the [arduino wiki](http://www.arduino.cc/playground/FreeBSD/CLI) -- this one is for BSD make (suffix rules work a little differently to GNU make).

[sourcecode]
# tools
AVRDUDE=avrdude -F -V
OBJCOPY=avr-objcopy
CC=avr-gcc
RM=rm -f

# parameters
MCU=atmega328
F_CPU=16000000UL
BIN_FORMAT=ihex
PORT=/dev/cuaU0
BAUD=57600
PROTOCOL=arduino
PART=ATMEGA3290
CFLAGS=-Wall -Os -DF_CPU=$(F_CPU) -mmcu=$(MCU)
PROG=addr
SUDO=sudo

.SUFFIXES: .elf .hex

.c.elf:
	$(CC) $(CFLAGS) -o $@ $<

.elf.hex:
	$(OBJCOPY) -O $(BIN_FORMAT) -R .eeprom $< $@

.PHONY: all
all: ${PROG}.hex

${PROG}.hex: ${PROG}.elf

${PROG}.elf: ${PROG}.c

.PHONY: clean
clean:
	$(RM) ${PROG}.elf ${PROG}.hex

.PHONY: upload
upload: ${PROG}.hex
	${SUDO} $(AVRDUDE) -c $(PROTOCOL) -p $(PART) -P $(PORT) \
		-b $(BAUD) -U flash:w:${PROG}.hex
[/sourcecode]



## Conclusion



I was pretty impressed with how easy it really was to program an AVR on OpenBSD. The compiler is familiar - it's just GCC cross targeting, and the editor is familar -- in my case it's vim and not a bloated java GUI.

Ofcourse, this just scratches the surface. Expect further hacks later.

Thanks to ckuethe@ for his tutorial.
