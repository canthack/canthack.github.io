---
author: mex
comments: true
date: 2011-12-21 20:24:57+00:00
layout: post
slug: rickrolling-the-trolls
title: RickRolling The Trolls
wordpress_id: 1079
categories:
- Programming
- Unix
---

[![](http://canthack.org/uploads/rick-300x150.jpg)](http://canthack.org/uploads/rick.jpg)This post is inspired by a video a friend (Reashlin) sent to me (embedded below). In their company it is policy to lock computers when users are away from them, and if a machine is left unlocked it is customary to tell the supervisor in a "friendly" email from the account of the person in who left their machine unlocked.

Knowing this, Reashlin's colleague wrote a C# application that leaves the computer running whatever it was before until the mouse is moved, when it switches to a full screen RickRoll video with the machine locked.

A whole C# application to do this seemed a bit much, so I distilled the whole thing into the following 4 line bash script. The script is quite simple. It simply waits for input from /dev/input/mice then spawns full screen mplayer in the background, and xtrlock in the foreground.  xtrlock is a handy little program that captures all keyboard/mice events until the users password is entered, a bit like a transparent xlock.  There is an issue with this script if the user is proficient in keyboard short-cuts they might never touch the mouse... but that's something for another day.

If the others at canthack.org try and do [this](http://canthack.org/uploads/aliastroll.jpg) to me again they will be getting a surprise (well not that much; they do read this after all).

[sourcecode language="bash"]
#!/bin/bash
sudo cat /dev/input/mice | read -n 1
mplayer -fs rick.flv &
xtrlock
[/sourcecode]

http://youtu.be/Bps8yTw0W5c
