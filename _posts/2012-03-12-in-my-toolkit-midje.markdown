---
title: "In My Toolkit: Midje"
layout: post
tags: Clojure, Testing
---
When I switched to Clojure as my primary programming language, the top item on my shopping list was a testing library. Clojure comes with `clojure.test`, but after 5 years of using RSpec, I wanted something more in its style. I stopped searching when I found Midje. It was exactly what I was looking for. It even had something I didn't realize I'd been yearning for: metaconstants--more on those later.

## Highlights
Below are some of the highlights of Midje that are intended to give
you a taste of its style and capabilities. For the details and
additional features, please see its
[user guide](https://github.com/marick/Midje/wiki) and annotated
[example](https://github.com/marick/Midje/blob/master/examples/basic/test/basic/core_test.clj).

### Facts
The fact macro is the basic Midje building block. With it you declare the expected result of an expression. It is striking in its simplicity:

    (fact (+ 1 1) => 2)

The expression before the `=>` is the expression being tested and the one after it is the expected result. In this case the result is a specific value, but a rich set of [checkers](https://github.com/marick/Midje/wiki/Checkers) allow for more generality.

Optional document strings allow you to elaborate on your tests:

    (fact "basic addition"
      (+ 1 1) => 2)

Multiple related facts may be combined into a single `fact`--or `facts`, if you prefer--expression:

    (facts "about basic addition"
      "one and one are two"
      (+ 1 1) => 2
      "two and two are four"
      (+ 2 2) => 4)

### Prerequisites
RSpec has mocks and stubs. Midje has prerequisites. They are specified with the provided clause within a fact:

    (fact (sum-of-squares 2 3) => 13
      (provided 
        (square 2) => 4
        (square 3) => 9))

Prerequisites both provide return values for a (possibly not-yet-implemented) function and ensure that it is being called as expected.

### Metaconstants
[Metaconstants](https://github.com/marick/Midje/wiki/Metaconstants) are my favorite feature of Midje. They are an elegant solution to what would require extensive use of mock objects in RSpec. For example:

    (fact
      (sum-scores [..game1.. ..game2..]) => 10
      (provided
        (score ..game1..) => 3
        (score ..game2..) => 7))

This states that `sum-scores` will be passing `..game1..` and
`..game2..` through to the score function and then summing the results
to get its result. The beauty is that the implementation of `sum-scores`
does not care what sort of values `..game1..` and `..game2..` are and, thanks to metaconstants, neither does the test.

### Midje Mode for Emacs
If you are an Emacs user, it is worth installing [Midje mode](https://github.com/marick/Midje/wiki/Midje-mode). It gives you the ability to send individual facts to the browser and see the results as comments added to the buffer above the fact. This is a big time saver compared to running the tests through, e.g., the Leiningen Midje plugin and makes the red/green/refactor cycle very quick.

As an added bonus, it also updates the Clojure mode indentation rules so that facts format as shown in the above examples.

## Getting Midje
Ready to give Midje a try? The [source code](https://github.com/marick/Midje) is available on GitHub and the [library](http://clojars.org/midje) is available on Clojars.
