<!--
author: JP Richardson
publish: Thu Jun 28 2012 13:23:54 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2012/06/28/nextflow-sane-coffeescript-flow-control/
tags: CoffeeScript, Node.js
slug: 2012/06/28/nextflow-sane-coffeescript-flow-control
-->

NextFlow: Sane CoffeeScript Flow Control
========================================

Take a look at the most prominent JavaScript control flow libraries:
[Async.js](https://github.com/caolan/async),
[Step](https://github.com/creationix/step),
[Seq](https://github.com/substack/node-seq). If you were to use these
libraries in CoffeeScript, your code would be an ugly mess.

**Async.js / CoffeeScript**

```ruby
async = require('async')

async.series(
  (->
    #first function
  ),
  (->
    #second function
  )
)
```

**Step / CoffeeScript**

```ruby
Step = require('step')

Step(
  (->
    #first function
  ),
  (->
    #second function
  )
)
```

**Seq / CoffeeScript**

```ruby
Seq = require('seq')

Seq().seq(->
  #first function
).seq(->
  #second function
)
```

Yuck. If you're programming in JavaScript, all of them are very usable
solutions. Also, to be fair, they do a lot more than NextFlow. But
NextFlow looks much nicer with CoffeeScript programs.

**How to Install:**

`npm install --production nextflow`

Can be used in the browser too.

Execute sequentially, calling the \`next()\` function:

```ruby
next = require('nextflow')

vals = []
x = 0

flow =
  1: ->
    vals.push(1)
    @next()
  2: ->
    vals.push(2)
    x = Math.random()
    @next(x)
  3: (num) ->
    vals.push(num)
    @next()
  4: ->
    vals.push(4)
    @next()
  5: ->
    console.log vals[0] #is 1
    console.log vals[1] #is 2
    console.log vals[2] #is x
    console.log vals[3] #is 4

next(flow)
```

Call functions by the label:

```ruby
vals = []
x = 0

flow =
  a1: ->
    vals.push(1)
    @a2()
  a2: ->
    vals.push(2)
    x = Math.random()
    @a3(x)
  a3: (num) ->
    vals.push(num)
    @a4()
  a4: ->
    vals.push(4)
    @a5()
  a5: ->
    console.log vals[0] #is 1
    console.log vals[1] #is 2
    console.log vals[2] #is x
    console.log vals[3] #is 4

next(flow)
```

Call either \`next()\` or call the label:

```ruby
vals = []
x = 0
y = 0

flow =
  a1: ->
    vals.push(1)
    @a2()
  a2: ->
    vals.push(2)
    x = Math.random()
    @a3(x)
  a3: (num) ->
    vals.push(num)
    y = Math.random()
    @next(y)
  a4: (num) ->
    vals.push(num)
    @a5()
  a5: ->
    console.log vals[0] #is 1
    console.log vals[1] #is 2
    console.log vals[2] #is x
    console.log vals[3] #is y

next(flow)
```

[NextFlow on Github](https://github.com/jprichardson/node-nextflow)

Checkout [Gitpilot](http://gitpilot.com), to become more productive with
Git.

Follow me on Twitter: [@jprichardson](http://twitter.com/jprichardson)

-JP
