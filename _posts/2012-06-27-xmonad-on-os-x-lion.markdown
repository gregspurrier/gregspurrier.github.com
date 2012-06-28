---
layout: post
title: xmonad on OS X Lion
date: 2012-06-27T19:30:00-07:00
tags: xmonad, Emacs
---

I've been experimenting with distraction-free development environments on OS X Lion. I had high hopes for [full-screen Emacs](/2012/04/10/full-screen-emacs-24-for-os-x-lion.html). Unfortunately, neither Eshell nor terminal-mode felt quite right for command line work. And, even with `windmove-default-bindings`, navigating around the three or four windows I was using to take advantage of the full-screen space was awkward.

About the time I was abandoning full-screen Emacs, I discovered [xmonad](http://xmonad.org/) through some tweets from [@Baranoksy](https://twitter.com/Baranosky) and [@SeanTAllen](https://twitter.com/SeanTAllen). It is a tiling window manager for X Windows that can be controlled completely through the keyboard. xmonad is to X what tmux is to a terminal. Trying it on an Arch Linux VM gave me exactly what I was looking for: Emacs on the left and a variable number of XTerms stacked on the right.

Intrigued and excited to incorporate it into my daily work, I set out to get xmonad working full-screen on OS X Lion. It takes some work, and the experience is not seamless--see issues to be aware of, below--but it is good enough to for serious work. I've been using it as my primary development setup for the past week weeks and have been very happy with it.

The following sections describe the steps I went through to get xmonad up and running in a full-screen X11 environment on OS X Lion. I assume that you already have [Homebrew](http://mxcl.github.com/homebrew/) installed. 

## Installing XQuartz
`X11.app`, the implementation of the X Window System that ships with OS X Lion does not support full-screen usage. To get that functionality, you must install the latest version of [XQuartz](http://xquartz.macosforge.org/).

Installation is simple: download the [XQuartz-2.7.2 disk image](http://xquartz.macosforge.org/downloads/SL/XQuartz-2.7.2.dmg), open it, and run the `XQuartz.pkg` installer. After installation, log out and log in again. This ensures that XQuartz will be recognized as the default implementation of X.

## Building xmonad
xmonad is implemented in Haskell, so the first step is installing the Haskell compiler and platform:

    brew update
    brew install ghc haskell-platform

These packages take a while to build. Now is a good time to take a break, stretch your legs, and come back in a few minutes. If they are still building, peruse the [xmonad Guided Tour](http://xmonad.org/tour.html) to get a taste for the functionality that you will soon be enjoying.

Next, install xmonad's dependencies using cabal, which is Haskell's package manager:

    cabal update
    cabal install "X11 >=1.5.0.0 && <1.6"
    cabal install "utf8-string ==0.3.*"

Finally, download, build, and install xmonad as an application for use by the current user:

    mkdir ~/build-xmonad
    cd ~/build-xmonad
    curl -O http://hackage.haskell.org/packages/archive/xmonad/0.10/xmonad-0.10.tar.gz
    tar xzf xmonad-0.10.tar.gz
    cd xmonad-0.10
    runhaskell Setup.lhs configure --user --prefix=$HOME
    runhaskell Setup.lhs build
    runhaskell Setup.lhs install --user

## Configuring XQuartz for xmonad

Create an `~/.xinitrc` file containing:

    [[ -f ~/.Xresources ]] && xrdb -load ~/.Xresources
    xterm &
    $HOME/bin/xmonad

Now start XQuartz (it is installed in `/Applications/Utilities`). You will not see anything yet. Open the XQuartz Preferences with Command-, and update the following settings:

- Output
    - Enable "Full-screen mode"
- Input
    - Enable "Emulate three button mouse"
    - Disable "Follow system keyboard layout"
    - Disable "Enable key equivalents under X11"
    - Enable "Option keys sent Alt_L and Alt_R"
- Pasteboard
    - Enable all of the options

We are now ready to toggle full-screen mode with Command-Option-A. After doing so, you should be greeted by a full-screen XTerm. Hitting Option-Shift-Return should open another Xterm. Now hit Option-Shift-Q to quit xmonad (and XQuartz): we've got a little more work to do.

## Improving the Full-screen Experience

- Three-finger swipe up to enter mission control
- Hold down the Option key and click the + button in the top left. This will create another desktop.
- Select the new desktop and launch XQuartz. Right click on its icon in the dock and choose "This Desktop" under Options. Now XQuartz will always launch into this desktop.

## Issues To Be Aware Of

If you use a three-finger left or right swipe to move from the desktop running XQuartz to another desktop or full-screen app and then return, your xmonad windows will likely be missing. Simply click on the XQuartz icon in the dock to bring them back.

I have had a few instances in which the Command-Return key sequence to launch a new terminal stops responding. If you have a terminal open, it's not the end of the world because you can still launch them manually from the command line. But, it is annoying and I typically restart xmonad as soon as I am at a stoping point. Unfortunately, I have not found a reliable way to reporduce this in order to file a bug report. Fortunately, it is pretty rare.

## Optional: Building Emacs 24 with X Support

If you're an Emacs user, you'll likely want a version of Emacs 24 that has been built with support for X. To avoid conflicting with the packages in /usr/local managed by Homebrew, we'll put this build in /opt/local:

    brew install fontconfig libjpeg libtiff ungif 
    cd ~/build-xmonad
    curl -O http://ftp.gnu.org/pub/gnu/emacs/emacs-24.1.tar.gz
    tar xzf emacs-24.1.tar.gz
    cd emacs-24.1
    ./configure --prefix=/opt/local
    sudo make install

## Acknowledgements

These instructions were inspired by the instructions found on the [Using xmonad on Apple OSX](http://www.haskell.org/haskellwiki/Xmonad/Using_xmonad_on_Apple_OSX) page. 

The hint about dedicating a separate desktop space to X11 came from [Run X11 applications full screen in Mac OS X Lion](http://www.itworld.com/software/243789/run-x11-applications-full-screen-mac-os-x-lion).

Thanks to [Alex Baranosky](https://twitter.com/Baranosky) and [Josh Fleming](https://twitter.com/joshvf) for test driving earlier versions of these instructions.
