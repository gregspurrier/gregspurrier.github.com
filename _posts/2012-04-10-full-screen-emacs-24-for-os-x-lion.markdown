---
layout: post
title: Full-screen Emacs 24 for OS X Lion
date: 2012-04-10T21:00:00-07:00
tags: Emacs
---

I have been experimenting with Emacs in full-screen mode as a way to reduce distractions. The Homebrew Emacs formula includes a patch providing the `M-x ns-toggle-fullscreen` command for switching between normal and full-screen modes. It works well, but does not provide the typical OS X Lion full-screen app experience. In particular, it remains on the desktop, obscuring non-full-screen applications, rather than moving to its own space.

Searching on the web yielded a [patch](https://gist.github.com/1355895) by Konstantinos Efstathiou and [his instructions](http://efstathiou.gr/unlinked/fullscreen) for compiling Emacs as a proper full-screen application for OS X Lion. It looked promising, but I was reluctant to install updated versions of autoconf and automake, especially given his warnings. Searching further, I found an [EmacsWiki entry](http://emacswiki.org/emacs/FullScreen#toc23) referencing a Homebrew formula using the same patch, but it failed to build for me. In fact, all Homebrew builds for Emacs 24 were failing for me, so perhaps the HEAD of the git Emacs mirror it uses was broken.

Frustrated, I took inspiration from David Caldwell's [build-emacs](https://github.com/caldwell/build-emacs) script and used the following commands to build a Lion-only, full-screen capable version of Emacs 24.0.95:

    curl -O ftp://alpha.gnu.org/gnu/emacs/pretest/emacs-24.0.95.tar.gz
    tar xzf emacs-24.0.95.tar.gz 
    cd emacs-24.0.95/
    curl https://raw.github.com/gist/1355895/860fb678838e3cceccf896f4116b13ee5815f526/lion-fullscreen.patch | patch -p1
    sed -ie "s/colorWithCalibratedRed/colorWithDeviceRed/" src/nsterm.m
    ./configure --host=x86_64-apple-darwin --build=i686-apple-darwin --with-ns
    make clean install 

For good measure, I also included the equivalent of the Homebrew Emacs formula's `--srgb` flag to fix the Emacs [color rendering bug](http://debbugs.gnu.org/cgi/bugreport.cgi?bug=8402) on OS X.

Once the build is complete, you can test it with `open nextstep/Emacs.app`. Both `M-x ns-toggle-fullscreen` and the standard full-screen button in the top-right corner of the window work for switching modes. If everything is OK, you can then move it to `/Applications` or wherever else you normally keep your Emacs installation.

The jury is still out on my distraction-free-Emacsing experiment, but at least now it seamlessly integrates with the OS X Lion full-screen experience.