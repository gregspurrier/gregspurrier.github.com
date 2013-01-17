---
layout: post
title: "ShenRuby 0.2.0: Hello Ruby Interop"
date: 2013-01-16T20:40:00-08:00
tags: Ruby, Shen, ShenRuby
---

I'm pleased to announce the release of ShenRuby 0.2.0. This release brings improved performance, bug fixes, and the beginnings of Ruby/Shen interoperablilty. Please see the [release notes](https://github.com/gregspurrier/shen-ruby/blob/master/HISTORY.md#020---january-16-2013) for the full details.

The following contrived example illustrates the interop features introduced with ShenRuby 0.2.0.

## Fizz Buzz in Ruby and Shen
Imagine you are being interviewed for a job and the interviewer has just asked you to implement [Fizz Buzz](http://en.wikipedia.org/wiki/Fizz_buzz) in any language you like. You admit that Fizz Buzz was not covered in your undergraduate algorithms class and that you have no idea what it is. The interviewer smiles and explains: "it's no big deal. You just need to make a function that takes an integer as input and returns 'Fizz' if the number is divisible by 3, 'Buzz' if it is divisible by 5, 'Fizz Buzz' if it is divisible by both 3 and 5, and the string representation of the number otherwise. Please implement it and show that it works on the numbers 1 through 20."

You've been reading up on Shen lately and the interviewer's description of the Fizz Buzz translates easily to Shen's pattern matching approach to function definition. But, the standard Shen system functions don't include a `divides?` function to tests whether one number evenly divides another. No problem: that's trivial in Ruby. So, you choose a hybrid approach using ShenRuby.

First, you create a new instance `ShenRuby::Shen` to be your Shen environment.

    require 'rubygems'
    require 'shen_ruby'
    
    shen = ShenRuby::Shen.new

You read in ShenRuby's [README](https://github.com/gregspurrier/shen-ruby/blob/master/README.md) that any Ruby instance method added to the Shen environment is available for use within Shen. So, you add a `divides?` method:

    class << shen
      # Returns true if b is evenly divisible by b.
      def divides?(a, b)
        b % a == 0
      end
    end

You also read that the `eval_string` method may be used to evaluate arbitrary Shen code and is typically used for defining new functions. Now that you have `divides?`, implementing Fizz Buzz in Shen is trivial:

    # Implement the fizz-buzz function in Shen
    shen.eval_string <<-EOS
      (define fizz-buzz
        X -> "Fizz Buzz" where (and (divides? 3 X)
                                   (divides? 5 X))
        X -> "Fizz" where (divides? 3 X)
        X -> "Buzz" where (divides? 5 X)
        X -> (str X))
    EOS

Finally, you write a bit of Ruby to print out the first twenty results, taking advantage of the fact that Shen functions are invokable as methods on the Shen object:

    (1..20).each do |i|
      puts shen.fizz_buzz(i)
    end

Intrigued, the interviewer spends the rest of the time asking questions about Shen. You get the job.

## Learning More about Ruby<->Shen Interop
The above example shows most of the capabilities of the Ruby/Shen interoperablilty as it exists in ShenRuby 0.2.0. I've left out a few details about automatic coercsions that take place between the Ruby and Shen worlds and some limitations with the current implementation. Those are both covered in the [README](https://github.com/gregspurrier/shen-ruby/blob/master/README.md#ruby-shen-interop) section of the ShenRuby README.

The next interoperablilty feature that I plan to work on is the ability for Shen code to instantiate and interaction with native Ruby objects. The syntax for this will be inspired by Clojure's Java interoperablilty syntax. 

These are still the early days of the interoperablilty story in ShenRuby. I expect the APIs to evolve as they get more mileage. Feedback, suggestions, and bug reports are appreciated.


