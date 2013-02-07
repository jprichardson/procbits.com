<!--
author: JP
publish: Sat Oct 29 2011 21:06:33 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2011/10/29/a-node-js-experiment-thinking-asynchronously-recursion-calculate-file-size-directory/
tags: CoffeeScript, Node.js
slug: 2011/10/29/a-node-js-experiment-thinking-asynchronously-recursion-calculate-file-size-directory
-->

A Node.js Experiment: Thinking Asynchronously, Using Recursion to Calculate the Total File Size in a Directory
==============================================================================================================

I recently picked up Node.js/CoffeeScript; I figured that since
JavaScript can run on about every modern computing device, it's about
time that I accept JavaScript instead of side-stepping it by using dying
technologies such as GWT and Silverlight.

I've always felt that the best way to learn a new language/platform is
to start by writing a simple program that solves a simple problem.

My problem involved traversing the filesystem and performing some tasks.
For the sake of this blog post and for the sake of your attention span,
the problem can be reduced to a simple algorithm that computes the total
space that a directory and its contents use.

Let's start by creating a simple synchronous version:

```ruby
fs = require('fs')
path = require('path')

du = (dir) ->
  total = 0
  try 
    stat = fs.lstatSync(dir)
    if stat.isFile()
      total += stat.size
    else if stat.isDirectory()
      files = fs.readdirSync(dir)
      for file in files
        total += du(path.join(dir, file))
  catch e
  
  total

DIR = '/'
total_bytes = du(DIR)
total_kb = total_bytes / 1024.0
total_mb = total_kb / 1024.0

console.log("#{DIR}: #{total_mb.toFixed(3)} MB")
```

This code works fine and as expected. It displays the total size of your
entire directory in MiB. Ya, I know, I wrote "MB".

But... we are using Node.js here. The asynchronous nature should be
embraced. Let's rewrite this algorithm in an asynchronous form.

```ruby
fs = require('fs')
path = require('path')

duAsync = (dir, cb) ->
  total = 0
  fs.lstat dir, (err, stat) ->
    if err then return
    if stat.isFile()
      total += stat.size
    else if stat.isDirectory()
      fs.readdir dir, (err, files) ->
        if err then return
        for file in files
          duAsync path.join(dir,file), cb
    cb(null,total)

DIR = '/'
duAsync DIR, (err, total_bytes) ->
  total_kb = total_bytes / 1000.0
  total_mb = total_kb / 1000.0

  console.log("#{DIR}: #{total_mb.toFixed(3)} MB")
```

Hmm, this doesn't output the correct values. I'm not passing the totals
up the callback chain.

Also, from here on out, I'm only going to show the algorithm.

Let's take advantage of closures and modify this a bit. If we could
remove the recursion, that may simplify things a bit.

```ruby
duAsync2 = (dir,cb) ->
  total = 0
  files = []
  all_files.push(dir)

  while all_files.length > 0
    current_dir = files.pop
    fs.lstat current_dir, (err,stat) ->
      if err then return
      if stat.isFile()
        total += stat.size
      else if stat.isDirectory()
        fs.readdir current_dir, (err,files) ->
          if err then return
          for file in files
            all_files.push(path.join(current_dir, file))
      cb(null,total)
```

On the surface, this looks fairly simple. We have removed the recursive
aspect to simplify it a bit. The code in the while block will always see
'total' so we don't run into the same problem as the last
implementation.

One major problem though, this doesn't work. This exits almost right
away. Ah yes... we are doing an asynchronous implementation. The
all\_files array is empty by the time the while loop goes to the second
iteration.

Maybe recursion is unavoidable? Let's still leverage closures though.

This version is very similar to the last, I've just managed to use
recursion within a function. The 'again' function is called recursively.

```ruby
duAsync3 = (dir,cb) ->
  total = 0

  again = (current_dir) ->
    fs.lstat current_dir, (err, stat) ->
      if err then return
      if stat.isFile()
        total += stat.size
      else if stat.isDirectory()
        fs.readdir current_dir, (err,files) ->
          if err then return
          for file in files
            again(path.join(current_dir, file))
      cb(null, total)

  again(dir)
```

It works! Consider this: what if you only want the results at the very
end? That is, you only want the callback to occur once, and at the
end... then what do you do?

This was a dilemma that I faced for a bit. For this particular problem,
it might not really matter much. Especially considering that this is a
console utility. However, I considered figuring this out, a right of
passage as a Node.js/JavaScript noob. So I didn't want to use any
utilities such as [Async.js](https://github.com/caolan/async),
[Seq](https://github.com/substack/node-seq), etc.

I started doing research, fortunately I stumbled upon two great
articles:

1.  ["Asynchronous JavaScript: The Tale of
    Harry"](http://bjouhier.wordpress.com/2011/01/09/asynchronous-javascript-the-tale-of-harry/)
2.  [Currying the Callback the Essence of
    Futures](http://bjouhier.wordpress.com/2011/04/04/currying-the-callback-or-the-essence-of-futures/)

The first article seemed to have almost an identical problem. Except,
that the author didn't impose the additional constraint of only
executing the callback upon the finished. The solution in that article
works as expected, but seems a bit more complex than necessary.

I kept researching. Found an article [Deriving the Y-Combinator in 7
Easy Steps
(JavaScript)](http://igstan.ro/posts/2010-12-01-deriving-the-y-combinator-in-7-easy-steps.html).
My mind was exploding learning some of these functional programming
concepts!

But, I still wasn't closer to a solution. I finally made my way into
\#node.js on freenode (IRC). Fortunately,
[AvianFlu](https://github.com/avianflu) was able to lend me a tip. He
suggested the following:

-   Create three variables: started, finished, running
-   At the beginning of the callback, increment started and running.
-   At the end, decrement running and increment finished.
-   When (started === finished) && (running === 0) You should be done.

I experimented with this for awhile. Sometimes, it felt that I was
close. But it never quite worked. Then I thought about it a bit more and
kept the concept of a 'running' variable and added a variable to denote
the number of files left to process.

```ruby
duAsync4 = (dir,cb) ->
  total = 0
  file_counter = 1 #starts at one because of the initial directory
  async_running = 0

  again = (current_dir) ->
    fs.lstat current_dir, (err, stat) ->
      if err then file_counter--; return
      if stat.isFile()
        file_counter--
        total += stat.size
      else if stat.isDirectory()
        file_counter--
        async_running++
        fs.readdir current_dir, (err,files) ->
          async_running--
          if err then return #console.log err.message
          file_counter += files.length
          for file in files
            again path.join(current_dir, file)
      else
        file_counter--
      if file_counter is 0 and async_running is 0
        cb(null, total)

  again dir
```

This works. What's important to note is that there are many ways to
solve problems using Node.js. On my Quad-Core MBP 8 GB Ram, this is
almost **twice as fast** as the synchronous version!

Try it out and let me know your results. Also, can you think of any
other ways to solve this problem?

Do you use Git? If so, checkout [Gitpilot](http://gitpilot.com) to make
using Git thoughtless.

Follow me on Twitter: [@jprichardson](http://twitter.com/jprichardson)
and read my blog on entrepreneurship: [Techneur](http://techneur.com).

-JP Richardson
