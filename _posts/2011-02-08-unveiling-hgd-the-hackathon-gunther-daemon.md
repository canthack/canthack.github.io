---
author: edd
comments: true
date: 2011-02-08 13:34:01+00:00
excerpt: I hereby introduce <a href="https://github.com/vext01/hgd">HGD - The Hackathon
  Gunther Daemon<a/>
layout: post
slug: unveiling-hgd-the-hackathon-gunther-daemon
title: Unveiling HGD - The Hackathon Gunther Daemon
wordpress_id: 494
categories:
- Programming
- Unix
---

If you have been following the canthack blog, you will have read about [the LPD hack](http://canthack.org/2010/11/lpd-music-hack-how-its-done/) and undoubtedly noted it's limitations.

So... At the last hack, we tested some code myself and Mex have been working on in our spare time. I hereby introduce [HGD - The Hackathon Gunther Daemon](https://github.com/vext01/hgd).


<blockquote>

HGD is a music system suitable for hackathons, internet cafes, LAN parties etc. Music is played on one set of speakers and clients queue up media from their own machines over the network. Because we use MPlayer to play media, you can send anything MPlayer understands, including video.

HGD was inspired by the LPD hack -- a music system used at OpenBSD hackathons. We plan to implement similar functionality and extend it.

Current features
----------------

> 
> 
	
>   * Network listener daemon.
> 
	
>   * Player daemon (uses mplayer).
> 
	
>   * Command line client with the ability queue and vote-off tracks.
> 
	
>   * Works on Linux and OpenBSD.
> 

</blockquote>



A few bugs were found at the hack, but we believe we have fixed them.

Please go ahead and test it -- we plan to tag a release fairly soon, so if you find bugs, please file them on github. After this we plan to add authentication support.

There is also a GTK client on the way from Mex. If you pester him, he might put the code up. The HGD protocol is very simple, so if you want to write a client, it should not be too difficult -- you will just need to bone up on TCP socket programming in your favorite language (Someone please write an android client).

Enjoy
