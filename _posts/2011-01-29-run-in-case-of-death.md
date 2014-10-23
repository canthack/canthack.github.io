---
author: fish
comments: true
date: 2011-01-29 02:48:33+00:00
layout: post
slug: run-in-case-of-death
title: Run in case of Death
wordpress_id: 481
categories:
- Programming
---

[![And every day it gets harder to fight the urge to su to the user and freak people out.](http://imgs.xkcd.com/comics/admin_mourning.png)](http://xkcd.com/686/)

OK so I figured - I've got irssi running in screen on my virtual server, like most good geeks.  What happens if I get hit by a bus/fall down a well/die from a phase-to-phase electrical shock?

Assuming that my bank account continues to work for some time, I will continue paying my subscription and my server will continue to operate.  My screen session will stay active for quite some time after that.  So, after I die, I will still be joined to #sparkfun and #openrightsgroup - and nobody in there will be the wiser that one of the people on their channel is in fact dead.  If you are in a busy channel (say, #ubuntu or #debian) it is very possible that one of the people in there is in fact dead.  And you'd never know it.  Spooky, eh?

So, I wanted to try and rectify this problem.  As such, I started hacking together a Python script which I will run on a cron-job (once it is completed).  It's pretty simple:
[sourcecode language="python"]#!/usr/bin/env python

import os
import time

QUESTIONABLE = 48 * 3600
VERY_QUESTIONABLE = 72 * 3600
DEAD = 14 * 24 * 3600

alivefile = os.stat('~/.alive')
difference = time.time() - alivefile.st_atime

if (difference > QUESTIONABLE):
        print "You might be dead .... I'll ask"
if (difference > VERY_QUESTIONABLE):
        print "You're starting to worry my now"
elif (difference > DEAD):
        print "You're dead.  I'll tell your friends"
else:
        print "You're alive.  Yay!"[/sourcecode]
It's really nothing special, all it does is check the access time on a file (called "alive").

Currently, it doesn't do anything other than print something when it gets worried.  However, I plan to hook into Clickatel and have it send me a SMS in that instance, and remind me to ping it should I forget.

I've added a line to my .bashrc
[sourcecode language="bash"]touch ~/.alive[/sourcecode]
So every time I log in, it updates the access time on the file.

Sending a SMS to my [AQL](http://www.aql.com) number can also run a script which will update the access time on the file.

After that, I need to write some irssi plugins that hook into every time I send a message to update the access time again (gotta be alive to send a message on irssi).

And finally, once I've done this, I'll write some plugins for irssi that will run in the case of my death.  I'm not entirely sure what it might do - maybe let everyone know that I am now dead and leave all of the channels.  Or try and freak people out a bit and switch to a Markov chain bot which uses all of the text I've ever said on IRC from my log files - and make it look like I'm still there (but possibly deranged).

Now, the question is - can find a service which will let me programatically send a letter via some kind of API, and send out a condolences greeting card for myself?
