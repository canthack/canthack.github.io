---
author: mex
comments: true
date: 2010-12-23 12:16:47+00:00
layout: post
slug: the-video-part-3
title: The Video (part 3)
wordpress_id: 331
categories:
- Programming
---

[![](http://canthack.org/uploads/000-150x150.jpg)](http://canthack.org/uploads/000.jpg)This is the third post in the series, the [first](http://canthack.org/2010/11/the-video/) and [second](http://canthack.org/2010/12/the-video-part2/) are elsewhere.

In preparation for the next hack night I have been playing around with multiple angle videos.
I created short set of images using [motion](http://www.lavrsen.dk/foswiki/bin/view/Motion/WebHome) with three cameras.  I then used a [python](http://www.python.org/) script to sew the images together.  The script converts a directory listing of files (with time stamps) into a list of lists of filenames to pass into [montage](http://www.imagemagick.org/script/montage.php) to make the indervidual images into frames for the video.

The algorithm is rather suboptimal, it just chooses the next lowest lowest image from the list.  If any one has any ideas on how to improve this please leave a comment.  More details after the break..
<!-- more -->



### Input



    
    
    Camera1			Camera2			Camera3
    1220195243-00.jpg	1220195241-01.jpg	1220195232-01.jpg
    1220195244-01.jpg	1220195242-01.jpg	1220195257-01.jpg
    1220195306-01.jpg	1220195243-01.jpg	1220195304-01.jpg
    1220195308-00.jpg	1220195307-01.jpg	1220195312-01.jpg
    





### Output



    
    
     { 1220195243-00.jpg ,  1220195241-01.jpg ,  1220195232-01.jpg },
     { 1220195243-00.jpg , *1220195242-01.jpg*,  1220195232-01.jpg },
     { 1220195243-00.jpg , *1220195242-01.jpg*,  1220195232-01.jpg },
     {*1220195244-01.jpg*,  1220195242-01.jpg ,  1220195232-01.jpg },
     {...etc..}
    



Every triplet in this list is then used to generate a call to montage, to stitch the photos together.


    
    
    montage -geometry +0+0 1220195243-00.jpg 1220195241-01.jpg /
    1220195232-01.jpg 001.jpg
    



to create a frame like this...
[[caption id="attachment_353" align="aligncenter" width="300" caption="Stitched Frame from for the Video"]![Stitched Frame from for the Video](http://canthack.org/uploads/018-300x75.jpg)[/caption]](http://canthack.org/uploads/018.jpg)

 
[sourcecode language="python"]

from collections import deque
import getopt
import os
import subprocess
import sys
    
def doit():
    try:
        opts, args = getopt.getopt(sys.argv[1:], "h", ["help"])
    except getopt.error, msg:
        print msg
        print "for help use --help"
        sys.exit(2)
        
    print ("opts", opts)
    print ("args", args)
    
    images = []
    
    paths = args
    
    
    for path in paths:
        files = deque(os.listdir("./" + path + "/"))
        print files
        images.append(files)
    
    
    stillRunning = range(0, len(images))
    
    c = []
    n = []
    for index in stillRunning:
        c.append(images[index].popleft())
        n.append(images[index].popleft())
        
    x = []
    x.append(c[:])
    
    print "c=", c, "\nn=", n, "\nx=", x
    while len(stillRunning) > 0:
        done = False
        for t in stillRunning:
            
            if n[t] <= min (n):
                c[t] = n[t]
                x.append(c[:])
                
                if len(images[t]) > 0:
                    n[t] = images[t].popleft()
                else:
                    stillRunning.remove(t)
                    print (stillRunning)
                    n[t] = "NULL"
                done = True


        if done:
            continue
        else:
            print ("fuck", len(x), c, n)
            exit(-1)
    

    print ""
    count = 0
    for xx in x:
        command = ['montage', '-geometry', '+0+0']
        for i in range (0, len(paths)):
            command.append('./' + paths[i] + '/' + xx[i])
        command.append('./output/' + str(count).zfill(3) + ".jpg")
        
        
        print "\n", xx
        print "Building frame", count +1, "of", len(x)
        subprocess.call(command)
        count = count + 1
    
    
if __name__ == '__main__':
    doit()
[/sourcecode]
