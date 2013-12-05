<!--
author: JP Richardson
publish: Thu May 24 2012 18:19:20 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2012/05/24/thinking-asynchronously-in-coffeescriptjavascript-loops-and-callbacks/
tags: CoffeeScript, JavaScript
slug: 2012/05/24/thinking-asynchronously-in-coffeescriptjavascript-loops-and-callbacks
title: Thinking Asynchronously in CoffeeScript/JavaScript: Loops and Callbacks
-->



Awhile back, I wrote about my new experience in learning Node.js: [A
Node.js Experiment: Thinking Asynchronously, Using Recursion to
Calculate the Total File Size in a
Directory](http://procbits.com/2011/10/29/a-node-js-experiment-thinking-asynchronously-recursion-calculate-file-size-directory/).

Consider this snippet of code:

```javascript
var names = ['JP', 'Chris', 'Leslie'];
for (var i = 0; i < names.length; ++i){
  var name = names[i];
  setTimeout(function(){
    alert(name);              
  },10);
}​
```

Equivalent CoffeeScript:

```ruby
names = ['JP', 'Chris', 'Leslie']
for name in names
  setTimeout(->
    alert(name)
  ,10)
```

Click
[here](http://coffeescript.org/#try:names%20%3D%20%5B'JP'%2C%20'Chris'%2C%20'Leslie'%5D%0Afor%20name%20in%20names%0A%20%20setTimeout(-%3E%0A%20%20%20%20alert(name)%0A%20%20%2C10))
to run it. If you guessed that the loop would alert "Leslie" three
times, then you'd be correct.

The problem is, that before the callback executes, the loop has
completed. Thus callback always has the last value.

How do you solve this problem? You wrap the callback in a closure that
executes immediately.

JavaScript:

```javascript
var names = ['JP', 'Chris', 'Leslie'];
for (var i = 0; i < names.length; ++i){
  var name = names[i];
  (function(name){
    setTimeout(function(){
      alert(name);              
    },10);
  })(name);
}​
```

CoffeeScript:

```ruby
names = ['JP', 'Chris', 'Leslie']
for name in names
  do (name) ->
    setTimeout(->
      alert(name)
    ,10)
```

Click
[here](http://coffeescript.org/#try:names%20%3D%20%5B'JP'%2C%20'Chris'%2C%20'Leslie'%5D%0Afor%20name%20in%20names%0A%20%20do%20(name)%20-%3E%0A%20%20%20%20setTimeout(-%3E%0A%20%20%20%20%20%20alert(name)%0A%20%20%20%20%2C10))
to run it.

These solutions execute the block of code in a parallel manner. Using
the alert's are not a good indication in showing this behavior. However,
if you were opening files, all of them would be opened approximately
(not exactly) at the same time.

What if you wanted to perform the action in the callback in a serial
manner?

Using the previous simple example, it'd look like this:

JavaScript:

```javascript
var names = ['JP', 'Chris', 'Leslie'];
loop = function(i){
    setTimeout(function(){
      alert(names[i]);
      if (i < names.length - 1)
        loop(i + 1);       
    },10);
}
loop(0);
```

CoffeeScript:

```ruby
names = ['JP', 'Chris', 'Leslie'];
doloop = (i) ->
  setTimeout(->
    alert(names[i])
    if i < names.length - 1
      doloop(i + 1)       
  ,10);
doloop(0)
```

[Run
it.](http://coffeescript.org/#try:names%20%3D%20%5B'JP'%2C%20'Chris'%2C%20'Leslie'%5D%3B%0Adoloop%20%3D%20(i)%20-%3E%0A%20%20setTimeout(-%3E%0A%20%20%20%20alert(names%5Bi%5D)%0A%20%20%20%20if%20i%20%3C%20names.length%20-%201%0A%20%20%20%20%20%20doloop(i%20%2B%201)%20%20%20%20%20%20%20%0A%20%20%2C10)%3B%0Adoloop(0))

If you were doing file processing in the loop, it would be executed
sequentially.

Hopefully this helps you to better understand asynchronous design of
algorithms in JavaScript.

**Update:** I forgot about the forEach function that exists in Node.js
and most modern browsers. This function pretty much solves the problem.

Here's the JavaScript code:

```javascript
var names = ['JP', 'Chris', 'Leslie'];
names.forEach(function(name){
  setTimeout(function(){
    alert(name);              
  },10);
}​);
```

Much cleaner. Thanks to [smog\_alado
[Reddit]](http://www.reddit.com/r/programming/comments/u34ed/thinking_asynchronously_in_coffeescriptjavascript/c4ryifw)
for the reminder.


