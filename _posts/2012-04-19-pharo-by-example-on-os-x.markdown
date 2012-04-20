---
layout: post
title: Running the Pharo by Example Image
date: 2012-04-19T22:00:00-07:00
tags: Pharo, Smalltalk
---
This week brought Version 1.4 announcements for both [Clojure](http://groups.google.com/group/clojure/browse_thread/thread/1f87f69db07a8162) and [Pharo](http://www.pharo-project.org/news?dialog=pharo-1-4-released). The alignment of their version numbers was a coincidence, but I took it as a subtle hint from the computer language universe that I should renew my dabbling in Smalltalk.

The first time that I took Pharo for a spin, I got through the Professor Stef tutorial and was intrigued. I was beginning to see why people find Smalltalk to be so appealing. There is a real beauty in the simplicity and consistency of its object model. I got busy and sidetracked, though, and didn't make it much further than that.

This time around, I am planning to work my way through [Pharo By Example](http://pharobyexample.org/). Unfortunately, the [image used by the examples in the
book](https://gforge.inria.fr/frs/download.php/27023/PBE-1.0.zip) does not run on the VM that comes with Pharo 1.4: it crashes on
startup. [This
post](http://lists.gforge.inria.fr/pipermail/pharo-users/2011-December/003278.html)
on the Pharo Users mailing list pointed me in the right direction. It
turns out that the image does not run on the Squeak/Cog-based VM
distributed with Pharo 1.4.

I don't know about other platforms, but for OS X, the answer is to use the non-Cog Squeak 5.7.x Cocoa VM available at [squeakvm.org](http://www.squeakvm.org/mac/). Download it, unzip it, and drag the Pharo by Example image onto it. That will launch the image and then you can start working through the book.

Happy Smalltalking!
