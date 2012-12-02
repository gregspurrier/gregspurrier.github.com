---
layout: post
title: "To Be Continued: Async Simplified"
date: 2012-12-02T12:30:00-07:00
tags: Clojure, ClojureScript, Node.js
---

Asynchronous programming can greatly boost the performance of I/O-bound applications. This wins it many proponents and is a major contributor to the popularity of Node.js.

Unfortunately, asynchronous programs can be challenging to write, read, and maintain. Left unchecked, they can devolve into piles of callback spaghetti and deeply nested anonymous callback function definitions. The latter are aptly called "Pyramids of Doom" by Trevor Burnham in his book [Async JavaScript](http://pragprog.com/book/tbajs/async-javascript).

Flow control libraries like [Async](https://github.com/caolan/async) and [Step](https://github.com/creationix/step) improve the situation in JavaScript by managing the flow of the program from one asynchronous function to the next. But, even with these libraries, the interplay between synchronous and asynchronous code is still too awkward for my taste.

In Clojure, where we can bring macros into play, we can do better. The remainder of this post introduces an asynchronous Clojure/ClojureScript library that I have been working on called To Be Continued. Its goal is to make it easier to write, read, and maintain asynchronous Clojure and ClojureScript programs.

## Callback Placeholders

Callback placeholders, indicated by `...` at the end of a form, are the central feature of To Be Continued. When one is encountered during the expansion of TBC's macros, it is replaced with a generated callback function that arranges for the flow of execution to continue where it left off once the result is available.

In addition to telling the TBC macros that a given form requires a callback, the `...` serves as a hint to readers that an asynchronous call is taking place behind the scenes even though TBC is allowing the flow of the code to be expressed uninterrupted.

Callback placeholders are used with TBC's threading macros and parallel binding form, described below.

## Threading Macros

To Be Continued provides two asynchronous-aware threading macros, `-+->` and `-+->>`, analogous to clojure.core's `->` and `->>` macros, respectively. They expect a value or an expression returning a value as their first argument, a callback form to invoke with the final result as their last argument, and any number of intermediate forms in between.

The intermediate forms may be synchronous function invocations, asynchronous function invocations having a callback placeholder as their final argument, or a combination of the two.

The return value of TBC threading macros is always nil. It is expected that the final form will be a callback that will do something useful with the result passed to it.

For example:

    (use 'to-be-continued.core)

    (defn async-square
      "Invoke the callback with the square of x after 1 second"
      [x callback]
      (.start (Thread. (fn []
                         (Thread/sleep 1000)
                         (callback (* x x))))))
    
    (-+-> 7
          (async-square ...)
          (str " monkeys")
          println)
    
    ;; => nil
    ;; 49 monkeys

Once the asynchronous result of `async-square` has been supplied to its automatically-generated callback, processing resumes with a synchronous call to `str` and then to `println` which serves as the final callback and displays the result.

## Parallel Binding

The `let-par` macro is the asynchronous equivalent of Clojure's `let` macro. It allows the results of multiple asynchronous functions, executed in parallel, to be bound to variables that can be referenced the body expression.

For example:

    (defn async-sum-of-squares
      [x y callback]
      (let-par [x-sq (async-square x ...)
                y-sq (async-square y ...)]
        (-+->> (+ x-sq y-sq)
               (str "The answer is: ")
               callback)))

    (async-sum-of-squares 2 5 println)
    ;; => nil
    ;; The answer is 29

Note that the use of the `-+->>` macro above is not strictly necessary because there are no asynchronous forms before the final callback. Its use is encouraged, however, for error handling purposes. Once error handling support has been incorporated into TBC (see Status and Next Steps, below) any errors that occur in the chain will be properly handled and propagated.

Like `-+->` and `-+->>`, the value of a `let-par` expression is always nil. It is expected to invoke a callback with its result.

## Example: ClojureScript + Node.js
To see To Be Continued in action, please check out [tbc-node-example](https://github.com/gregspurrier/tbc-node-example). It is an example project using TBC with ClojureScript and Node.js to asynchronously fetch data from GitHub's API. 

It has example usages of the macros described above as well `map-par`, a parallel, asynchronous equivalent of `clojure.core/map`.

## Status and Next Steps
To Be Continued 0.1.0 supports both Clojure and ClojureScript. The [source code](https://github.com/gregspurrier/to-be-continued) is available on GitHub and a [distribution package](https://clojars.org/to-be-continued) is posted on Clojars.

TBC currently lacks support for handling errors that occur during asynchronous computation. Therefore, it is not yet suitable for production use. Error handling is my next focus and will be available in the 0.2.0 release.

In the mean time, the existing functionality should be sufficient to get a feel for the library. Feedback is very welcome, whether as comments here or as issues filed on its GitHub project.