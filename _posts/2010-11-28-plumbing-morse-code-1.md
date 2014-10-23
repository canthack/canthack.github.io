---
author: mex
comments: true
date: 2010-11-28 17:02:29+00:00
layout: post
slug: plumbing-morse-code-1
title: Plumbing – Morse Code 1
wordpress_id: 9
categories:
- Arduino
- Programming
---

[![](http://farm6.static.flickr.com/5005/5211392709_7f23f6d058_m.jpg)](http://farm6.static.flickr.com/5005/5211392709_7f23f6d058_z.jpg)Plumbing is a runtime system and libraries for the Arduino that lets you write concurrent application using the Occam programing language.

This is my first hack using plumbing, it is a Morse Code encoder, that blinks the built in LED on my Arduino.

I am planning to do a follow up post to this with a Morse Code decoder.

For more information on Plumbing see [http://concurrency.cc/](http://concurrency.cc/)

See after the break for the code.

<!-- more -->

display.letter is the core of the code.  It takes a letter, looks up the sequence of flashes from the array data then sends pulses to switch the LED on and off.

Printing a String is handled by "display.word".  It simply splits the string up into a sequence of letters which are sent to display.letter.  The clever thing with this is it runs in parallel with display.letter.

[sourcecode language="sql"]
#INCLUDE "plumbing.module"

VAL INT DOT IS 400:
VAL INT DASH IS 800:
VAL INT NONE IS 0:

VAL INT DELAY.GAP IS 200: --GAP between dots and dashs
VAL INT DELAY.SPACE IS 1000: --Gap between chars

VAL [][]INT data IS
[ [ DOT,  DASH, NONE, NONE ], --a
  [ DASH, DOT,  DOT,  DOT  ], --b
  [ DASH, DOT,  DASH, DOT  ], --c
  [ DASH, DOT,  DOT,  NONE ], --d
  [ DOT,  NONE, NONE, NONE ], --e
  [ DOT,  DOT,  DASH, DOT  ], --f
  [ DASH, DASH, DOT,  NONE ], --g
  [ DOT,  DOT,  DOT,  DOT  ], --h
  [ DOT,  DOT,  NONE, NONE ], --i
  [ DOT,  DASH, DASH, DASH ], --j
  [ DASH, DOT,  DASH, NONE ], --k
  [ DOT,  DASH, DOT,  DOT  ], --l
  [ DASH, DASH, NONE, NONE ], --m
  [ DASH, DOT,  NONE, NONE ], --n
  [ DASH, DASH, DASH, NONE ], --o
  [ DOT,  DASH, DASH, DOT  ], --p
  [ DASH, DASH, DOT,  DASH ], --q
  [ DOT,  DASH, DOT,  NONE ], --r
  [ DOT,  DOT,  DOT,  NONE ], --s
  [ DASH, NONE, NONE, NONE ], --t
  [ DOT,  DOT,  DASH, NONE ], --u
  [ DOT,  DOT,  DOT,  DASH ], --v
  [ DOT,  DASH, DASH, NONE ], --w
  [ DASH, DOT,  DOT,  DASH ], --x
  [ DASH, DOT,  DASH, DASH ], --y
  [ DASH, DASH, DOT,  DOT  ]] :--z

--* Displays a single letter
-- @param letterChan a channel containing letters to print
-- @param pulse Signal to flip to turn light on and off.
PROC display.letter (CHAN BYTE letterChan, CHAN SIGNAL pulse!)
  [4]INT letterData:
  BYTE letter:
  WHILE TRUE
    SEQ
      letterChan ? letter
      letter := letter - 'a'
      letterData := data[INT letter]
      SEQ i = 0 FOR SIZE letterData
        CASE letterData[i]
          DOT, DASH
            SEQ
              pulse ! SIGNAL
              delay (letterData[i])
              pulse ! SIGNAL
              delay (DELAY.GAP)
          ELSE
            SKIP
      delay(DELAY.SPACE)
:

--* Displays a String
PROC display.word(CHAN INT::[]BYTE word, CHAN BYTE letters)
  WHILE TRUE
    INT count:
    [255]BYTE str:
    SEQ
      word ? count::str
      SEQ i = 0 FOR count
        IF
          (str[i] >= 'a') AND (str[i] <= 'z')
            letters ! str[i]
          TRUE
            SKIP
:

PROC main()
  CHAN BYTE letters:
  CHAN SIGNAL pulse:
  CHAN INT::[]BYTE words:
  PAR
    display.letter(letters, pulse)
    pin.toggle(13, LOW, pulse)
    display.word(words, letters)
    WHILE TRUE
      VAL []BYTE word1 IS "helloworld":
      SEQ
        words ! (SIZE word1)::word1
:
[/sourcecode]
