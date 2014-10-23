---
author: edd
comments: true
date: 2010-12-30 23:14:50+00:00
layout: post
slug: interfacing-an-hd44780-lcd-screen-the-begginings-of-the-car-audio-system
title: Interfacing an HD44780 LCD Screen - The Beginnings of the Car Audio System
wordpress_id: 389
categories:
- Arduino
- Hardware
- Programming
---

Following my previous post Programming the Arduino in Pure C, I have had time over the Xmas holdiays to do some more experimentation.

[![HD44780 on AVR](http://canthack.org/uploads/lcd-avr-225x300.jpg)](http://canthack.org/uploads/lcd-avr.jpg)


## Setting the Scene


First lets look at what I am aiming to do. I **hate** taking CDs into my car:



	
  * They get scratched

	
  * Or they get lost

	
  * You have to ram them in the small glove compartment

	
  * You have to have mad cd-swap-whilst-driving skills


You get the idea... For a long time I wanted to make a digital audio system for my car. It must:

	
  * Play my existing Vorbis files

	
  * Be very low power

	
  * Be robust - Can handle being powered off in use

	
  * Boot quickly

	
  * Be small


I had half-arsedly tried using [mini-itx ](http://www.mini-itx.com/)and [soekris systems](http://www.soekris.com/) and decided that they don't check all the boxes.

So I'm going to try to piece a system together using an AVR microcontroller (and it may take years). Why? Because I want to learn as much as possible about the inner workings of such a system and build it from the ground up -- well, perhaps with my friends [avr-gcc](http://www.nongnu.org/avr-libc/) and [vim](http://www.vim.org).

What will I need?



	
  * Visual output device

	
  * Audio output device - [This](http://www.sparkfun.com/products/9736) can decode Vorbis over SPI.

	
  * Input device - A keypad will be fine.

	
  * Storage -Probably an SD card (dread writing filesystem driver)


So.. step one - Visual output device...


## Interfacing an LCD Device


I'm going to use a Hitachi HD44780 2x16 LCD module for this. These are cheap, easy to interface and have an awesome old-skool calculator look about them.

Without going into too much detail, it was enough to read the following links to get an idea of how these controllers work:



	
  * [HD44780 Data Sheet](http://www.sparkfun.com/datasheets/LCD/HD44780.pdf)

	
  * [Joshua Galloway's Breakdown](http://joshuagalloway.com/lcd.html)


My build looked like this:

[caption id="attachment_393" align="alignleft" width="300" caption="Breadboard View"][![Breadboard View](http://canthack.org/uploads/aelcd_example_bb-300x251.png)](http://canthack.org/uploads/aelcd_example_bb.png)[/caption]

[caption id="attachment_394" align="alignright" width="249" caption="Schematic View"][![Schematic View](http://canthack.org/uploads/aelcd_example_schem-249x300.png)](http://canthack.org/uploads/aelcd_example_schem.png)[/caption]

I decided to use 4 bit operation, as there are very few pins on the AVR, so we need to save as many as we can. This also leads us onto another important issue.

I am using 4 pins of PORTB for the LCD data bus. In the beginning I would use an 8 bit variable to accumulate the data value; by this i mean, zero PORTB and use bitwise OR to set bits. This works...

Until you want to use the other pins of PORTB for something else. By zeroing them, you will ruin whatever their purpose was. The solution? We have to read out the state of the port first and leave the pins we are not concerned about alone. The following C snippet may help understanding:

[sourcecode language="cpp"]
uint8_t data;

/* get original value of PORTB */
data = PORTB;

/* zero the 4 pins we are going to use for the LCD data bus */
data &amp;= 0xf0;

/* Set the 4 pins to whatever we want */
data |= some4bit_value;

/* write back to the port */
PORTB = data;
[/sourcecode]

Another caveat is that the Arduino bootloader initialisation process interferes with the LCD controller configuration, meaning that the LCD sometimes fails to initialise. As a workaround, I just wired the LCD logic power up to an output pin of the AVR and manually powercycled the LCD right after the program begins executing. Annoying because I lose another pin.


## Free Code


Once I had basic operation working, I started making the code suitably generic, for anyone to use. You can use any ports and pins you like, just so long as:



	
  * All of the data bus pins are next to each other on the same port.

	
  * All of the control bus pins are next to each other on the same port.


Unfortunately you wont be able to put both the data bus and the control bus on the same port. Timing is also customisable.

The code is available [here](http://theunixzoo.co.uk/wiki/doku.php?id=edd:eavr) and is free and open under the ISC license. Here is an example of how to use the library:

[sourcecode language="cpp"]
#include
#include
#include 

#include "ealcd.h"

/*
 * We will do a fresh reset of the lcd power, so that the arduino
 * initialisation will not screw up the lcd configuration
 */
#define LCD_P_POWER             (1 &lt;&lt; 3)
#define LCD_POWER_PORT          PORTB

void
demo_title()
{
        char                    rev[20] = "$Revision: 11 $", *c;

        /* clear screen and start at far left */
        ealcd_display_ctrl(1, 1, 1);
        ealcd_home();
        ealcd_clear();

        ealcd_set_ddram_addr(4);
        ealcd_put_string("EAVR LCD");

        ealcd_set_ddram_addr(67);
        ealcd_put_string("Version");
        for (c = rev; *c != ':'; c ++);
        for (; *c != '$'; c ++)
                ealcd_put_char(*c);

        _delay_ms(2000);
}
int
main(void)
{
        /* you still have to do data dirctions manually */
        DDRC = 0b00001111;
        DDRB = 0b00001111;

        /* power cycle lcd */
        LCD_POWER_PORT = LCD_POWER_PORT &amp; (~LCD_P_POWER);
        _delay_ms(1);
        LCD_POWER_PORT = LCD_POWER_PORT | LCD_P_POWER;

        /* set up lcd */
        ealcd_init();

        /* set number of lines and font */
        ealcd_function_set(2, 0);

        /* cursor on, blink, font 1 */
        ealcd_display_ctrl(1, 1, 1);

        while(1)
                demo_title();

        return (0);
}
[/sourcecode]


