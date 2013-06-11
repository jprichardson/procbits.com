<!--
author: JP Richardson
publish: Thu Jan 19 2012 19:04:01 GMT-0600 (CST)
status: publish
type: post
link: https://procbits.wordpress.com/2012/01/19/comparing-two-javascript-objects/
tags: CoffeeScript, JavaScript
slug: 2012/01/19/comparing-two-javascript-objects
title: Comparing Two Javascript Objects
-->



Recently, I was faced with a problem where I needed to compare two
Javascript objects. My initial strategy was to convert them to JSON and
compare the JSON strings.

Sort of like this:

```javascript
var a = JSON.stringify(person1);//'{"firstName":"JP","lastName":"Richardson"}'
var b = JSON.stringify(person2);//'{"firstName":"JP","lastName":"Richardson"}'

assert(a === b);
```

Simple enough, right?

Not so fast. I encountered a case like this:

```javascript
var a = JSON.stringify(person1);//'{"firstName":"JP","lastName":"Richardson"}'
var b = JSON.stringify(person2);//'{"lastName":"Richardson","firstName":"JP"}'

assert(a === b);
```

The data is the same, but the string is different. Fortunately,
Stackoverflow had a nice [Javascript object comparison
algorithm](http://stackoverflow.com/questions/1068834/object-comparison-in-javascript)
to dump into my app.

```javascript
Object.prototype.equals = function(x)
{
  var p;
  for(p in this) {
      if(typeof(x[p])=='undefined') {return false;}
  }

  for(p in this) {
      if (this[p]) {
          switch(typeof(this[p])) {
              case 'object':
                  if (!this[p].equals(x[p])) { return false; } break;
              case 'function':
                  if (typeof(x[p])=='undefined' ||
                      (p != 'equals' && this[p].toString() != x[p].toString()))
                      return false;
                  break;
              default:
                  if (this[p] != x[p]) { return false; }
          }
      } else {
          if (x[p])
              return false;
      }
  }

  for(p in x) {
      if(typeof(this[p])=='undefined') {return false;}
  }

  return true;
}
```

Test passed. I eventually hit a situation where I had some code with an
Object that had a Person prototype and some data that came from JSON.
Kinda like this:

```javascript
var person1 = new Person('JP', 'Richardson');
var person2 = JSON.parse('{"firstName":"JP","lastName":"Richardson"}');

//deepEquals is code snippet above ^
person1.deepEquals(person2); // <--- THIS FAILS
```

I only cared about comparing the data. The methods associated with the
object (Prototype) didn't matter. Let's modify the above algorithm. I
use CoffeeScript. Here's the modification:

```ruby
Object::jsonEquals = (x) ->
  #we do this because two objects may have the same data fields and data but different prototypes
  x1 = JSON.parse(JSON.stringify(this))
  x2 = JSON.parse(JSON.stringify(x))

  p = null
  for p of x1
    return false if typeof (x2[p]) is 'undefined'
  for p of x1
    if x1[p]
      switch typeof (x1[p])
        when 'object'
          return false unless x1[p].jsonEquals(x2[p])
        when 'function'
          return false if typeof (x2[p]) is 'undefined' or (p isnt 'equals' and x1[p].toString() isnt x2[p].toString())
        else
          return false  unless x1[p] is x2[p]
    else
      return false if x2[p]
  for p of x2
    return false if typeof (x1[p]) is 'undefined'
  true
```

This causes the situation like I described above to pass. Essentially
convert to JSON to remove the prototype. I suppose you could make this
more efficient my just manually setting the prototype to Object before
doing the comparison, but oh well this works for the time being.

Do you use Git? If so, checkout [Gitpilot](http://gitpilot.com) to make
project management and collaborating on projects seamless.

Follow me on Twitter: [@jprichardson](http://twitter.com/jprichardson)
and read my blog on entrepreneurship: [Techneur](http://techneur.com).

-JP Richardson
