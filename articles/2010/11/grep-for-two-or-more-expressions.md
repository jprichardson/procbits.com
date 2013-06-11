<!--
author: JP Richardson
publish: Tue Nov 09 2010 14:09:22 GMT-0600 (CST)
status: publish
type: post
link: https://procbits.wordpress.com/2010/11/09/grep-for-two-or-more-expressions/
tags: Linux
slug: 2010/11/09/grep-for-two-or-more-expressions
title: Grep for Two or More Expressions
-->



Unfortunately a database of mine crashed. Fortunately, I have log files
with the data. I need the timestamp and the data.

The lines in the log files look something like this:
` ####### collect $TIMESTAMP ######### *GARBAGE *GARBAGE *GARBAGE $ID:=$DATASET *GARBAGE`

It's been years since I've done anything more than a simple grep. I
wasn't even sure which [grep I needed... egrep? fgrep?
grep?](http://www.unix.com/unix-desktop-dummies-questions-answers/39670-difference-grep-egrep-fgrep.html)

I can use this regex for the timestamp line:

```bash

I can use this regex for the dataset line:

```bash

But how can I put them together? It's probably of no surpise that I can
use the '|' operator like so:

```bash

Are you a [Git](http://gitpilot.com) user? Let me help you make project
management with Git simple. Checkout [Gitpilot](http://gitpilot.com).

That's it! Follow me on Twitter
[@jprichardson](http://twitter.com/jprichardson) and read my [blog on
entrepreneurship](http://techneur.com).

-JP
