---
layout: post
title: Announcing ShenRuby 0.1.0
date: 2012-12-30T21:15:00-07:00
tags: Ruby, Shen, ShenRuby
---

I discovered [Shen](http://shenlanguage.org)--a functional programming language created by Mark Tarver--a few months ago while reading a 2011 [blog post](http://blog.fogus.me/2011/10/18/programming-language-development-the-past-5-years/) by Michael Fogus, author of _The Joy of Clojure_. As I read more about it, I was smitten. A modern, functional Lisp with pattern matching, currying, and an optional, very powerful static type system? Sign me up!

Something I found intriguing about Shen was that it was specifically designed for portability. It is implemented in a language called K Lambda, which is a tiny Lisp consisting of only 46 functions. Port K Lambda and you have ported Shen.

A few weeks ago I got to thinking: wouldn't it be nice to have Shen ported to Ruby in a way that enabled the construction of hybrid applications? Tasks well suited to Shen's pattern matching approach to functional programming could be implemented in Shen. Tasks well suited to OOP or imperative programming could be written in Ruby. The two parts could then run together on a Ruby VM.

Inspired, and with a copy of [_The Book of Shen_](http://www.shenlanguage.org/tbos.html) in hand, I got to work.

## ShenRuby

Today I am pleased to announce ShenRuby 0.1.0, the first release of my Ruby port of Shen. The goal of the 0.1.0 release is to provide a Shen 7.1 REPL that can be easily installed and used by Rubyists to explore Shen.

Here is zero to factorial in the ShenRuby REPL:

    % gem install shen-ruby
    Fetching: shen-ruby-0.1.0.gem (100%)
    Successfully installed shen-ruby-0.1.0
    1 gem installed
    Installing ri documentation for shen-ruby-0.1.0...
    Installing RDoc documentation for shen-ruby-0.1.0...
    % srrepl
    Loading.... Completed in 18.59 seconds.
    
    Shen 2010, copyright (C) 2010 Mark Tarver
    www.shenlanguage.org, version 7.1
    running under Ruby, implementation: ruby 1.9.3
    port 0.1.0 ported by Greg Spurrier
    
    
    (0-) (define factorial
           0 -> 1
           X -> (* X (factorial (- X 1))))
    factorial
    
    (1-) (factorial 5)
    120
    
    (2-) (quit)
    % 

In case it is not obvious, the `(0-)`, `(1-)`, and `(2-)` seen above are the Shen REPL's prompts. The number shown increase with each expression evaluated.

At this point, ShenRuby only provides a REPL, launched via the `srrepl` command. As ShenRuby matures towards its 1.0.0 release, my focus will be on enabling bi-directional interaction between Ruby and Shen and on improving performance to the point that hybrid applications are viable.

Porting Shen to Ruby has been tremendous fun so far and I'm excited to start work on the features that will support easy interaction between Ruby and Shen.

## Learning More
If you are interested in learning more about Shen, try using the ShenRuby REPL to work through the [Shen in 15 minutes](http://shenlanguage.org/learn-shen/tutorials/shen_in_15mins.html#shen-in-15mins) tutorial on the Shen website. Afterwards, explore the other resources found on the [Learn Shen](http://shenlanguage.org/learn-shen/index.html) section of Shen's website.

For more information on ShenRuby, please see the [ShenRuby Repository](https://github.com/gregspurrier/shen-ruby) on GitHub.
    




