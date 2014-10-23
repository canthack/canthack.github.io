---
author: static-void
comments: true
date: 2014-04-06 03:03:00+00:00
layout: post
slug: a-new-mod-for-mega-drive-60hz-colour-correction
title: A new mod for Mega Drive 60Hz colour correction
wordpress_id: 1419
---

A well known difference between European and US/Japanese Mega Drives is that European models run with a 50Hz display update while US/Japan models get a 60Hz update. This is because US and Japanese TVs use the NTSC video standard at 60Hz, and here in Europe we use PAL at 50Hz.

What this means is that most games actually run slower on European consoles, which sucks if you want to play the games as they were originally intended to be played.

Fortunately for us, some clever clogs discovered that you can actually switch the console from 50Hz to 60Hz by changing an input on one of the chips inside the device from 0V to 5V. You generally want to be able to actually switch between 50 and 60Hz rather than fixing your console as one or the other, because some games were designed for the European consoles and others for the US/Japanese.

Adding a switch is pretty straightforward:

  1. Cut the trace that runs just above resistor R73
  2. Solder a wire to the right hand side of the cut trace (white/blue)
  3. Solder wires to the nearby sources of 0V (black) and 5V (red)
  4. Add a switch to give the white/blue wire 0V or 5V
[caption id="" align="aligncenter" width="403"]![](http://canthack.org/uploads/4.jpg) Adding a switch to a MD2 to choose between 50 and 60Hz[/caption]

## Welcome to the 60s

So far so good, we can switch our MD to 60Hz and enjoy the games as they were intended - huzah! But, as is often the case in life, there's a catch. Switch your MD to 60Hz and, unless your TV actually is black and white only, you're going to notice that your MD is only sending out a black and white picture. Apparently my great Grandfather used to really swear at his black and white TV when he was watching the snooker, and when I loaded up Sonic and his hair was grey instead of blue I knew exactly how he felt. Shit man, what happened!?

To generate a composite signal for your TV, the Mega Drive takes the RGB signal its video circuitry produces, and passes it to a composite video encoder chip. The encoder chip has a clock input that it uses as the carrier frequency for the colour information in the composite signal it outputs. This colour carrier actually has to be a very precise frequency; for PAL systems it is 4.43361875MHz, while NTSC systems require 3.579545MHz. If the frequency is off by more than a small amount, the TV wont pick up the colour information at all and you get a black and white picture.

To generate this frequency, an NTSC Mega Drive takes its system clock of 53.6931Mhz, and divides it by 15 to get the required 3.57954Mhz signal. A PAL Mega Drive instead has a system clock of 53.203Mhz, which it divides by 12 to get 4.433583Mhz, close enough to the correct carrier frequency for PAL. So here we have the crutch of the problem: when you tell the PAL Mega Drive to run at 60Hz, it thinks it is an NTSC model and sets the clock divider to divide by 15. This gives a carrier frequency of 53.203/15 = 3.546867MHz, which is not a correct colour carrier for NTSC or PAL, so you get a black and white picture.

## More modding

Well of course, this is also a well known issue. The easiest way to fix it is to buy an RGB cable and connect your MD to your TV with that. However, if you're like me, it will itch at you that there's a flaw in your system... or maybe your TV doesnt have an RGB input or something. Whatever the reason, if you want to find out how to fix this properly you fire up the all knowing google and discover there are loads of guides out there for how to fix this, great, we're saved!

Wrong. Well, for me anyway. Let me explain. The idea behind the fix is pretty simple, what we need to do is generate the 4.43361875MHz clock signal the video encoder needs, then cut the trace that supplies it with the system generated clock and feed our new signal in instead. However, this is harder than it sounds, because you need to actually generate that clock. Many of the guides available on the net tell you to get a 4 pin crystal oscillator like the one below:

[caption id="" align="aligncenter" width="280"]![](http://www.mpja.com/images/19080.jpg) 4 pin oscillator[/caption]

These are really neat little devices, you connect them up to ground and 5V, and they have a pin that outputs the frequency written on the top of the can. Now go find one that runs at 4.43361875MHz. Any luck? Didn't think so. These bastards are damn near impossible to find, and when you do the price is stupid (£30, WTF?). I guess since no one uses composite video any more, they don't make them any more. Well that sucks.

**Update: **User fluxcore over on the assembler games forums pointed me to a cheap source of PAL oscillators - D'oh! Anyway, this page still describes a fairly cheap way to achieve the same thing. If you want to go the oscillator route though, try here: [http://www.aliexpress.com/item/10PCS-Lot-4-433619M-Crystal-Oscillator-4-433619MHz-13-21-13-21-5-08mm-Square-active/949416078.html](http://www.aliexpress.com/item/10PCS-Lot-4-433619M-Crystal-Oscillator-4-433619MHz-13-21-13-21-5-08mm-Square-active/949416078.html)

## Oscillator circuits

The next logical thought is, "can I make my own oscillator", and the answer is, in theory at least, yes, and a few of the guides and many forum posts explain how to do it.

You basically get an actual quartz crystal of the right frequency (which is actually not that easy for PAL), and make a little oscillator circuit with some resistors and capacitors that should generate the right frequency clock signal. Most people use Pierce oscillators, you have to tune them, and they are a bitch. The internet is full of people trying and failing to make these things work. Somehow, through voodoo, luck, or genius, some people have actually made them work, but the gods alone know how because I've tried many times to make a number of different oscillator designs and none of the worked. The problem is that 4.43361875MHz is really very fast, and to tune an oscillator circuit properly you need very small capacitances. So if you make a little circuit running at that speed on breadboard, the parasitic capacitance of the board is going to screw up the tuning. I've tried making them with wires and deadbugging too, I just can't make it work.

## Time division

At this point I basically gave up for a while. I consider myself fairly good at electronics, certainly not bad, and I had failed miserably five or six times to make this work. However, after coming back to it recently (and failing once more) I had an idea. Why mess about with all these impossible arcane analogue circuits when we can handle the problem another way completely - we know that if we divide the system clock by 12 we get the right frequency. So let's make a clock divider instead!

My A level electronics project was based on clock dividers, and they're very straightforward to build out of flip flops. Division by 2 is easy, just a single D-type flip flop as follows:

[caption id="" align="aligncenter" width="383"]![](http://www.eecs.tufts.edu/~dsculley/tutorial/flopsandcounters/DivideBy2Counter.jpg) Divide by 2 counter[/caption]

From there you need to do division by 6, which is a bit more complex but can be achieved with a Johnson counter:

[caption id="attachment_1423" align="aligncenter" width="300"][![Mod 6 Johnson counter](http://canthack.org/uploads/JohnsonCounter-300x157.png)](http://canthack.org/uploads/JohnsonCounter.png) Mod 6 Johnson counter[/caption]

And there it is. All that remained was to find some fast enough flip flops. I found the 74ACT175, quad D-types that will run up to 150MHz. A slight shame is that the divide 2 and divide 6 parts of the circuit need separate clock inputs, so two chips are needed even though only 4 D-types are actually used. The circuit itself is really simple:

[![Div12 Circuit](http://canthack.org/uploads/Div12-Circuit-1024x576.jpg)](http://canthack.org/uploads/Div12-Circuit.jpg)

I decided to build it without using any actual breadboard to make it really small. Below are some pictures, I'm afraid they aren't great as I was having trouble getting my camera phone to focus.

[gallery ids="1425,1426,1427"]

The picture below shows where the signal is fed in to the encoder. You can just see where I've cut the trace, it's the middle one of the three I've exposed. Note that I took this capacitor off later, as I realised it wasn't at all necessary (it was a remnant from an earlier attempt with an oscillator circuit).

[![encoder input](http://canthack.org/uploads/encoder-input-1024x576.jpg)](http://canthack.org/uploads/encoder-input.jpg)

This low quality pic shows where you get the clock source from:

[![Clock source](http://canthack.org/uploads/clock-source-300x168.jpg)](http://canthack.org/uploads/clock-source.jpg)

Here's the whole thing just before I put the lid back on:

[![Finished mod](http://canthack.org/uploads/finished-1024x576.jpg)](http://canthack.org/uploads/finished.jpg)

One neat thing about the Mega Drive 2 is that there's this hole at the back that is basically ready made for a toggle switch, so I didn't have to drill a hole in the case or anything:

[![Toggle switch](http://canthack.org/uploads/toggle1-300x168.jpg)](http://canthack.org/uploads/toggle1.jpg) [![toggle2](http://canthack.org/uploads/toggle2-1024x576.jpg)](http://canthack.org/uploads/toggle2.jpg)

This should work on the Mega Drive 1 as well. You should also be able to do a similar mod on an NTSC Mega Drive too, but your circuit will need to divide by 15 instead of 12.

Finally, thanks to all those out there who wrote guides and gave information on forums that helped me figure this out. I hope this in turn can help some other people!
