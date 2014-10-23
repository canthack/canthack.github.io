---
author: tris
comments: true
date: 2010-12-13 14:08:52+00:00
layout: post
slug: lcd-now-playing-screen
title: LCD Now Playing Screen
wordpress_id: 151
categories:
- Hardware
- Programming
---

[caption id="attachment_152" align="alignright" width="300" caption="The LCD Now Playing Display"][![LCD Now Playing](http://canthack.org/uploads/IMG_2115-300x200.jpg)](http://canthack.org/uploads/IMG_2115.jpg)[/caption]For Hack Night #2, I wanted to create a "Now Playing" LCD display to integrate with the [lpHack ](http://canthack.org/2010/11/lpd-music-hack-how-its-done/)music server that we worked on at Hack Night #1. The resulting project is another real _Can't Hack_ team effort as it builds on and relies upon the music server, the music submission shell script, and the lpHack [Client GUI project](http://canthack.org/2010/11/lphack-client-in-lazarus/).




## The LCD


A [PIC18F2431](http://ww1.microchip.com/downloads/en/DeviceDoc/39616d.pdf) was used, with PORTB (8 bits) connected to the input of the [TS2040-1](http://www.displaybras.com.br/pdf/DAN%20202004A-1.pdf) 80 character alphanumeric LCD device itself. The LCD is running in 8-bit mode, so all the PIC needs to do is send ASCII characters at the correct baud rate. Pins 3,4 and 5 of PORTC are connected to the control pins on the LCD device, and finally pins 6 and 7 of PORTC are connected via a [MAX232](http://en.wikipedia.org/wiki/MAX232) to a serial port (with all relevant capacitors and resistors in place).

An RS232 serial port was used as the communication channel between the PC and the board. The serial port used was a USB to serial converter and appears in this case under `/dev/ttyUSB0` in Linux.


## Driving the LCD


The [lpHack Client GUI](http://canthack.org/2010/11/lphack-client-in-lazarus/) from Hack Night #1 outputs the play queue into a file _/tmp/lpPlaylist.log_, so the obvious choice was to run this and create a script to read the first line from this file and send it down the serial port to the LCD board.

This was performed by a Python script using the `serial` and `io` imports to facilitate reading from files and writing to serial ports.

At a future Hack Night I plan to hard-wire this circuit onto some veroboard.

For the complete code for all components of the LCD display, check after the break...

<!-- more -->

Microchip C18 code for the PIC18F2431 used to drive the LCD display. Programmed using Microchip MPLAB 8 under Windows, through a ICD2 debugger:

[sourcecode language="cpp"]

//enable pin on the lcd
#define EN_ON LATCbits.LATC5 = 1
#define EN_OFF LATCbits.LATC5 = 0

//read/write pin on the alphanumeric
#define RW_ON LATCbits.LATC3 = 1
#define RW_OFF LATCbits.LATC3 = 0

#define RS_ON LATCbits.LATC4 = 1
#define RS_OFF LATCbits.LATC4 = 0

void main (void);
void Delay(int i);
void clearDisplay(void);
void returnHome(void);
void setEntryMode(char Dec, char Shifted);
void SetDisplayStatus(char on);
void SetUp(void);
void clock(void);
void setDDRAM(char c);

void Delay(int i)
{
  while(i--);
}

void clearDisplay()
{
  //Clears the alphanumeric display

  RW_OFF;
  RS_OFF;
  PORTB = 0x01;
  clock();
}

void returnHome()
{
  //Return to beginning of line
  RW_OFF;
  RS_OFF;
  PORTB = 0x02;
  clock();
}

void setEntryMode(char Dec, char Shifted)
{
  RW_OFF;
  RS_OFF;
  PORTB = 0x04 | (Dec ? 0x02 : 0x00) | (Shifted ? 0x01 : 0x00);
  clock();
}

void SetDisplayStatus(char on)
{
  RW_OFF;
  RS_OFF;
  if (on)
    PORTB = 0x0F;
  else
    PORTB = 0x00;
  clock();
}

void SetUp(void)
{
  RW_OFF;
  RS_OFF;
  PORTB = 0x14;
  clock();
}

void writeChar(char c)
{
  RW_OFF;
  RS_ON;
  PORTB = c;
  clock();
}

void functionSet(void)
{
  RW_OFF;
  RS_OFF;
  PORTB = 0x38;
  clock();
  Delay(300);
  PORTB = 0x38;
  Delay(200);
  clock();
}

void clock(void)
{
  Delay(80);
  EN_ON;
  Delay(80);
  EN_OFF;
}

void setDDRAM(char c)
{
  RS_OFF;
  RW_OFF;
  PORTB = 0x80 | c;
  clock();
}

void main (void)
{

  Delay(500);

  TRISB = 0x00;  //port B is output for the 8 bit lcd
  TRISC = 0xC0;  //port C all 0 except port 6 and 7 (for rs232)

  TXSTA = 0x80;  //set up UART port for RS232
  RCSTA = 0xB0;  //to 9600 baud, 8 bit, no parity
  BAUDCON = 0x02;
  SPBRG = 0x0C;
  SPBRGH = 0x00;

  //internal oscillator to 8 MHz
  OSCCONbits.IRCF0 = 0x01;
  OSCCONbits.IRCF1 = 0x01;
  OSCCONbits.IRCF2 = 0x01;

  EN_OFF;

  functionSet();
  SetDisplayStatus(1);
  clearDisplay();
  setEntryMode(1,0);
  returnHome();
  SetUp();
  setDDRAM(0);

  while(1)
  {
    while(!PIR1bits.RCIF); //wait for data on serial

    //write data to lcd
    RCSTA = 0xA0;
    RCSTA = 0xB0;

    writeChar(RCREG);
  }
}
[/sourcecode]

Python code to read the playlist from the temp file and sent it to the LCD over the serial port. Run from the command line under Linux with the LCD board connected to `/dev/ttyUSB0` through a USB to serial adapter:

[sourcecode language="python"]
import serial;
import time;
import sys;
import io;

#open serial port for writing to the PIC
#powered lcd device board
ser = serial.Serial('/dev/ttyUSB0')
print "Writing to port " + ser.portstr

#initialise - blank the lcd
for i in range(1, 80+80):
	ser.write(' ')
	time.sleep(0.005)

while 1:
	#open playlist and read first line in
	playlist = open("/tmp/lpPlaylist.log", 'r')
	s = playlist.readline()
	playlist.close()

	#tidy up by removing non-printing characters
	s = s.replace("\t", '')
	s = s.replace("\n", '')

	print s

	#expand or crop to 80 characters (lcd size)
	l=len(s);

	if l < 80:
		s = (s[:80])
	else:
		for i in range(l, 80):
			s = s + ' '

	#lcd is interlaced - split into rows in
	#the order 1, 3, 2, 4
	s = s[0:20] + s[40:60] + s[20:40] + s[60:80]

	#write to the lcd
	for i in s:
		ser.write(i)
		time.sleep(0.005)

	time.sleep(5)

#close serial port
ser.close()
[/sourcecode]
