---
author: mex
comments: true
date: 2010-12-13 13:22:39+00:00
layout: post
slug: the-video-part2
title: The Video (part2)
wordpress_id: 140
categories:
- Unix
---

![A Webcam](http://canthack.org/uploads/webca.jpg)So the plan for this weeks hack session was to get an all new video of the hacking this time with multiple points of view!  Unfortunately I was not able to get this to work during the actual event.  However I did learn number of things about motion and have got it working since.


## Configuring motion for multiple cameras


When using motion with only one camera, all that is needed it _motion.conf_.  However if you need to use more than one camera you need a config file per camera in addition to motion.conf.
For example if you have two cameras you might have the files:



	
  * motion.conf

	
  * thread1.conf

	
  * thread2.conf


and you start each thread off with the thread setting in motion.conf:

    
    thread /etc/motion/thread1.conf
    thread /etc/motion/thread2.conf
    


The thread*.conf files only need to contain the setting the are unique to that camera.  The contents of my thread1.conf is.

    
    videodevice /dev/video0
    target_dir /tmp/motion/1
    webcam_port 8082
    




## So why no video?


As I said earlier I could not get this working at the hack night, even though I worked out all the settings above.  I have subsequently solved the issue, and there will be a multi angle video of the next hack night.

On Ubuntu there are a number of sample thread*.conf files.  I would recommend not trying to edit the files supplied and just create your own, because the reason I could not get it working was one of the settings set by the sample config.
