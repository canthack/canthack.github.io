---
author: tris
comments: true
date: 2011-05-13 15:22:42+00:00
layout: post
slug: hexy-binary-to-hex-array-converter
title: 'Hexy: Binary to Hex Array Converter.'
wordpress_id: 627
categories:
- Hardware
- Programming
---

Just after the second hack night,  Tim was temporarily staying at my house for a while, and one evening just happened to be working on some embedded PIC stuff.




He was writing a system to serve an image over a simple web server that was running on the chip itself. Now, I'm not too familiar with [embedded C](http://canthack.org/2010/12/lcd-now-playing-screen/), but my skills as an application developer were still useful that evening!




In order to work with an image on a system with no filesystem, or indeed kernel, the binary data of the image file (in this case it was a jpeg) needed to be entered into an array of bytes within the C source code, a bit like the example shown here:



[sourcecode language="cpp"]
  unsigned byte data[] = {0x3C,0x3F,0x78,0x6D...};
[/sourcecode]


Tim approached me to create a Windows tool that could automate this, and so I opened up [Lazarus](http://lazarus.freepascal.org) and got coding! In a few minutes I had a solution (at the time we named it TimHex). After a few more minutes of optimisation, the finished utility worked pretty well, and we were able to serve images up from the PIC.





[![hexy](http://canthack.org/uploads/hexy.png)](http://canthack.org/2011/05/hexy-binary-to-hex-array-converter/hexy/)
    Hexy for Windows




So why this blog post? Well, in the last week or so I resurrected TimHex, renamed it, tidied it up, made it cross-platform and open sourced it.  The result of all this hard work is Hexy, which can be found on my [GitHub page](https://github.com/tristan2468/Hexy).




Feel free to have a look and post comments.




I already have some plans for future changes to it, including a command-line only interface and configurable outputs. I hope to be able to continue working on it in my spare time.
