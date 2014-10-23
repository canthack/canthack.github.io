---
author: tris
comments: true
date: 2011-03-11 13:51:15+00:00
layout: post
slug: getting-faster-usb-charging-from-an-in-car-charger
title: Getting Faster USB Charging from an In Car Charger
wordpress_id: 505
categories:
- Hardware
---

The other day I was driving to work with my brand new Google Nexus S Android phone on the dashboard, as usual. A new version of [Google Maps](https://market.android.com/details?id=com.google.android.apps.maps&feature=search_result) had just come out, and coupled with the fact that I had just got a new phone, I decided to test the navigation.

[caption id="attachment_533" align="alignright" width="154" caption="Google Nexus S"][![Nexus S](http://canthack.org/uploads/Google-Nexus-S-fronton-154x300.jpg)](http://canthack.org/2011/03/getting-faster-usb-charging-from-an-in-car-charger/google-nexus-s-fronton/)[/caption]

When I got to work, I noticed the battery level on my phone was lower than I would have expected, so I viewed the battery discharge graph, and was surprised to see that the battery charge had been depleting all the while I was navigating with the screen on, even though the Nexus S was connected to the in car USB port, and charging all the while. My in car USB port is rated at 1A, but something was surely up.

After some Googling, I came to the conclusion that the phone was thinking it was connected to a standard USB port, not an AC adapter, and was therefore limiting itself to only draw 500mA of current, irrespective of what the charger can deliver, to remain compliant with USB standards (where a computer USB port can only deliver a maximum of 500mA).

To confirm what I had suspected, I used the Nexus's diagnostic mode. To enter this mode, I simply tap the code `*#*#INFO#*#*` into the dialer. When the main diagnostic screen appeared, I clicked "Battery information" to see what source the Nexus _thought _it was connected to. Sure enough, when connected to the in car USB, this screen was reporting "USB", meaning that the phone is not able to take advantage of the 1A output of the car charger.

So what I wanted to do was make the phone think it was connected to an AC adapter. To do this, the data lines on the USB connector (D+ and D-) must be shorted together. this signals to the phone that it is not connected to a computer and then the phone will go into fast charging mode.

I then took a cheap micro USB cable and used a multimeter to check the connections on the cables, as we don't want to short out the power + and - lines, that would be really bad! Once the correct cables were determined, I simply cut them, and soldered them together, as shown in the image.

[caption id="attachment_509" align="alignright" width="300" caption="Shorting the data lines"][![USB](http://canthack.org/uploads/usb-300x223.jpg)](http://canthack.org/2011/03/getting-faster-usb-charging-from-an-in-car-charger/usb/)[/caption]

After putting the case back on the connector on the cable, I connected the phone back up to the car's USB port, and sure enough, the phone reported itself as connected to AC as the charging mode on the INFO Screen.

Testing this the next day, the battery graph showed a slight increase in charge over the course of the journey rather than the slight discharge of before, so the hack was a success.

So now I have a cable specifically for using in the car. I do not recommend using this to connect to a PC or other "real" USB port, just in case!
