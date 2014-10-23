---
author: omer
comments: true
date: 2011-07-03 17:13:06+00:00
layout: post
slug: phphgdc-a-php-client-for-hgd
title: phphgdc - A PHP client for HGD
wordpress_id: 781
categories:
- Software
---

[HGD](https://github.com/vext01/hgd) is [Edd](http://twitter.com/vext01) and [Mex](http://twitter.com/egelmex)'s awesome network playlist daemon that we use at our hacknights. It lets us queue songs from our machines to a single set of speakers, which is infinitely easier than swapping an audio cable around every time someone wants play a song. It even has a 'vote off' feature for rubbish songs.

At the last hacknight I wrote a simple PHP client for the HGD that will let people queue songs from their browsers. This is quite useful as currently the official hgd client doesn't compile (easily) on Windows and we do have a few Windows users. It is also useful for the lazy people who can't be bothered with the installation of a client since they can just point their browsers to my PHP client.

[![](http://canthack.org/uploads/336407484-300x281.png)](http://canthack.org/uploads/336407484.png)

The idea is to set this up on the same machine that runs the hgd but that's not a requirement since the client uses sockets to communicate with the server.

It is pretty much a work-in-progress so it's a bit rough around the edges but the basic functionality (authentication/queuing songs) works fine. The source lives[ here](https://github.com/omerk/phphgdc), in case you want to play with it. I'll prettify and add a few more features like playlist and vote off support to it at the next hack event.

And here's the environment (i.e: the last hacknight) this was conceived in:

[![](http://canthack.org/uploads/2011-07-01-19-12-59-224x300.jpg)](http://canthack.org/uploads/2011-07-01-19-12-59.jpg)


