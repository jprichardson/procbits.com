<!--
author: JP Richardson
publish: Fri Oct 21 2011 20:28:24 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2011/10/21/a-for-loop-conversion-from-javascript-to-coffeescript/
tags: JavaScript
slug: 2011/10/21/a-for-loop-conversion-from-javascript-to-coffeescript
title: A for-loop conversion from JavaScript to CoffeeScript
-->



I was stumbling along StackOverflow when I ran into this question:

[How can I convert a Javascript for-loop to
Coffee?](http://stackoverflow.com/questions/7337351/how-can-i-convert-a-javascript-for-loop-to-coffeescript)
With this example:

```javascript
for (i = 0; i < 10; i++) {
    doStuff();
}
```

The answer: (I've since edited and updated the Stackoverflow answer)

```ruby
doStuff() for i in [0 .. 9]
```

http://jashkenas.github.com/coffee-script/\#loops

This answer works for this contrived case.

What happens when you have a case like this:

```javascript
for (var x = 0; x < myArray.length; ++x)
  alert(x);
```

What's the answer smarty pants? If you answered:

```ruby
alert(i) for i in [0 .. myArray.length-1]
```

You get a pass if myArray has anything in it. What will happen if
myArray is an empty (not null) array? Go ahead and execute it. Go here
to the [CoffeeScript page](http://jashkenas.github.com/coffee-script/),
and paste this code in the "Try CoffeeScript", I'll wait.

```ruby
myArray = []
alert(i) for i in [0 .. myArray.length-1]
```

What's that? You say it returned "0" and "-1"???

    The secret is in the ".." which implies "=>" or "<=" 
    and the "..." implies "&lt" or "&gt".

So this is probably what you expect:

```ruby
myArray = []
alert(i) for i in [0 ... myArray.length]
```

It's too bad the [loops
section](http://jashkenas.github.com/coffee-script/#loops) at this time
doesn't mention this.

CoffeeScript is a great tool. Here is another tool that allows [you to
convert JavaScript to CoffeeScript](http://js2coffee.org/). Try our
examples here and see what the tool generates. Hint: it isn't always
optimized.

Do you use Git? If so, checkout [Gitpilot](http://gitpilot.com) to make
using Git thoughtless.

Follow me on Twitter: [@jprichardson](http://twitter.com/jprichardson)
and read my blog on entrepreneurship: [Techneur](http://techneur.com).

-JP Richardson
