---
author: tris
comments: true
date: 2011-09-20 15:34:56+00:00
layout: post
slug: hgdc-x-a-cross-platform-native-hgd-client
title: hgdc-x. A Cross-Platform, Native hgd Client.
wordpress_id: 855
categories:
- Events
- Programming
---

At Can't Hack's recent Medway Hack day, which took place at the Medway Hackspace, I decided to work on a new desktop /GUI client for the [hgd ](http://canthack.org/2011/07/hgd-0-4-1/)music server. The current crop of hgd clients does not so far include an actively maintained desktop GUI, so it was a great opportunity to provide one, and seeing as we planned to (and did) hack for 12 hours straight - there was plenty of time with which to get something reasonable implemented!

It is called **hgdc-x**. The _**X**_ alludes to the cross-platform nature of the GUI code. By writing it from the ground up in [Lazarus](http://lazarus.freepascal.org)/Freepascal, it can be compiled for Windows/Linux/Mac OS X/FreeBSD among others, targeting various toolkits like GTK, QT, Win32, and Carbon.

I have since essentially finished the client, which supports all of the usual hgd user features like user authentication, SSL encrypted connection, listing the playlist, queuing tracks and voting tracks off, but also includes added features like album artwork fetching (using the Last.fm API). Limited support is provided for hgd's administrative features, so hgdc-x supports song pausing and skipping as long as you are logged in as a user who is an admin on the hgd server.

In the future I will add Last.fm scrobbling, remaining admin features and other cool stuff.

The development version (available right now) been tested on Windows, Linux and Mac OS X, and works fine on all three.

[caption id="attachment_889" align="alignright" width="254" caption="hgdcx on Linux"][![hgdcx on Linux](http://canthack.org/uploads/hgdcx2-254x300.png)](http://canthack.org/2011/09/hgdc-x-a-cross-platform-native-hgd-client/hgdcx-3/)[/caption]

Proper tagged releases and pre-compiled binaries will follow, as soon as the hgd server makes its next release (0.5). When this happens the version numbers will be kept in sync with the server. Until then, the HEAD of hgdc-x should work correctly with the HEAD of the main hgd server repository, as I scramble to keep up with API and protocol improvements, so if you can install Lazarus 0.9.30, give it a go if you like!

The source is available from [GitHub](https://github.com/tristan2468/hgdc-x), where an Issue Tracker is active for any inevitable bugs or indeed feature requests you may like to add.

When complete, the same GitHub page will be the place to go for the pre-compiled binaries.
