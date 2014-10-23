---
author: tris
comments: true
date: 2013-04-14 09:58:28+00:00
layout: post
slug: pipurr-the-remote-cat-interaction-server
title: PiPurr - The Remote Cat Interaction Server
wordpress_id: 1361
categories:
- Hardware
- Programming
- Software
---

[caption id="attachment_1381" align="alignright" width="300"][![PiPurr Server](http://canthack.org/uploads/pipurr-300x260.png)](http://canthack.org/uploads/pipurr.png) PiPurr Server[/caption]

All the best hacks and open-source projects come about by scratching personal itches. In fact when asked by friends and acquaintances "_How do I start with coding - what should I write?_", that is my usual answer - "_Find something you don't like or would like to improve and do that_".

This brings us to the idea of PiPurr, what I like to call the Remote Cat Interaction Server.

Whenever we go on holiday, we never really get to relax, because [Hannah](http://canthack.org/author/han/) is always asking me things like "_Do you think the cats are OK?_", "_What do you think the cats are up to?_" etc. PiPurr is the solution to this problem.

The goal is not just to be able to keep an eye on what the cats are doing when away from home, but also to interact and play with them remotely too.

PiPurr is a simple client-server solution. The [server](https://github.com/tristan2468/PiPurr-Server) runs on a Raspberry Pi, for low power consumption and provides the functionality - capturing images, dispensing treats, making sounds play from the speakers etc, and clients connect to this over HTTP and request those actions.

[caption id="attachment_1373" align="alignleft" width="168"][![PiPurr for Android](http://canthack.org/uploads/newui-168x300.png)](http://canthack.org/uploads/newui.png) PiPurr for Android[/caption]

The server is implemented as a small Python web server, using [HTTPServer](http://docs.python.org/2/library/basehttpserver.html) that, instead of serving static files from a directory, exposes a small number of URIs that correspond to those actions. This means that all that is required to access it is a browser. A summary of the actions are:



	
  * /cats.jpeg - Capture and return a photo from the webcam. Uses [OpenCV](http://opencv.org/).

	
  * /sound - Play the sound out the speakers to entice the cats over.

	
  * /treat - Dispense a cat treat - Currently in development, and will use a [PicoBorg](http://piborg.com/picoborg) stepper motor to drive a treat dispenser. Keep a look out for another blog post about this in the future.


The decision to use the (rather large and [fiddly to get working on Raspberry Pi](http://eduardofv.com/read_post/185-Installing-OpenCV-on-the-Raspberry-Pi)) OpenCV for image capture is down to the fact that in the future I am going to write a cat detector, and possibly other vision-based functionality.

A neat addition to the server is the [LedBorg](http://piborg.com/ledborg), which is an RGB LED add-on, set to flash different colours depending on what happens when requests come in. Red for 403s, 500s etc., green for 200 on the image URI and blue for 200 on the sound URI.

[caption id="attachment_1374" align="alignright" width="168"][![Sharing](http://canthack.org/uploads/share-168x300.png)](http://canthack.org/uploads/share.png) Sharing[/caption]

For ease of use, I also developed an [Android client](https://github.com/tristan2468/PiPurr-for-Android) that allows easy access to PiPurr whilst out and about. The app also allows for sharing of the captured images over email, Facebook etc.

I will be adding more functionality to the solution as time goes on. Some of the things I have planned are:



	
  * / - Implement a web UI to control it easier with a web browser.

	
  * /laser - Move a laser pointer or similar cat to about with motor control.

	
  * /video - Stream live video.


I plan to talk about this project at [BarCamp Canterbury](http://barcampcanterbury.com) on the 27th April 2013 if you would like to find out more, or have any cool ideas.
