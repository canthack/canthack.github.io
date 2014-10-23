---
author: edd
comments: true
date: 2010-11-28 16:12:19+00:00
excerpt: So, it's quite a hack and it amazes me that there is no better solution yet.
layout: post
slug: lpd-music-hack-how-its-done
title: LPD Music Hack - How it's Done
wordpress_id: 31
categories:
- Unix
---


<a href="http://farm6.static.flickr.com/5043/5211382565_467cda6110_z.jpg" rel="lightbox[lpd]"><img
class="alignright" title="The Playlist"
src="http://farm6.static.flickr.com/5043/5211382565_467cda6110_m.jpg" alt="" width="240"
height="180" /></a>People have been asking how to achieve the lpd music hack, which was used at <a
href="http://omer.me/2010/11/friday-night-geekery/">Friday night geekery #1</a>.

First of all I would like to explain that the idea is not my own. OpenBSD developers have been using
this technique at <a href="http://www.openbsd.org/hackathons.html">their hackathons</a> for years
now. I was first exposed to this hack at p2k9 when I was enrolled as an OpenBSD dev, then i asked
for the configs at p2k10. So basically the message is "this was not my idea, but it's awesome".
<h2>Requirements</h2>
A single music server which maintains a playlist, playing tracks one at a time. Users should be able
to queue their own music from their own machines without having to put anything into a database
up-front (which is why mpd sucks for this). Users should be able to view the playlist.

Seems simple huh? Well no software I know does this.
<h2>Configure Server</h2>
<ul>
<ul>
	<li>Find a machine capable of playing audio and install OpenBSD on it. Do not enable aucat, or
if you do, make sure the daemon user can use the aucat socket.</li>
	<li>Set up a print spool that will use a shell script as it's filter. An /etc/printcap like
this:
<pre lang="bash">mp3|lp:\
  :lp=/dev/null:mx#0:if=/opt/bin/mp3spool:sd=/var/spool/output
</pre>
</li>
	<li>Permit remote printing</li>
</ul>
</ul>
Set up an /etc/hosts.lpd and list hosts that should be able to play music. If you are in a trusted
network, you can just stick in a '+', meaning "everyone with a reverse lookup name". If your hosts
do not have reverse DNS records, then list them in /etc/hosts instead. Make sure that
/etc/resolv.conf is configured correctly to do so.
<ul>
<ul>
	<li>Make the spooling script</li>
</ul>
</ul>
Make the script that 'if' refers to in the print spooler, so in our case /opt/bin/mp3spool.
<pre lang="bash">/usr/local/bin/mplayer -vo null -af resample=48000 - || \
true &gt; /dev/null 2&gt;&amp;1
</pre>
Make sure the script is executable.
<ul>
<ul>
	<li>Start lpd</li>
</ul>
</ul>
Just type `lpd` as root. If you want lpd to start at boot
<pre lang="bash">echo 'lpd_flags=""' &gt;&gt; /etc/rc.conf.local</pre>
&nbsp;
<ul>
	<li>Test the server</li>
</ul>
&nbsp;

`lpq` should display 'no entries' and you should be able to queue songs using
<pre lang="bash">lpr /some/song.mp3</pre>
<h2>Configure Clients</h2>
<ul>
<ul>
	<li>Set up spooler</li>
</ul>
</ul>
In /etc/printcap:
<pre lang="bash">lp:\
  :lp=:rm=:rp=lp:sd=/var/spool/output:lf=/var/log/lpd-errs:
</pre>
<ul>
<ul>
	<li>Start spooler in the same way as the server, `lpd`</li>
	<li>Test clients</li>
</ul>
</ul>
`lpq` and `lpr` should work in the same way as on the server.

That's it!

A few extra notes:
<ul>
	<li>When you are using `lpq`, song names may be too long and truncated to '...', in which case,
you would use `lpq -l`.</li>
	<li>User's may de-queue their own tracks using `lprm <em>jobnumber</em>`, as long as the song is
not already playing. The job number is found in the short `lpq` output.</li>
	<li>Assign a "queue admin" who has root access on the server. If a crap song comes on and 3
people vote it off, the queue admin can kill the job using lprm as root.</li>
	<li>If others don't use lpd or can't be arsed to set up cups (i don't blame you), then you can
distribute a script to queue songs locally from the server. This is what we used:
<pre lang="bash">#!/bin/sh

host="username@servername"
path="/tmp/"

if [ "$1" = "" ]; then
        echo "       q.sh "
        echo "       q.sh -l"
        exit 1
elif [ "$1" = "-l" ]; then
        ssh ${host} lpq -l
        exit 0
fi

file="`basename \"$1\"`"
scp "$1" "${host}:${path}\"${file}\""
ssh ${host} "lpr ${path}\"${file}\""
ssh ${host} "rm ${path}\"${file}\""
</pre>
If you do this you will either have to set up a user account for each user (if you want to see who
queued what in the playlist), or if you are lazy, use a single user account on the server.</li>
</ul>
So, it's quite a hack and it amazes me that there is no better solution yet. Please someone write
one.

Please leave know-it-all comments underneath, if you must.

